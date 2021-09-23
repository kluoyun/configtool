<template>
	<b-container>
		<b-card v-if="template.standalone" v-show="!board.hasESP32Onboard" class="mb-3" :header="$t('network.settings')">
			<b-form-checkbox v-model="networkEnabled" :disabled="isESP32" v-preset.left="preset.network.enabled" :title="$t('network.enableDescription')">{{$t('network.enableDescriptionAlt')}}</b-form-checkbox>
			<div v-show="networkEnabled" class="pl-4">
				<b-form-row class="mt-3">
					<b-col>
						<b-form-group :label="$t('network.password')">
							<b-form-input v-model.trim="password" v-b-tooltip.hover :title="$t('network.passwordDescription')" maxlength="64" placeholder="reprap" type="text"></b-form-input>
						</b-form-group>
					</b-col>

					<template v-if="board.hasESP && template.firmware >= 3">
						<b-col cols="auto">
							<b-form-group :label="$t('network.wifiNameAlt')">
								<b-form-input v-model.trim="ssid" v-b-tooltip.hover :title="$t('network.wifiNameDescription')" maxlength="32" placeholder="configure manually" type="text"></b-form-input>
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
							<b-form-group :label="$t('network.espDataReadyPin')">
								<b-form-input v-model.trim="espDataReadyPin" v-preset="preset.network.espDataReadyPin" v-b-tooltip.hover :title="$t('network.espDataReadyPinDescription')" maxlength="4" type="text" required> </b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.lpcTfrReadyPin')">
								<b-form-input v-model.trim="lpcTfrReadyPin" v-preset="preset.network.lpcTfrReadyPin" v-b-tooltip.hover :title="$t('network.lpcTfrReadyPinDescription')" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.espResetPin')">
								<b-form-input v-model.trim="espResetPin" v-preset="preset.network.espResetPin" v-b-tooltip.hover :title="$t('network.espResetPinDescription')" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
					</template>
				</b-form-row>
				<b-checkbox v-if="board.hasESP && template.firmware" v-model="espRXTX" v-preset.left="preset.network.espRXTX" :title="$t('network.espRXTXDescription')">{{$t('network.espRXTXDescription')}}</b-checkbox>
				<b-form-row v-if="espRXTX" class="mt-3">
					<b-col cols="auto">
						<b-form-group :label="$t('network.serialRxPin')">
							<b-form-input v-model.trim="serialRxPin" v-preset="preset.network.serialRxPin" v-b-tooltip.hover :title="$t('network.serialRxPinDescription')" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
					<b-col cols="auto">
						<b-form-group :label="$t('network.serialTxPin')">
							<b-form-input v-model.trim="serialTxPin" v-preset="preset.network.serialTxPin" v-b-tooltip.hover :title="$t('network.serialTxPinDescription')" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
				</b-form-row>
			
					<b-col v-if="board.hasEthernet">
						<b-form-group :label="$t('network.mac')">
							<b-form-input v-model.trim="macAddress" v-preset="preset.network.mac_address" :title="$t('network.macDescription')" :formatter="formatMAC" :state="isValidMAC(template.network.mac_address)" maxlength="17" placeholder="automatically generated" type="text"></b-form-input>
						</b-form-group>
					</b-col>
				

				<b-checkbox v-model="dhcp" v-show="board.hasEthernet" v-preset.left="preset.network.dhcp" :title="$t('network.dhcpDescription')">{{$t('network.dhcp')}}</b-checkbox>
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

				<b-checkbox v-model="http" v-preset.left="preset.network.protocols.http" :title="$t('network.httpDescription')" class="mt-3">{{$t('network.http')}}</b-checkbox>
				<!--b-checkbox v-model="ftp" v-preset.left="preset.network.protocols.ftp" :title="$t('network.ftpDescription')">{{$t('network.ftp')}}</b-checkbox>
				<b-checkbox v-model="telnet" v-preset.left="preset.network.protocols.telnet" :title="$t('network.telnetDescription')">{{$t('network.telnet')}}</b-checkbox-->

			</div>
		</b-card>

		<b-card v-if="template.standalone && (board.hasESP32Onboard || board.esp32.esp32Supported)" :header="$t('network.settings32')">
			<b-form-checkbox v-model="networkEnabled32" :disabled="isESP8266" :title="$t('network.enableDescription')">{{$t('network.enableDescriptionAlt32')}}</b-form-checkbox>
			<div v-show="networkEnabled32" class="pl-4">
				<b-form-row class="mt-3">
					<b-col>
						<b-form-group :label="$t('network.password')">
							<b-form-input v-model.trim="password" v-b-tooltip.hover :title="$t('network.passwordDescription')" maxlength="64" placeholder="reprap" type="text"></b-form-input>
						</b-form-group>
					</b-col>

					<template v-if="board.hasESP && template.firmware >= 3">
						<b-col cols="auto">
							<b-form-group :label="$t('network.wifiNameAlt')">
								<b-form-input v-model.trim="ssid" v-b-tooltip.hover :title="$t('network.wifiNameDescription')" maxlength="32" placeholder="configure manually" type="text"></b-form-input>
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
							<b-form-group :label="$t('network.espDataReadyPin')">
								<b-form-input v-model.trim="espDataReadyPin32" v-preset="preset.network.espDataReadyPin32" v-b-tooltip.hover :title="$t('network.espDataReadyPinDescription')" maxlength="4" type="text" required> </b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.lpcTfrReadyPin')">
								<b-form-input v-model.trim="lpcTfrReadyPin32" v-preset="preset.network.lpcTfrReadyPin32" v-b-tooltip.hover :title="$t('network.lpcTfrReadyPinDescription')" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.espResetPin')">
								<b-form-input v-model.trim="espResetPin32" v-preset="preset.network.espResetPin32" v-b-tooltip.hover :title="$t('network.espResetPinDescription')" maxlength="4" type="text" required></b-form-input>
							</b-form-group>
						</b-col>
						<b-col cols="auto">
							<b-form-group :label="$t('network.csPin')">
								<b-form-input v-model.trim="wifi8266CSPin32" v-preset="preset.network.wifi8266CSPin32" v-b-tooltip.hover :title="$t('network.csPinDescription')" maxlength="4" type="text" ></b-form-input>
							</b-form-group>
						</b-col>
					</template>
				</b-form-row>
				<b-checkbox v-if="board.hasESP && template.firmware" v-model="esp32RXTX" v-preset.left="preset.network.espRXTX" :title="$t('network.espRXTXDescription')">{{$t('network.espRXTXDescription')}}</b-checkbox>
				<b-form-row v-if="esp32RXTX" class="mt-3">
					<b-col cols="auto">
						<b-form-group :label="$t('network.serialRxPin')">
							<b-form-input v-model.trim="serialRxPin" v-preset="preset.network.serialRxPin" v-b-tooltip.hover :title="$t('network.serialRxPinDescription')" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
					<b-col cols="auto">
						<b-form-group :label="$t('network.serialTxPin')">
							<b-form-input v-model.trim="serialTxPin" v-preset="preset.network.serialTxPin" v-b-tooltip.hover :title="$t('network.serialTxPinDescription')" maxlength="4" type="text" required></b-form-input>
						</b-form-group>
					</b-col>
				</b-form-row>
			
					<b-col v-if="board.hasEthernet">
						<b-form-group :label="$t('network.mac')">
							<b-form-input v-model.trim="macAddress" v-preset="preset.network.mac_address" :title="$t('network.macDescription')" :formatter="formatMAC" :state="isValidMAC(template.network.mac_address)" maxlength="17" placeholder="automatically generated" type="text"></b-form-input>
						</b-form-group>
					</b-col>
				

				<b-checkbox v-model="dhcp" v-show="board.hasEthernet" v-preset.left="preset.network.dhcp" :title="$t('network.dhcpDescription')">{{$t('network.dhcp')}}</b-checkbox>
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

				<b-checkbox v-model="http" v-preset.left="preset.network.protocols.http" :title="$t('network.httpDescription')" class="mt-3">{{$t('network.http')}}</b-checkbox>
				<!--b-checkbox v-model="ftp" v-preset.left="preset.network.protocols.ftp" :title="$t('network.ftpDescription')">{{$t('network.ftp')}}</b-checkbox>
				<b-checkbox v-model="telnet" v-preset.left="preset.network.protocols.telnet" :title="$t('network.telnetDescription')">{{$t('network.telnet')}}</b-checkbox-->

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
			networkEnabled32: 'template.network.enabled32',
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
			espDataReadyPin32: 'board.esp32.espDataReadyPin',
			lpcTfrReadyPin32: 'board.esp32.lpcTfrReadyPin',
			espResetPin32: 'board.esp32.espResetPin',
			wifi8266CSPin32: 'board.esp32.wifi8266CSPin',
			espRXTX: 'template.network.espRXTX',
			esp32RXTX: 'template.network.esp32RXTX',
			serialRxPin: 'board.serialRxPin',
			serialTxPin: 'board.serialTxPin'
		}),
		isESP8266: function(){
			return this.networkEnabled;
		},
		isESP32: function(){
			return this.networkEnabled32;
		}
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
