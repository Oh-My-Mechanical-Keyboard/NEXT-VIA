<template>
  <div class="keyboard-layout">
    <input id="file" type="file" accept=".json" @change="importDefinition" />
    <div class="keyboard">
      <div class="keyboard-name">
        <h2> {{ kbName }} </h2>
      </div>
      <div class="keyboard-wrap">
        <div class="keyboard-keys" :style="{ width: `${layout.width * unit}px`, height: `${layout.height * unit}px` }">
          <div v-for="(key, i) in layout.keys" :key="`${i}`" :class="['key', key.color]" :style="{
            left: `${key.x * unit + 2}px`,
            top: `${key.y * unit + 2}px`,
            width: `${key.w * unit - 4}px`,
            height: `${key.h * unit - 4}px`,
            '--w': `${key.w * unit - 4}px`,
            // '--x2': key.extra && `${key.x2 * unit}px`,
            // '--y2': key.extra && `${key.y2 * unit}px`,
            // '--w2': key.extra && `${key.w2 * unit - 4}px`,
            // '--h2': key.extra && `${key.h2 * unit - 4}px`,
          }" :data-extra="key.extra">
            <!-- <span v-for="(label, i) in key.labels" :key="i" class="label">{{ label }}</span> -->
            <span class="label">{{ key.color }}</span>
          </div>
        </div>
      </div>
      <br>
      <label>{{ $t('ui.keycode') }}:</label>
      <span class="hint">
        <a href="https://docs.qmk.fm/#/keycodes" :title="$t('ui.keycodesRef')" target="_blank" rel="noopener">{{
            $t('ui.keycodesRef')
        }}</a>
      </span>
    </div>
    <div class="keycodes">
      <keycode-bar></keycode-bar>
    </div>
  </div>
</template>

<script>
import kleZ65ble from './kle-jsons/z65ble/65.json'
import kbdef from '#/controller/klecfg2kbctlcfg'
import KeycodeBar from '../KeycodeBar/index.vue'

export default {
  name: 'keyboardLayout',
  components: {
    KeycodeBar
  },
  data() {
    return {
      unit: 50
    }
  },
  computed: {
    kbName() {
      return this.selectedKBDefinition.name
    },
    layout() {
      return this.selectedKBDefinition.layouts
    },
    selectedKBDefinition() {
      return kbdef.keyboardDefinition2KBCTLDefinition(kleZ65ble)
    }
  },
  methods: {
    importDefinition(ev) {
      const file = ev.target.files[0]
      const reader = new FileReader()
      console.log(file)
      console.log(file.path)
      reader.onload = async () => {
        let res;
        try {
          res = JSON.parse(reader.result.toString());
          console.log(res)
        } catch (err) {
          console.log(`${err.name}: ${err.message}`);
        }
      };
      reader.readAsBinaryString(file);
    }
  }
}
</script>

<style scoped>
.keyboard-layout {
  background-color: #333;
  padding: 20px;
}

.keyboard-name {
  text-align: center;
  color: #fff;
  margin: auto;
}

.keyboard-wrap {
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #000;
  border-radius: 7px;
  display: inline-block;
  background-color: #111;
}

.keyboard-keys {
  font-size: 12px;
  overflow: hidden;
  position: relative;
}

.key {
  position: absolute;
  z-index: 10;
  border-radius: 7px;
  background-color: #333;
  color: #fff;
  text-align: center;
  /* overflow: hidden; */
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.key.encoder_r {
  border-radius: var(--w);
}

.key.encoder_l {
  border-radius: var(--w);
}

.key[data-extra]:after {
  content: '';
  position: absolute;
  top: var(--y2);
  left: var(--x2);
  width: var(--w2);
  height: var(--h2);
  z-index: -5;
  display: block;
  border-radius: 7px;
  background-color: #333;
  box-sizing: border-box;
}

.key.active,
.key.active[data-extra]:after {
  background-color: #aaa;
  color: #333;
}

.label {
  /* height: 16px; */
  line-height: 16px;
  display: block;
  min-width: 1px;
  min-height: 16px;
}
</style>
