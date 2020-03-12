<template>
    <div>
        <div v-show="category == true"><b-form-input id='findInput' v-model="codeNumber"></b-form-input></div>
        <div v-show="category == true">
            <div id="scannerContainer" class="scannerContainer"></div>
        </div>
    </div>
</template>

<script>
import Quagga from 'quagga';
export default {
    name: 'BarcodeReader',
    props: {
        category: Boolean,
    },
    updated() {
        this.showLiveCamera();
        
    },
    beforeUpdate(){
        this.test();
    },
    methods: {
        test: function(){
            console.log('REREREDRBEBDE')
        },
        showLiveCamera: async function(){
            await Quagga.init({
                inputStream: {
                    name: "Live",
                    type: "LiveStream",
                    target: document.getElementById('scannerContainer'),
                    constraints: {
                        width: 1024,
                        height: 768,
                        facingMode: "environment",
                    },
                },
                decoder: {
                    readers: [
                      "code_93_reader"
                    ],
                    //multiple: true,
                },

            }, function (err) {
                if (err) {
                    console.log(err);
                    return
                }

                console.log("Initialization finished. Ready to start");
                Quagga.start();

            });

            Quagga.onProcessed(function (result) {
                var drawingCtx = Quagga.canvas.ctx.overlay,
                drawingCanvas = Quagga.canvas.dom.overlay;
                drawingCanvas.style.display = 'none';

                if (result) {
                    if (result.boxes) {
                        drawingCtx.clearRect(0, 0, parseInt(drawingCanvas.getAttribute("width")), parseInt(drawingCanvas.getAttribute("height")));
                        result.boxes.filter(function (box) {
                            return box !== result.box;
                        }).forEach(function (box) {
                            Quagga.ImageDebug.drawPath(box, { x: 0, y: 1 }, drawingCtx, { color: "green", lineWidth: 2 });
                        });
                    }

                    if (result.box) {
                        Quagga.ImageDebug.drawPath(result.box, { x: 0, y: 1 }, drawingCtx, { color: "#00F", lineWidth: 2 });
                    }

                    if (result.codeResult && result.codeResult.code) {
                        Quagga.ImageDebug.drawPath(result.line, { x: 'x', y: 'y' }, drawingCtx, { color: 'red', lineWidth: 3 });
                    }
                }
            });


            Quagga.onDetected(function (result) {
              if(result){
                if(result.codeResult) {
                  this.codeNumber = result.codeResult.code;
                  const findInput = document.getElementById('findInput');
                  findInput.value = this.codeNumber;
                }
                else if(Array.isArray(result)){
                  for(let codeNumber of result){
                    console.log("resultssss ", codeNumber);
                    this.codeNumber = result.codeResult.code;
                    }
                } else {
                    console.log("not detected", result);
                    this.codeNumber = result.codeResult.code;
                }
              }
            });
        },
    },
    data: function() {
        return {
            codeNumber: '',
        }
    }
}
</script>

<style lang="scss" scoped>
    .scannerContainer {
        .drawingBuffer {
            display: none;
        }
    }
</style>