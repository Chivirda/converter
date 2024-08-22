<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto py-3" max-width="900">
      <v-row><v-col>
          <h1 class="text-h3">Конвертация текста</h1>
        </v-col></v-row>
      <v-row>
        <v-col>
          <v-form>
            <v-row>
              <v-col>
                <v-textarea variant="outlined" label="Сырой текст" v-model="rawText"></v-textarea>
              </v-col>
            </v-row>
            <v-row>
              <v-col><v-btn @click="convert">Конвертировать</v-btn></v-col>
            </v-row>
          </v-form>
        </v-col>
      </v-row>
      <v-row v-for="text in textArray">
        <v-col>
          <p>Название МСД: {{ `МСД_кв_${text.kvartal}_дел_${text.plot}` }}
            <v-btn density="compact" icon @click="copyToClipboard(`МСД_кв_${text.kvartal}_дел_${text.plot}`)">
              <v-icon size="x-small">mdi-content-copy</v-icon>
            </v-btn>
          </p>
          <p>Адрес: {{ `Архангельская область, Шенкурский район, Шенкурское лесничество, Ледское участковое лесничество, леса
            АО ${text.tenant}, квартал ${text.kvartal}, ${text.stratum.split(',').length > 1 ? 'выделы' :
              'выдел'} ${text.stratum}, делянка ${text.plot}` }}
            <v-btn density="compact" icon @click="copyToClipboard(`Архангельская область, Шенкурский район, Шенкурское лесничество, Ледское участковое лесничество, леса
            АО ${text.tenant}, квартал ${text.kvartal}, ${text.stratum.split(',').length > 1 ? 'выделы' :
                'выдел'} ${text.stratum}, делянка ${text.plot}`)">
              <v-icon size="x-small">mdi-content-copy</v-icon>
            </v-btn>
          </p>
          <p>Координаты: {{ text.coords }}
            <v-btn density="compact" icon @click="copyToClipboard(text.coords)">
              <v-icon size="x-small">mdi-content-copy</v-icon>
            </v-btn>
          </p>
          <p>Широта: {{ text.coords.split(', ')[0] }}
            <v-btn density="compact" icon @click="copyToClipboard(text.coords.split(', ')[0])">
              <v-icon size="x-small">mdi-content-copy</v-icon>
            </v-btn>
          </p>
          <p>Долгота: {{ text.coords.split(', ')[1] }}
            <v-btn density="compact" icon @click="copyToClipboard(text.coords.split(', ')[1])">
              <v-icon size="x-small">mdi-content-copy</v-icon>
            </v-btn>
          </p>
        </v-col>
      </v-row>
      <v-snackbar v-model="snackbar.show" :color="snackbar.color" :timeout="snackbar.timeout" :text="snackbar.text" location="right"</v-snackbar>
    </v-responsive>
  </v-container>
</template>

<script setup>
import { reactive, ref } from 'vue';

const rawText = ref('')
const textArray = reactive([])
const snackbar = reactive({
  show: false,
  text: 'Текст успешно скопирован',
  color: 'success',
  timeout: 2000
})

function copyToClipboard(text) {
  navigator.clipboard.writeText(text)
  snackbar.show = true
}

function prepareText(text) {
  if (text.endsWith('.')) {
    text = text.slice(0, -1)
  }
  const kvartal = text.match(/Кв\.\s*(\d+)/)[1]
  const stratum = text.match(/выд\.\s*([\d,\s]+)/)[1].replace(/,\s*/g, ', ')
  const plot = text.match(/дел\.\s*(\d+)/)[1]
  const coords = text.match(/координаты:\s*([\d.,\s-]+)/)[1]
  const tenant = text.includes('АО Шеговары') ? 'Шеговары' : 'Ледское'

  textArray.push({ kvartal, stratum, plot, coords, tenant })
}

function convert() {
  const textArray = rawText.value.split('\n')
  textArray.forEach(line =>
    line !== '' && line !== ' ' ? prepareText(line) : null
  )
}
</script>
