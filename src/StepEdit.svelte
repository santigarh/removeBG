<script lang="ts">
  import 'two-up-element'
  import { originalImage, modfiedImage } from './store'

  let processingImage = true
  let tries = 0
  let intervalId: number

  $: {
    if (processingImage) {
      clearInterval(intervalId)
      intervalId = setInterval(() => {
        tries++
      }, 500)
    }
  }
</script>

<two-up>
  <img src={$originalImage} alt="Imagen original" />
  <img
    on:load={() => (processingImage = false)}
    on:error={() => (processingImage = true)}
    src={`${$modfiedImage}&t=${tries}`}
    alt="Imagen sin fondo"
  />
</two-up>
<a
  download
  href={$modfiedImage}
  class="block bg-blue-600 w-full text-xl text-center hover:bg-blue-800 font-bold text-white rounded-full px-4 py-2 mt-10"
  >Descargar imagen sin fondo</a
>
