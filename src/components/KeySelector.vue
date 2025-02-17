<script setup lang="ts">
import { ref } from 'vue';
import { keyboardEventToHidCodeMap, keyCodeToKeyName, keyModifierToKeyName, LayerControlToKeyName, MouseKeycodeToKeyName, SystemCodeToKeyName } from "../apis/utils"
import { KeyCode, KeyModifier, LayerControlKeycode, MouseKeycode, SystemKeycode } from "emi-keyboard-controller"
import { SelectOption, useMessage } from 'naive-ui';

const message = useMessage();

const binding = defineModel("binding", {default : 0});

function handleKeyModifierClick(key: number | string | KeyCode) {
    var modifier = (binding.value >> 8) & 0xFF;
    if ((modifier & key as number) > 0) {
        modifier &= ~(key as number);
    }
    else {
        modifier |= key as number;
    }
    binding.value = binding.value & 0xFF | (modifier << 8);
}

function handleKeyCodeClick(key: number | string | KeyCode) {
    binding.value = binding.value & 0xFF00 | key as number;
}

function handleFullKeyCodeClick(key: number | string | KeyCode) {
    binding.value = key as number;
}

function handleLayerNumber(n: number | null) {
    binding.value = ((Number(layer_control_value.value) << 12) | (n as number) << 8 | KeyCode.LayerControl);
}

function handleUserNumber(n: number | null) {
    binding.value = ((n as number & 0xFF) << 8 | KeyCode.KeyUser);
}

function handleLayerControl(value: string, option: SelectOption) {
    binding.value = (Number(value) << 12) | layer_value.value << 8 | KeyCode.LayerControl;
}

const layer_options = Object.keys(LayerControlKeycode).slice(0,4).map((key) => {
    return {
        value: key,
        label: LayerControlToKeyName[key as unknown as LayerControlKeycode]
    };
});
const layer_value = ref(0);
const layer_control_value = ref((LayerControlKeycode.LayerMomentary as number).toString());

</script>
<template>
    <n-tabs type="segment" animated>
        <n-tab-pane name="Normal" title="Normal">
            <n-list vertical>
                <n-list-item>
                    <n-thing title="Modifiers">
                        <n-space vertical>
                            <n-button @click="() => { binding = binding & 0xFF; }">
                                Clear</n-button>
                            <n-button-group>
                                <n-button v-for="(key, index) in Object.keys(KeyModifier)
                                    .slice(1, 9)" @click="handleKeyModifierClick(key)"
                                    :type="((binding >> 8) & 0xFF & (key as unknown as number)) > 0 ? 'primary' : ''">
                                    {{ keyModifierToKeyName[key as unknown as KeyModifier] }}</n-button>
                            </n-button-group>
                        </n-space>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Event">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.NoEvent, KeyCode.ErrorUndefined + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Alphabet Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.A, KeyCode.Z + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Numbertic Keys">
                        <n-button-group>
                            <n-button v-for="(key, code) in Object.keys(KeyCode)
                                //.filter(key => isNaN(Number(key)))
                                .slice(KeyCode.Key1, KeyCode.Key0 + 1)"
                                :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                                @click="handleKeyCodeClick(key)">
                                {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                        </n-button-group>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Control Keys">
                        <n-button-group>
                            <n-button v-for="(key, code) in Object.keys(KeyCode)
                                //.filter(key => isNaN(Number(key)))
                                .slice(KeyCode.Enter, KeyCode.Tab + 1)"
                                :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                                @click="handleKeyCodeClick(key)">
                                {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                        </n-button-group>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Symbols">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.Spacebar, KeyCode.Slash + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Function Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.CapsLock, KeyCode.Pause + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Navigation Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.Insert, KeyCode.UpArrow + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Keypad">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.NumLock, KeyCode.KeypadDot + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Additional Symbols and Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.NonUsBackslash, KeyCode.KeypadEqual + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Extended Function Keys">
                        <n-button-group>
                            <n-button v-for="(key, code) in Object.keys(KeyCode)
                                //.filter(key => isNaN(Number(key)))
                                .slice(KeyCode.F13, KeyCode.F24 + 1)"
                                :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                                @click="handleKeyCodeClick(key)">
                                {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                        </n-button-group>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Media and System Control Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.Execute, KeyCode.VolumeDown + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Locking Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.LockingCapsLock, KeyCode.LockingScrollLock + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="International and Language-Specific Keys">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.KeypadComma, KeyCode.Lang9 + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Additional Commands and Editing">
                        <n-button v-for="(key, code) in Object.keys(KeyCode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(KeyCode.AlternateErase, KeyCode.ExSel + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ keyCodeToKeyName[key as unknown as KeyCode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Mouse">
                        <n-button v-for="(key, code) in Object.keys(MouseKeycode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(MouseKeycode.MouseLButton, MouseKeycode.MouseWheelDown + 1)"
                            :type="((binding & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleKeyCodeClick(key)">
                            {{ MouseKeycodeToKeyName[key as unknown as MouseKeycode] }}</n-button>
                    </n-thing>
                </n-list-item>
            </n-list>
        </n-tab-pane>
        <n-tab-pane name="Others" title="others">
            <n-list vertical>
                <n-list-item>
                    <n-thing title="Mouse">
                        <n-button v-for="(key, code) in Object.keys(MouseKeycode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(MouseKeycode.MouseLButton, MouseKeycode.MouseWheelDown + 1)"
                            :type="((binding & 0xFF) == KeyCode.MouseCollection && ((binding >> 8) & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleFullKeyCodeClick((key as unknown as number) << 8 | KeyCode.MouseCollection)">
                            {{ MouseKeycodeToKeyName[key as unknown as MouseKeycode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Layer">
                        <n-flex>
                            <n-grid :cols="4">
                                <n-gi :span="1">
                                    <n-select :options="layer_options" @update:value="handleLayerControl" v-model:value="layer_control_value" ></n-select>
                                </n-gi>
                                <n-gi :span="3">
                                    <n-input-number @update:value="handleLayerNumber" v-model:value="layer_value" max="15" min="0"></n-input-number>
                                </n-gi>
                            </n-grid>
                        </n-flex>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="System">
                        <n-button v-for="(key, code) in Object.keys(SystemKeycode)
                            //.filter(key => isNaN(Number(key)))
                            .slice(0, 10)"
                            :type="((binding & 0xFF) == KeyCode.KeySystem && ((binding >> 8) & 0xFF) == (key as unknown as number)) ? 'primary' : ''"
                            @click="handleFullKeyCodeClick((key as unknown as number) << 8 | KeyCode.KeySystem)">
                            {{ SystemCodeToKeyName[key as unknown as SystemKeycode] }}</n-button>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="User">
                        <n-flex>
                            <n-input-number @update:value="handleUserNumber" max="255" min="0"></n-input-number>
                        </n-flex>
                    </n-thing>
                </n-list-item>
                <n-list-item>
                    <n-thing title="Transparent">
                        <n-button :type="((binding & 0xFF) == KeyCode.KeyTransparent) ? 'primary' : ''"
                            @click="handleFullKeyCodeClick(KeyCode.KeyTransparent)">
                            Transparent</n-button>
                    </n-thing>
                </n-list-item>
            </n-list>
        </n-tab-pane>
    </n-tabs>
</template>

<style scoped></style>
