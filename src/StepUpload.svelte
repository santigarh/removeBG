<script lang="ts">
  import Dropzone from 'dropzone'
  import 'dropzone/dist/dropzone.css'
  import { onMount } from 'svelte'
  import { ImageStatus } from './types.d'
  import { imageStatus, modfiedImage, originalImage } from './store'
  import { Cloudinary } from '@cloudinary/url-gen'
  import {
    backgroundRemoval,
    vignette,
    negate,
    blackwhite,
  } from '@cloudinary/url-gen/actions/effect'
  //dropShadow
  const cloudinary = new Cloudinary({
    cloud: {
      cloudName: 'dugjarzoe',
    },
    url: {
      secure: true,
    },
  })

  onMount(() => {
    const dropzone = new Dropzone('#dropzone', {
      uploadMultiple: false,
      acceptedFiles: '.jpg,.png,.webp',
      maxFiles: 1,
    })

    dropzone.on('sending', (file, xhr, formData) => {
      imageStatus.set(ImageStatus.UPLOADING)

      formData.append('upload_preset', 'ml_default')
      formData.append('timestamp', Date.now() / 1000)
      formData.append('apikey', 241815566972144)
    })

    dropzone.on('success', (flie, response) => {
      const { secure_url: url, public_id: publicId } = response

      const imageWithoutBackground = cloudinary
        .image(publicId)
        .effect(vignette(30))

      imageStatus.set(ImageStatus.DONE)
      modfiedImage.set(imageWithoutBackground.toURL())
      originalImage.set(url)
    })

    dropzone.on('error', (flie, response) => {
      console.log({ response })
    })
  })
</script>

<form
  id="dropzone"
  class="shadow-2xl border-dashed border-2 border-gray-300 rounden-lg aspect-video w-full flex items-center justify-center flex-col"
  action="https://api.cloudinary.com/v1_1/dugjarzoe/image/upload"
>
  {#if $imageStatus === ImageStatus.READY}
    <button
      class="font-bold pointer-events-none bg-blue-700 rounded-full text-bold text-white text-xl px-6 py-4"
    >
      Upload FIles
    </button>
    <strong class="text-lg mt-4 text-gray-900">or drop a file</strong>
  {/if}

  {#if $imageStatus === ImageStatus.UPLOADING}
    <strong class="text-lg mt-4 text-gray-900">Uploading files</strong>
  {/if}
</form>
