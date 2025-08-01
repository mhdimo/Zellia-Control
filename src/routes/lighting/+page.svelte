<script lang="ts">
  import { KeyboardDisplayValues } from '$lib/KeyboardState.svelte';
  import { glassmorphismMode } from '$lib/DarkModeStore.svelte';
  import NewZellia80He from '$lib/NewZellia80HE.svelte';
  import { language, t } from '$lib/LanguageStore.svelte';

  // Helper function for string formatting
  const formatString = (template: string, ...args: (string | number)[]): string => {
    return template.replace(/{(\d+)}/g, (match, index) => {
      return args[index] !== undefined ? String(args[index]) : match;
    });
  };

  let currentLanguage = $state($language);

  // Subscribe to language changes
  language.subscribe(value => {
    currentLanguage = value;
  });

  let selectedEffect = $state('static');
  let brightness = $state(100); // Frontend display value (0-100)
  let speed = $state(50);
  let direction = $state('left-to-right');
  let staticColor = $state('#ff0000');
  let gradientColors = $state(['#ff0000', '#00ff00', '#0000ff']);
  let selectedLayer = $state(1);
  let perKeyMode = $state(false);
  let selectedKeys = $state(new Set());
  let keyColor = $state('#ffffff');

  let CurrentSelected = $state<[number, number] | null>(null);

  // Convert frontend brightness (0-100) to hardware brightness (0-70)
  function getHardwareBrightness(frontendBrightness: number): number {
    return Math.round((frontendBrightness / 100) * 70);
  } // Convert hardware brightness (0-70) to frontend brightness (0-100)
  function getFrontendBrightness(hardwareBrightness: number): number {
    return Math.round((hardwareBrightness / 70) * 100);
  }

  const effects = $derived([
    {
      id: 'static',
      name: t('lighting.static', currentLanguage),
      description: t('lighting.staticDesc', currentLanguage),
    },
    {
      id: 'breathing',
      name: t('lighting.breathing', currentLanguage),
      description: t('lighting.breathingDesc', currentLanguage),
    },
    {
      id: 'wave',
      name: t('lighting.wave', currentLanguage),
      description: t('lighting.waveDesc', currentLanguage),
    },
    {
      id: 'ripple',
      name: t('lighting.ripple', currentLanguage),
      description: t('lighting.rippleDesc', currentLanguage),
    },
    {
      id: 'rainbow',
      name: t('lighting.rainbow', currentLanguage),
      description: t('lighting.rainbowDesc', currentLanguage),
    },
    {
      id: 'reactive',
      name: t('lighting.reactive', currentLanguage),
      description: t('lighting.reactiveDesc', currentLanguage),
    },
    {
      id: 'spectrum',
      name: t('lighting.spectrum', currentLanguage),
      description: t('lighting.spectrumDesc', currentLanguage),
    },
    {
      id: 'gradient',
      name: t('lighting.gradient', currentLanguage),
      description: t('lighting.gradientDesc', currentLanguage),
    },
  ]);

  const directions = $derived([
    { id: 'left-to-right', name: t('lighting.leftToRight', currentLanguage) },
    { id: 'right-to-left', name: t('lighting.rightToLeft', currentLanguage) },
    { id: 'top-to-bottom', name: t('lighting.topToBottom', currentLanguage) },
    { id: 'bottom-to-top', name: t('lighting.bottomToTop', currentLanguage) },
    { id: 'center-out', name: t('lighting.centerOut', currentLanguage) },
    { id: 'outside-in', name: t('lighting.outsideIn', currentLanguage) },
  ]);

  function hexToRgb(hex: string) {
    const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
    return result
      ? {
          r: parseInt(result[1], 16),
          g: parseInt(result[2], 16),
          b: parseInt(result[3], 16),
        }
      : null;
  }

  function addGradientColor() {
    gradientColors = [...gradientColors, '#ffffff'];
  }

  function removeGradientColor(index: number) {
    gradientColors = gradientColors.filter((_, i) => i !== index);
  }

  function toggleKeySelection() {
    if (CurrentSelected) {
      const keyId = `${CurrentSelected[0]},${CurrentSelected[1]}`;
      if (selectedKeys.has(keyId)) {
        selectedKeys.delete(keyId);
      } else {
        selectedKeys.add(keyId);
      }
      selectedKeys = new Set(selectedKeys);
    }
  }

  function applyToSelectedKeys() {
    // Apply current color to selected keys
    console.log('Applying color to selected keys:', selectedKeys);
  }

  function clearKeySelection() {
    selectedKeys.clear();
    selectedKeys = new Set(selectedKeys);
  }

  function applySettings() {
    const hardwareBrightness = getHardwareBrightness(brightness);

    console.log('Applying lighting settings:', {
      effect: selectedEffect,
      brightness: hardwareBrightness, // Send hardware value (0-70)
      frontendBrightness: brightness, // For reference (0-100)
      speed,
      direction,
      staticColor,
      gradientColors,
      layer: selectedLayer,
    });
  }
</script>

<NewZellia80He
  onClick={(x, y, event) => {
    console.log(`Key clicked at (${x}, ${y})`, event);
  }}
  bind:currentSelectedKey={CurrentSelected}
>
  {#snippet body(x, y)}
    <div
      class="hover:scale-90 transition-all duration-300 h-14 border bg-gray-50 border-gray-400 dark:bg-black borde dark:border-gray-700 data-[selected=true]:bg-gray-500 data-[selected=true]:border-gray-700 data-[selected=true]:border-4 rounded-lg flex flex-col items-center justify-center hover:cursor-pointer gap-1 font-sans text-white"
    ></div>{/snippet}
</NewZellia80He>
<div
  class="rounded-2xl shadow p-8 mt-2 mb-4 grow bg-gray-50 dark:bg-black border border-gray-200 dark:border-gray-600 text-black dark:text-white h-full flex flex-col {$glassmorphismMode
    ? 'glassmorphism-card'
    : ''}"
>
  <div class="flex items-center justify-between -mt-4 mb-4">
    <h2 class="text-2xl font-bold text-black dark:text-white">
      {t('lighting.title', currentLanguage)}
    </h2>
    <div class="flex gap-2">
      <button
        class="px-4 py-2 rounded transition-colors text-white {$glassmorphismMode
          ? 'glassmorphism-button'
          : ''}"
        style="background-color: var(--theme-color-primary);"
        onclick={applySettings}
      >
        {t('lighting.applySettings', currentLanguage)}
      </button>
      <button
        class="bg-gray-800 dark:bg-gray-800 hover:bg-gray-700 dark:hover:bg-gray-700 text-white border border-white dark:border-white px-4 py-2 rounded transition-colors {$glassmorphismMode
          ? 'glassmorphism-button'
          : ''}"
        onclick={() => (perKeyMode = !perKeyMode)}
      >
        {perKeyMode
          ? t('lighting.exitPerKeyMode', currentLanguage)
          : t('lighting.perKeyMode', currentLanguage)}
      </button>
    </div>
  </div>

  <div
    class="rounded-xl shadow p-6 flex flex-col lg:flex-row gap-6 flex-1 {$glassmorphismMode
      ? 'glassmorphism-card'
      : ''}"
  >
    <!-- Effects Panel -->
    <div class="flex-1 min-w-[300px]">
      <div class="grid grid-cols-2 gap-3 mb-6">
        {#each effects as effect}
          <button
            class="p-3 rounded-lg border-2 text-left transition-all duration-200"
            style={selectedEffect === effect.id
              ? `border-color: var(--theme-color-primary); background-color: color-mix(in srgb, var(--theme-color-primary) 15%, var(--tw-prose-body));`
              : `border-color: ${'#e5e5e5 dark:#4b5563'}; background-color: transparent;`}
            onclick={() => (selectedEffect = effect.id)}
          >
            <div class="font-medium text-black dark:text-white">
              {effect.name}
            </div>
            <div class="text-sm text-gray-600 dark:text-gray-300">
              {effect.description}
            </div>
          </button>
        {/each}
      </div>

      <!-- Global Controls -->
      <div class="space-y-4">
        <!-- Brightness -->
        <div>
          <div class="flex justify-between text-sm text-gray-600 dark:text-gray-300 mb-2">
            <span>{t('lighting.brightness', currentLanguage)}</span>
            <span>{brightness}%</span>
          </div>
          <input
            type="range"
            min="0"
            max="100"
            bind:value={brightness}
            class="w-full h-2 rounded-full bg-gray-300 dark:bg-gray-700 appearance-none slider-thumb"
          />
        </div>

        <!-- Speed (for animated effects) -->
        {#if ['breathing', 'wave', 'rainbow', 'spectrum'].includes(selectedEffect)}
          <div>
            <div class="flex justify-between text-sm text-gray-600 dark:text-gray-300 mb-2">
              <span>{t('lighting.speed', currentLanguage)}</span>
              <span>{speed}%</span>
            </div>
            <input
              type="range"
              min="1"
              max="100"
              bind:value={speed}
              class="w-full h-2 rounded-full bg-gray-300 dark:bg-gray-700 appearance-none slider-thumb"
            />
          </div>
        {/if}

        <!-- Direction (for directional effects) -->
        {#if ['wave', 'gradient', 'spectrum'].includes(selectedEffect)}
          <div>
            <label class="block text-sm text-gray-600 dark:text-gray-300 mb-2"
              >{t('lighting.direction', currentLanguage)}</label
            >
            <select
              bind:value={direction}
              class="w-full p-2 border border-gray-300 dark:border-white bg-white dark:bg-black text-black dark:text-white rounded-lg"
            >
              {#each directions as dir}
                <option value={dir.id}>{dir.name}</option>
              {/each}
            </select>
          </div>
        {/if}
      </div>
    </div>

    <!-- Divider -->
    <div class="hidden lg:block w-px bg-gray-200 dark:bg-white"></div>

    <!-- Color Controls Panel -->
    <div class="flex-1 min-w-[300px]">
      <h3 class="text-lg font-medium mb-4 text-black dark:text-white">
        {t('lighting.colorSettings', currentLanguage)}
      </h3>
      {#if perKeyMode}
        <!-- Per-Key Color Mode -->
        <div class="space-y-4">
          <div
            class="p-3 rounded-lg border dark:bg-black bg-white dark:border-white border-[#e5e5e5] {$glassmorphismMode
              ? 'glassmorphism-button'
              : ''}"
          >
            <div class="font-medium text-black dark:text-white">
              {t('lighting.perKeyModeActive', currentLanguage)}
            </div>
            <div class="text-sm" style="color: var(--theme-color-primary);">
              {t('lighting.clickKeysToSelect', currentLanguage)}
            </div>
          </div>

          <div>
            <label class="block text-sm text-gray-600 dark:text-gray-300 mb-2"
              >{t('lighting.keyColor', currentLanguage)}</label
            >
            <div class="flex gap-2">
              <input
                type="color"
                bind:value={keyColor}
                class="w-12 h-10 rounded border border-gray-300 dark:border-white"
              />
              <input
                type="text"
                bind:value={keyColor}
                class="flex-1 p-2 border border-gray-300 dark:border-white bg-white dark:bg-black text-black dark:text-white rounded-lg font-mono"
                placeholder="#ffffff"
              />
            </div>
          </div>

          <div class="flex gap-2">
            <button
              class="flex-1 px-3 py-2 rounded transition-colors text-white bg-primary {$glassmorphismMode
                ? 'glassmorphism-button'
                : ''}"
              style="background-color: var(--theme-color-primary);"
              onclick={toggleKeySelection}
              disabled={!CurrentSelected}
            >
              {CurrentSelected && selectedKeys.has(`${CurrentSelected[0]},${CurrentSelected[1]}`)
                ? t('lighting.deselectKey', currentLanguage)
                : t('lighting.selectKey', currentLanguage)}
            </button>
            <button
              class="{$glassmorphismMode
                ? 'glassmorphism-button'
                : ''} bg-gray-600 hover:bg-gray-700 text-white dark:bg-gray-800 border border-white px-3 py-2 rounded transition-colors"
              onclick={clearKeySelection}
            >
              {t('lighting.clear', currentLanguage)} ({selectedKeys.size})
            </button>
          </div>
          <button
            class="w-full px-3 py-2 rounded transition-colors text-white {$glassmorphismMode
              ? 'glassmorphism-button'
              : ''}"
            style="background-color: var(--theme-color-primary);"
            onclick={applyToSelectedKeys}
            disabled={selectedKeys.size === 0}
          >
            {t('lighting.applyToSelectedKeys', currentLanguage)}
          </button>
        </div>
      {:else}
        <!-- Effect-based Color Controls -->
        {#if selectedEffect === 'static' || selectedEffect === 'breathing' || selectedEffect === 'reactive'}
          <div>
            <spam class="block text-sm text-gray-600 dark:text-gray-300 mb-2"
              >{t('lighting.color', currentLanguage)}</spam
            >
            <div class="flex gap-2">
              <input
                type="color"
                bind:value={staticColor}
                class="w-12 h-10 rounded border border-gray-300 dark:border-white"
              />
              <input
                type="text"
                bind:value={staticColor}
                class="flex-1 p-2 border border-gray-300 dark:border-white bg-white dark:bg-black text-black dark:text-white rounded-lg font-mono"
                placeholder="#ff0000"
              />
            </div>
            {#if hexToRgb(staticColor)}
              {@const rgb = hexToRgb(staticColor)}
              {#if rgb}
                <div class="text-xs text-gray-600 dark:text-gray-400">
                  {formatString(t('lighting.rgbValues', currentLanguage), rgb.r, rgb.g, rgb.b)}
                </div>
              {/if}
            {/if}
          </div>
        {:else if selectedEffect === 'gradient'}
          <div>
            <label class="block text-sm text-gray-600 dark:text-gray-300 mb-2"
              >{t('lighting.gradientColors', currentLanguage)}</label
            >
            <div class="space-y-2">
              {#each gradientColors as color, index}
                <div class="flex gap-2 items-center">
                  <input
                    type="color"
                    bind:value={gradientColors[index]}
                    class="w-10 h-8 rounded border border-gray-300 dark:border-white"
                  />
                  <input
                    type="text"
                    bind:value={gradientColors[index]}
                    class="flex-1 p-1 border border-gray-300 dark:border-white bg-white dark:bg-black text-black dark:text-white rounded text-sm font-mono"
                  />
                  {#if gradientColors.length > 2}
                    <button
                      class="w-8 h-8 bg-red-500 text-white rounded hover:bg-red-600 transition-colors text-sm"
                      onclick={() => removeGradientColor(index)}>×</button
                    >
                  {/if}
                </div>
              {/each}
            </div>
            <button
              class="mt-2 w-full bg-gray-200 hover:bg-gray-300 text-gray-700 dark:bg-gray-800 dark:hover:bg-gray-700 dark:text-white border border-white px-3 py-2 rounded transition-colors"
              onclick={addGradientColor}
            >
              {t('lighting.addColor', currentLanguage)}
            </button>
          </div>
        {:else}
          <div class="p-4 bg-gray-50 dark:bg-gray-800 rounded-lg text-center">
            <div class="text-gray-600 dark:text-gray-300">
              {selectedEffect === 'rainbow' || selectedEffect === 'spectrum'
                ? t('lighting.automaticColors', currentLanguage)
                : t('lighting.noColorSettings', currentLanguage)}
            </div>
          </div>
        {/if}
      {/if}
      <!-- Preview -->
      <div class="mt-6">
        <label class="block text-sm text-gray-600 dark:text-gray-300 mb-2"
          >{t('lighting.preview', currentLanguage)}</label
        >
        <div
          class="h-16 rounded-lg border {'border-gray-300 dark:border-white'} flex items-center justify-center"
          style:background={selectedEffect === 'static'
            ? staticColor
            : selectedEffect === 'gradient'
              ? `linear-gradient(90deg, ${gradientColors.join(', ')})`
              : '#f3f4f6 dark:#374151'}
        >
          {#if selectedEffect === 'rainbow' || selectedEffect === 'spectrum'}
            <div class="text-sm text-gray-600 dark:text-gray-300">
              {t('lighting.animatedEffect', currentLanguage)}
            </div>
          {:else if selectedEffect === 'reactive' || selectedEffect === 'ripple'}
            <div class="text-sm text-gray-600 dark:text-gray-300">
              {t('lighting.reactiveEffect', currentLanguage)}
            </div>
          {/if}
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .slider-thumb {
    appearance: none;
  }
  .slider-thumb::-webkit-slider-thumb {
    appearance: none;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: var(--theme-color-primary);
    cursor: pointer;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
  }
  .slider-thumb::-moz-range-thumb {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: var(--theme-color-primary);
    cursor: pointer;
    border: none;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
  }
</style>
