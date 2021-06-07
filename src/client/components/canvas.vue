<template>
    <div class="column">
        <canvas :height="height" :width="width" :id="domId"></canvas>
    </div>
</template>

<script>
export default {
    data () {
        return {
            "ctx": null,
            "height": 32,
            "width": 32,
            "imageData": null,
        }
    },
    "props": {
        "image": String,
        "domId": String,
        "routine": String,
    },
    mounted () {
        const c = document.getElementById(this.domId);
        this.ctx = c.getContext("2d");
        console.log(this.ctx);
        this.loadImage();
    },
    updated () {
        this.loadImage();
    },
    "methods": {
        loadImage () {
            const ci = new Image();
            ci.onload = () => {
                this.ctx.drawImage(ci, 0, 0, this.width, this.height);
                this.imageData = this.ctx.getImageData(0, 0, this.width, this.height);
                switch(this.routine) {
                    case "red":
                        this.getRed();
                    break;
                    case "green":
                        this.getGreen();
                    break;
                    case "blue":
                        this.getBlue();
                    break;
                    case "grey":
                        this.getGrey();
                    break;
                    case "invert":
                        this.getInvert();
                    break;
                }
            }
            ci.src = this.image;
        },
        getRed () {
            const reds = this.ctx.createImageData(this.width, this.height);
            let i;
            for (i = 0; i < this.imageData.data.length; i += 4) {
                reds.data[i] = this.imageData.data[i];
                reds.data[i+1] = 255;
                reds.data[i+2] = 255;
                reds.data[i+3] = this.imageData.data[i+3];
            }
            this.ctx.clearRect(0, 0, this.width, this.height);
            this.ctx.putImageData(reds, 0, 0);
            this.$store.dispatch("setImageRed", this.ctx.getImageData(0, 0, this.width, this.height));
        },
        getGreen () {
            const greens = this.ctx.createImageData(this.width, this.height);
            let i;
            for (i = 0; i < this.imageData.data.length; i += 4) {
                greens.data[i] = 255;
                greens.data[i+1] = this.imageData.data[i+1];
                greens.data[i+2] = 255;
                greens.data[i+3] = this.imageData.data[i+3];
            }
            this.ctx.clearRect(0, 0, this.width, this.height);
            this.ctx.putImageData(greens, 0, 0);
            this.$store.dispatch("setImageGreen", this.ctx.getImageData(0, 0, this.width, this.height));
        },
        getBlue () {
            const blues = this.ctx.createImageData(this.width, this.height);
            let i;
            for (i = 0; i < this.imageData.data.length; i += 4) {
                blues.data[i] = 255
                blues.data[i+1] = 255;
                blues.data[i+2] = this.imageData.data[i+2];
                blues.data[i+3] = this.imageData.data[i+3];
            }
            this.ctx.clearRect(0, 0, this.width, this.height);
            this.ctx.putImageData(blues, 0, 0);
            this.$store.dispatch("setImageBlue", this.ctx.getImageData(0, 0, this.width, this.height));
        },
        getGrey () {
            const greys = this.ctx.createImageData(this.width, this.height);
            let i;
            for ( i = 0; i < this.imageData.data.length; i += 4) {
                let avg = (this.imageData.data[i] + this.imageData.data[i + 1] + this.imageData.data[i + 2]) / 3;
                greys.data[i]     = avg; // red
                greys.data[i + 1] = avg; // green
                greys.data[i + 2] = avg; // blue
                greys.data[i + 3] = this.imageData.data[i+3];
            }
            this.ctx.clearRect(0, 0, this.width, this.height);
            this.ctx.putImageData(greys, 0, 0); 
            this.$store.dispatch("setImageGrey", this.ctx.getImageData(0, 0, this.width, this.height));
        },
        getInvert () {
            const inverts = this.ctx.createImageData(this.width, this.height);
            let i;
            for (i = 0; i < this.imageData.data.length; i += 4) {
                inverts.data[i]     = 255 - this.imageData.data[i];     // red
                inverts.data[i + 1] = 255 - this.imageData.data[i + 1]; // green
                inverts.data[i + 2] = 255 - this.imageData.data[i + 2]; // blue
                inverts.data[i + 3] = this.imageData.data[i+3];
            }
            this.ctx.clearRect(0, 0, this.width, this.height);
            this.ctx.putImageData(inverts, 0, 0);
            this.$store.dispatch("setImageInvert", this.ctx.getImageData(0, 0, this.width, this.height));       
        },

    }
}
</script>