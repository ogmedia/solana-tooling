<template>
    <div class="column">
        <div class="box">
            <div class="field block">
                <label class="label">Select an image</label>
                <div class="control">
                    <div class="file">
                      <label class="file-label">
                        <input class="file-input" type="file" name="test-image" @change="imageChange">
                        <span class="file-cta">
                          <span class="file-icon">
                            <i class="fas fa-upload"></i>
                          </span>
                          <span class="file-label">
                            Choose a file…
                          </span>
                        </span>
                      </label>
                    </div>
                </div>
            </div>

            <div v-if="imageSrc" class="block">
                <div class="columns">
                    <CanvasComponent :image="canvasImageObj" domId="canvas-r" routine="red"></CanvasComponent>
                    <CanvasComponent :image="canvasImageObj" domId="canvas-g" routine="green"></CanvasComponent>
                    <CanvasComponent :image="canvasImageObj" domId="canvas-b" routine="blue"></CanvasComponent>
                    <CanvasComponent :image="canvasImageObj" domId="canvas-bw" routine="grey"></CanvasComponent>
                    <CanvasComponent :image="canvasImageObj" domId="canvas-inv" routine="invert"></CanvasComponent>
                </div>
                <figure class="image is-128x128">
                    <h4>Original</h4>
                    <img id="image-preview" ref="image-preview" :src="image" />
                </figure>
            </div>
        </div>
    </div>
</template>

<script>
import CanvasComponent from "./canvas.vue";

export default {
    "components": {
        CanvasComponent
    },
    data() {
        return {
            "imageSrc": null,
            "canvasImage": null,
        }
    },
    "computed": {
        image () {
            return this.imageSrc;
        },
        canvasImageObj () {
            return this.canvasImage;
        }
    },
    "methods": {
        imageChange({ target }){
            const vm = this;
            const reader = new FileReader();
            reader.onload =  function(){
                vm.imageSrc = reader.result;
                vm.canvasImage = reader.result;
             }
            reader.readAsDataURL(target.files[0]);
        }
    }
}
</script>