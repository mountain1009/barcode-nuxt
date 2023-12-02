<script lang="ts" setup>
import Quagga from '@ericblade/quagga2'

const code = ref('')

const readerStart = async () => {
  console.log('Start!!')

  const myQuagga = document.getElementById('my_quagga')

  if (!myQuagga) return

  await Quagga.init(
    {
      inputStream: {
        name: 'Live',
        type: 'LiveStream',
        target: myQuagga,
      },
      decoder: {
        readers: ['ean_reader'], // バーコードの種類
      },
    },
    (err) => {
      if (err) {
        if (err.message === 'Permission denied') {
          alert('カメラを許可してください。')
        }
        return
      }
      console.log('Initialization finished!!')
      Quagga.start() // バーコード検知を開始する
    },
  )

  Quagga.onProcessed((result) => {
    if (result == null) return // 未検出の場合
    if (typeof result != 'object') return
    if (result.boxes == undefined) return
    const ctx = Quagga.canvas.ctx.overlay
    const canvas = Quagga.canvas.dom.overlay
    ctx.clearRect(0, 0, parseInt(canvas.width), parseInt(canvas.height))
  })

  Quagga.onDetected((result) => {
    code.value = result.codeResult.code as string
  })
}
</script>

<template>
  <div>
    <h1>BARCODE READER</h1>
    <p>code: {{ code }}</p>
    <div id="my_quagga"></div>
    <button @click="readerStart">スタート</button>
  </div>
</template>
