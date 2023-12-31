<!--ElementProperties.vue-->
<script setup lang="ts">
import { useElementsStore } from '@/stores/elements'
import { ref, watchEffect } from 'vue'
import DropDown from '@/components/common/DropDown.vue'
import IconArrowDown from '@/components/icons/IconArrowDown.vue'
import tagToTextType, { tags } from '@/utils/textType'
import { fonts, fontSizes } from '@/utils/font'
import IconBold from '@/components/icons/IconBold.vue'
import IconItalic from '@/components/icons/IconItalic.vue'
import IconUnderline from '@/components/icons/IconUnderline.vue'
import IconAlignJustified from '@/components/icons/IconAlignJustified.vue'
import IconAlignLeft from '@/components/icons/IconAlignLeft.vue'
import IconAlignCenter from '@/components/icons/IconAlignCenter.vue'
import IconAlignRight from '@/components/icons/IconAlignRight.vue'
import viewType from '@/utils/viewType'

const store = useElementsStore()
const selectedElement = ref(
  store.selectedElement ? store.selectedElement : { defaultProperties: {} }
) as any
watchEffect(() => {
  if (store.selectedElement !== null) {
    let selectedItemIndex = store.canvasElements.findIndex((el) => {
      return el.id === store.selectedElement?.id
    })

    if (selectedItemIndex !== -1) {
      store.canvasElements[selectedItemIndex].defaultProperties = {
        ...store.selectedElement.defaultProperties
      }
      if (store.currentPage) {
        store.updatePage(store.currentPage)
      }
    }
  }
})

watchEffect(() => {
  if (store.selectedElement) {
    selectedElement.value = { ...store.selectedElement }
  }
})
function updateProperties(key: string, value: any) {
  if (selectedElement.value) {
    selectedElement.value.defaultProperties[key] = value

    let selectedItemIndex = store.canvasElements.findIndex((el) => {
      return el.id == store.selectedElement?.id
    })

    if (selectedItemIndex !== -1) {
      store.canvasElements[selectedItemIndex].defaultProperties[key] = value

      if (store.currentPage) {
        const name = store.currentPage.name || 'default'

        store.currentPage = { ...store.currentPage, name }
        store.updatePage(store.currentPage)
      }
    }
  }
}
const clearSelectedElement = () => {
  store.selectedElement = null
}
</script>
<template>
  <div class="flex h-full flex-col justify-between">
    <div class="flex flex-col">
      <h2
        class="text-center w-full flex h-10 items-center justify-center border-b border-main text-sm"
      >
        {{ selectedElement.type }}
      </h2>

      <!-- Text Properties -->
      <div v-if="selectedElement.type === 'Text'" class="flex flex-col gap-4 px-5 py-3">
        <DropDown
          :value="selectedElement.defaultProperties.type"
          @update:value="updateProperties('type', $event)"
          trigger-class-name="h-10 border-gray-200 border w-full flex justify-between items-center px-3 rounded text-sm"
          :close-when-click-content="true"
        >
          <template #trigger
            ><span> {{ tagToTextType(store.selectedElement?.defaultProperties.type) }}</span>
            <IconArrowDown />
          </template>
          <template #content>
            <div class="flex flex-col w-full bg-white rounded-md border-main shadow-base divide-y">
              <button
                v-for="(type, index) in tags"
                :key="index"
                :value="type"
                @click="updateProperties('type', type)"
                class="text-center py-1 hover:bg-gray-50"
              >
                {{ type.replace(/^h/, 'Heading ').replace(/^p$/, 'Paragraph') }}
              </button>
            </div>
          </template>
        </DropDown>
        <textarea
          v-model="selectedElement.defaultProperties.text"
          class="w-full decoration-none text-sm p-1.5 placeholder:font-light focus:outline-none focus:ring-0 focus:border-gray-200 border resize-none rounded h-[160px]"
          placeholder="Text"
        ></textarea>
        <span class="text-gray-400 text-xs -mb-2">Font</span>
        <DropDown
          :value="selectedElement.defaultProperties.font"
          @update:value="updateProperties('font', $event)"
          trigger-class-name="h-10 border-gray-200 border w-full flex justify-between items-center px-3 rounded text-sm"
          :close-when-click-content="true"
        >
          <template #trigger
            ><span> {{ store.selectedElement?.defaultProperties.font }}</span>
            <IconArrowDown />
          </template>
          <template #content>
            <div class="flex flex-col w-full bg-white rounded-md border-main shadow-base divide-y">
              <button
                v-for="(font, index) in fonts"
                :key="index"
                :value="font"
                @click="updateProperties('font', font)"
                class="text-center py-1 hover:bg-gray-50"
              >
                {{ font }}
              </button>
            </div>
          </template>
        </DropDown>
        <span class="text-gray-400 text-xs -mb-2">Font Size</span>
        <div class="flex w-full justify-between">
          <button
            v-for="(fontSize, index) in fontSizes"
            :key="index"
            :value="fontSize"
            @click="updateProperties('fontSize', fontSize)"
            class="text-center hover:bg-gray-50 w-8 h-8 flex items-center justify-center rounded"
            :class="
              fontSize === selectedElement.defaultProperties.fontSize
                ? 'text-gray-950'
                : 'text-gray-300'
            "
            :style="{
              fontSize: fontSize
            }"
          >
            aA
          </button>
        </div>

        <span class="text-gray-400 text-xs -mb-2">Specification</span>
        <div class="flex w-full justify-between">
          <button
            class="hover:bg-gray-50 w-8 h-8 flex items-center justify-center rounded"
            @click="
              updateProperties(
                'fontWeight',
                store.selectedElement?.defaultProperties.fontWeight === 'bold' ? 'normal' : 'bold'
              )
            "
          >
            <IconBold
              :style="{
                fontWeight: selectedElement.defaultProperties.fontWeight
              }"
              :class="{
                'fill-gray-950': store.selectedElement?.defaultProperties.fontWeight === 'bold',
                'fill-gray-300': store.selectedElement?.defaultProperties.fontWeight !== 'bold'
              }"
            />
          </button>
          <button
            class="hover:bg-gray-50 w-8 h-8 flex items-center justify-center rounded"
            @click="
              updateProperties(
                'textDecoration',
                store.selectedElement?.defaultProperties.textDecoration === 'underline'
                  ? 'none'
                  : 'underline'
              )
            "
          >
            <IconUnderline
              :style="{
                textDecoration: selectedElement.defaultProperties.textDecoration
              }"
              :class="{
                'fill-gray-950':
                  store.selectedElement?.defaultProperties.textDecoration === 'underline',
                'fill-gray-300':
                  store.selectedElement?.defaultProperties.textDecoration !== 'underline'
              }"
            />
          </button>
          <button
            class="hover:bg-gray-50 w-8 h-8 flex items-center justify-center rounded"
            @click="
              updateProperties(
                'fontStyle',
                store.selectedElement?.defaultProperties.fontStyle === 'italic'
                  ? 'normal'
                  : 'italic'
              )
            "
          >
            <IconItalic
              :style="{
                fontStyle: selectedElement.defaultProperties.fontStyle
              }"
              :class="{
                'fill-gray-950': store.selectedElement?.defaultProperties.fontStyle === 'italic',
                'fill-gray-300': store.selectedElement?.defaultProperties.fontStyle !== 'italic'
              }"
            />
          </button>
          <button
            v-for="(align, index) in [
              { alignment: 'left', icon: IconAlignLeft },
              { alignment: 'center', icon: IconAlignCenter },
              { alignment: 'right', icon: IconAlignRight },
              { alignment: 'justify', icon: IconAlignJustified }
            ]"
            :key="index"
            class="hover:bg-gray-50 w-8 h-8 flex items-center justify-center rounded"
            @click="updateProperties('textAlign', align.alignment)"
          >
            <component
              :is="align.icon"
              :style="{
                textAlign: selectedElement.defaultProperties.textAlign
              }"
              :class="{
                'fill-gray-950':
                  store.selectedElement?.defaultProperties.textAlign === align.alignment,
                'fill-gray-300':
                  store.selectedElement?.defaultProperties.textAlign !== align.alignment
              }"
            />
          </button>
        </div>
        <span class="text-gray-400 text-xs -mb-2">Link</span>
        <input
          :value="selectedElement.defaultProperties.link"
          @input="updateProperties('link', ($event.target as HTMLInputElement)?.value)"
          class="w-full h-10 text-sm p-1.5 focus:outline-none focus:ring-0 focus:border-gray-200 border resize-none rounded placeholder-gray-300 placeholder:font-light"
          placeholder="https://"
        />
        <span class="text-gray-400 text-xs -mb-2">View</span>
        <DropDown
          :value="viewType(selectedElement.defaultProperties.view)"
          @update:value="updateProperties('font', $event)"
          trigger-class-name="h-10 border-gray-200 border w-full flex justify-between items-center px-3 rounded text-sm"
          :close-when-click-content="true"
        >
          <template #trigger
            ><span> {{ viewType(store.selectedElement?.defaultProperties.view) }}</span>
            <IconArrowDown />
          </template>
          <template #content>
            <div class="flex flex-col w-full bg-white rounded-md border-main shadow-base divide-y">
              <button
                v-for="(view, index) in ['desktop', 'mobile', 'responsive']"
                :key="index"
                :value="view"
                @click="updateProperties('view', view)"
                class="text-center py-1 hover:bg-gray-50"
              >
                {{ viewType(view) }}
              </button>
            </div>
          </template>
        </DropDown>
      </div>
      <!-- Button Properties -->
      <div v-else-if="selectedElement.type === 'Button'" class="flex flex-col gap-4 px-5 py-3">
        <DropDown
          :value="selectedElement.defaultProperties.action"
          @update:value="updateProperties('action', $event)"
          trigger-class-name="h-10 border-gray-200 border w-full flex justify-between items-center px-3 rounded text-sm"
          :close-when-click-content="true"
        >
          <template #trigger
            ><span>
              {{
                store.selectedElement?.defaultProperties.action === ''
                  ? 'Redirect to Next Page'
                  : store.selectedElement?.defaultProperties.action
              }}</span
            >
            <IconArrowDown />
          </template>
          <template #content>
            <div class="flex flex-col w-full bg-white rounded-md border-main shadow-base divide-y">
              <button
                :value="''"
                @click="updateProperties('action', '')"
                class="text-center py-1 hover:bg-gray-50"
              >
                Redirect to Next Page
              </button>
              <button
                v-for="(page, index) in store.pages"
                :key="index"
                :value="page.name"
                @click="updateProperties('action', page.name)"
                class="text-center py-1 hover:bg-gray-50"
              >
                {{ page.name }}
              </button>
            </div>
          </template>
        </DropDown>
        <input
          :value="selectedElement.defaultProperties.text"
          @input="updateProperties('text', ($event.target as HTMLInputElement)?.value)"
          class="w-full h-10 text-sm p-1.5 focus:outline-none focus:ring-0 focus:border-gray-200 border resize-none rounded placeholder-gray-300 placeholder:italic placeholder:font-light"
          placeholder="Text Here"
        />
      </div>

      <!-- Input Properties -->
      <div v-else-if="selectedElement.type === 'Input'" class="flex flex-col gap-4">
        <label>
          Placeholder:
          <input
            :value="selectedElement.defaultProperties.placeholder"
            @input="updateProperties('placeholder', ($event.target as HTMLInputElement)?.value)"
          />
        </label>
      </div>

      <!-- Block Properties -->
      <div v-else-if="selectedElement.type === 'Block'" class="flex flex-col gap-4 px-5 py-3">
        <span class="text-gray-400 text-xs -mb-2">Block Style</span>
        <div class="flex flex-col gap-2.5">
          <button
            class="w-full h-[60px] bg-gray-50 border border-gray-200 rounded flex items-center p-3.5"
            @click="updateProperties('children', 1)"
            :style="{
              backgroundColor:
                selectedElement.defaultProperties.children === 1 ? '#E5EDF9' : 'white'
            }"
          >
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
          </button>
          <button
            class="w-full h-[60px] bg-gray-50 border border-gray-200 rounded flex items-center p-3.5 gap-3.5"
            @click="updateProperties('children', 2)"
            :style="{
              backgroundColor:
                selectedElement.defaultProperties.children === 2 ? '#E5EDF9' : 'white'
            }"
          >
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
          </button>
          <button
            class="w-full h-[60px] bg-gray-50 border border-gray-200 rounded flex items-center p-3.5 gap-3.5"
            @click="updateProperties('children', 3)"
            :style="{
              backgroundColor:
                selectedElement.defaultProperties.children === 3 ? '#E5EDF9' : 'white'
            }"
          >
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
            <span class="w-full h-8 bg-white border border-dashed border-gray-200 rounded"></span>
          </button>
        </div>

        <span class="text-gray-400 text-xs -mb-2">Padding</span>
        <div class="flex gap-1.5">
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -left-[1px] border-l h-7 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.paddingLeft)"
              @input="
                updateProperties('paddingLeft', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px.</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -top-[1px] border-t w-12 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.paddingTop)"
              @input="
                updateProperties('paddingTop', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px.</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -right-[1px] border-r h-7 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.paddingRight)"
              @input="
                updateProperties('paddingRight', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px.</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -bottom-[1px] border-b w-12 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.paddingBottom)"
              @input="
                updateProperties('paddingBottom', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px.</span>
          </span>
        </div>
        <span class="text-gray-400 text-xs -mb-2">Margin</span>
        <div class="flex gap-1.5">
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -left-[1px] border-l h-7 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.marginLeft)"
              @input="
                updateProperties('marginLeft', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -top-[1px] border-t w-12 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.marginTop)"
              @input="
                updateProperties('marginTop', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -right-[1px] border-r h-7 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.marginRight)"
              @input="
                updateProperties('marginRight', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px</span>
          </span>
          <span class="relative flex items-center justify-center border rounded w-full h-10">
            <span class="absolute -bottom-[1px] border-b w-12 border-gray-950 rounded"></span>
            <input
              type="number"
              class="border-none outline-none text-center text-sm"
              min="0"
              max="100"
              :value="parseInt(selectedElement.defaultProperties.marginBottom)"
              @input="
                updateProperties('marginBottom', `${($event.target as HTMLInputElement)?.value}px`)
              "
            />
            <span class="text-xs text-gray-300">px</span>
          </span>
        </div>
      </div>
    </div>

    <button @click="clearSelectedElement" class="bg-black text-white w-full h-9">Done</button>
  </div>
</template>
<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
