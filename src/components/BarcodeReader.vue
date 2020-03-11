<template>
    <div>
        <div v-show="category == true">
            <video id="video" width="320" height="240" autoPlay></video>
            <!-- <div id="scanner-container"></div> -->
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
        this.showCamera();
    },
    methods: {
        showCamera: async function(){

            if(navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                navigator.mediaDevices.getUserMedia({ video: true }).then(function(stream) {
                    var video = document.getElementById('video');
                    video.srcObject = stream;
                    video.play();
                });
            }   else {
                console.log('nie dziala')
            }
        },
        showLiveCamera: async function(){
            await Quagga.init({
                inputStream: {
                    name: "Live",
                    type: "LiveStream",
                    target: document.querySelector('#scanner-container'),
                    constraints: {
                        width: 640,
                        height: 480,
                        facingMode: "environment"
                    },
                },
                decoder: {
                    readers: [
                      "code_128_reader",
                      "ean_reader",
                      "ean_8_reader",
                      "code_39_reader",
                      "code_39_vin_reader",
                      "codabar_reader",
                      "upc_reader",
                      "upc_e_reader",
                      "i2of5_reader",
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
                  console.log("result", result.codeResult.code, result);
                }
                else if(Array.isArray(result)){
                  for(let number of result){
                    console.log("resultssss ", number);
                    }
                } else {
                    console.log("not detected", result);
                }
              }
            });
        },

    },
}
</script>

<style lang="scss" scoped>
</style>