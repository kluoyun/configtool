<template>
	<b-container>
		<b-card v-if="template.standalone" header="Network Settings">
			<b-form-checkbox v-model="networkEnabled" v-preset.left="preset.network.enabled" title="Check this to enable networking features (M552 S1)">Enable Network via Ethernet or ESP8266</b-form-checkbox>
			<div v-show="networkEnabled" class="pl-4">
				<b-form-row class="mt-3">
					<b-col>
						<b-form-group :label="$t('network.password')">
							<b-form-input v-model.trim="password" v-b-tooltip.hover :title="$t('network.passwordDescription')" maxlength="64" placeholder="reprap" type="text"></b-form-input>
						</b-form-group>
					</b-col>

					<template v-if="board.hasESP && template.firmware >= 3">
						<b-col cols="auto">
							<b-form-group label="Your WiFi Network Name:">
								<b-form-input v-model.trim="ssid" v-b-tooltip.hover title="Name of the SSID to connect to. Alternatively, you can connect manually via M587" maxlength="32" placeholder="configure manually" type="text"></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.wifiPassword')">
								<b-form-input v-model.trim="ssidPassword" v-b-tooltip.hover :title="$t('network.wifiPasswordDescription')" maxlength="64" placeholder="none" type="text"></b-form-input>
							</b-form-group>
						</b-col>
					</template>
				</b-form-row>
				<b-form-row class="mt-3">
					<template v-if="board.hasESP && template.firmware >= 3">
						<b-col cols="auto">
							<b-form-group label="espDataReadyPin:">
								<b-form-input v-model.trim="espDataReadyPin" v-preset="preset.network.espDataReadyPin" v-b-tooltip.hover title="This is the pin to be used in board.txt for 8266wifi.espDataReadyPin" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group label="lpcTfrReadyPin:">
								<b-form-input v-model.trim="lpcTfrReadyPin" v-preset="preset.network.lpcTfrReadyPin" v-b-tooltip.hover title="This is the pin to be used in board.txt for 8266wifi.lpcTfrReadyPin" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group label="espResetPin:">
								<b-form-input v-model.trim="espResetPin" v-preset="preset.network.espResetPin" v-b-tooltip.hover title="This is the pin to be used in board.txt for 8266wifi.espResetPin" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
					</template>
				</b-form-row>
				<b-checkbox v-if="board.hasESP && template.firmware" v-model="espRXTX" v-preset.left="preset.network.espRXTX" title="Use RX/TX to update ESP8266 via DWC">Use RX/TX to update ESP8266 via DWC</b-checkbox>
				<b-form-row v-if="espRXTX" class="mt-3">
					<b-col cols="auto">
						<b-form-group label="serialRxPin:">
							<b-form-input v-model.trim="serialRxPin" v-preset="preset.network.serialRxPin" v-b-tooltip.hover title="This is the pin to be used in board.txt for 8266wifi.serialRxPin" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
					<b-col cols="auto">
						<b-form-group label="serialTxPin:">
							<b-form-input v-model.trim="serialTxPin" v-preset="preset.network.serialTxPin" v-b-tooltip.hover title="This is the pin to be used in board.txt for 8266wifi.serialTxPin" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
				</b-form-row>
			
					<b-col v-if="board.hasEthernet">
						<b-form-group :label="$t('network.mac')">
							<b-form-input v-model.trim="macAddress" v-preset="preset.network.mac_address" :title="$t('network.macDescription')" :formatter="formatMAC" :state="isValidMAC(template.network.mac_address)" maxlength="17" placeholder="automatically generated" type="text"></b-form-input>
						</b-form-group>
					</b-col>
				

				<b-checkbox v-model="dhcp" v-preset.left="preset.network.dhcp" :title="$t('network.dhcpDescription')">{{$t('network.dhcp')}}</b-checkbox>
				<b-form-row v-show="!dhcp" class="pl-4 mt-3">
					<b-col>
						<b-form-group :label="$t('network.ip')">
							<b-form-input v-model.trim="ipAddress" v-preset="preset.network.ip" :title="$t('network.ipDescription')" :state="isValidIP(this.template.network.ip)" maxlength="15" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
					<b-col>
						<b-form-group :label="$t('network.subnet')">
							<b-form-input v-model.trim="netmask" v-preset="preset.network.netmask" :title="$t('network.subnetDescription')" :state="isValidNetmask(this.template.network.netmask)" maxlength="15" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
					<b-col>
						<b-form-group :label="$t('network.gateway')">
							<b-form-input v-model.trim="gateway" v-preset="preset.network.ip" :title="$t('network.gatewayDescription')" :state="isValidIP(this.template.network.gateway)" maxlength="15" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
				</b-form-row>

				<b-checkbox v-model="http" v-preset.left="preset.network.protocols.http" title="Enable HyperText Transmission Protocol to provide access to the web interface" class="mt-3">Enable HTTP (required for the web interface)</b-checkbox>
				<!--b-checkbox v-model="ftp" v-preset.left="preset.network.protocols.ftp" title="Enable File Transmission Protocol. Be aware that RepRapFirmware only supports one concurrent connection!">Enable FTP</b-checkbox>
				<b-checkbox v-model="telnet" v-preset.left="preset.network.protocols.telnet" title="Enable Telnet. Be aware that RepRapFirmware only supports one concurrent connection!">Enable Telnet</b-checkbox-->

			</div>
		</b-card>
	</b-container>
</template>

<script>
'use strict';

import { mapState } from 'vuex'
import { mapFields } from 'vuex-map-fields'

export default {
	computed: {
		...mapState(['board', 'preset', 'template']),
		...mapFields({
			networkEnabled: 'template.network.enabled',
			password: 'template.network.password',
			ssid: 'template.network.ssid',
			ssidPassword: 'template.network.ssid_password',
			macAddress: 'template.network.mac_address',
			dhcp: 'template.network.dhcp',
			ipAddress: 'template.network.ip',
			netmask: 'template.network.netmask',
			gateway: 'template.network.gateway',
			http: 'template.network.protocols.http',
			ftp: 'template.network.protocols.ftp',
			telnet: 'template.network.protocols.telnet',
			espDataReadyPin: 'board.espDataReadyPin',
			lpcTfrReadyPin: 'board.lpcTfrReadyPin',
			espResetPin: 'board.espResetPin',
			espRXTX: 'template.network.espRXTX',
			serialRxPin: 'board.serialRxPin',
			serialTxPin: 'board.serialTxPin'
		})
	},
	methods: {
		formatMAC(value, event) {
			return value.toUpperCase()
		},
		isValidIP(ipAddress) {
			let matches = /^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})$/.exec(ipAddress);
			if (!matches) {
				return false;
			}
			return (matches[1] < 255) && (matches[2] < 255) && (matches[3] < 255) && (matches[4] < 255);
		},
		isValidNetmask(netmask) {
			let matches = /^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})$/.exec(netmask);
			if (!matches) {
				return false;
			}
			return (matches[1] < 256) && (matches[2] < 256) && (matches[3] < 256) && (matches[4] < 256);
		},
		isValidMAC(macAddress) {
			return (macAddress === '') || (/^[\dA-F][\dA-F]:[\dA-F][\dA-F]:[\dA-F][\dA-F]:[\dA-F][\dA-F]:[\dA-F][\dA-F]:[\dA-F][\dA-F]$/.test(macAddress));
		}
	}
}
</script>
