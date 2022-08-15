<template>
  <div id="app">
    <div id="reader"></div>
    
	<div :class="{show: newFlag}" class="resultView">
	<div class="close-button">
      <button type="button" @click="newFlag = false">X</button>
    </div>
		<hr/>
      <pre>decodedResult => {{ message }}</pre>
	</div>

	<div class="resultHistory" style="display:none">
		<div
      v-for="(msg, index) in message"
      :key="index"
      style="white-space: pre-line"
    >
		
    </div>
	</div>
  </div>
</template>

<script>
import { Html5Qrcode } from "html5-qrcode";
import axios from 'axios';

export default {
  data() {
    return {
      products: [],
 newFlag: false
    };
  },
  methods: {
    creatScan() {
		const qrBoxSize = 250
 const width = window.innerWidth
          const height = window.innerHeight
          const aspectRatio = width / height
          const reverseAspectRatio = height / width

          const mobileAspectRatio = reverseAspectRatio > 1.5
            ? reverseAspectRatio + (reverseAspectRatio * 12 / 100)
            : reverseAspectRatio

          const config = {
            fps: 20, // frame per seconds for qr code scanning
            qrbox: { width: qrBoxSize, height: qrBoxSize },
            videoConstraints: {
              facingMode: 'environment',
              aspectRatio: width < 600
                ? mobileAspectRatio
                : aspectRatio,
            },
          }

      /* const html5QrcodeScanner = new Html5QrcodeScanner(
        "qr-code-full-region",
        config
      );
	
      html5QrcodeScanner.render(this.onScanSuccess); */

 const html5QrCode = new Html5Qrcode(/* element id */ "reader");
html5QrCode.start(
	{ facingMode: "environment" },
  config,
  (decodedText, decodedResult) => {

  this.newFlag = true;
      this.products.push(decodedText);
	console.log(decodedText, decodedResult)

	axios
      .get('https://world.openfoodfacts.org/api/v2/product/04963406')
      .then(response => (this.info = response))
  },
  (errorMessage) => {
	console.log(errorMessage)
    // parse error, ignore it.
  })
.catch((err) => {
	console.log(err)
  // Start failed, handle it.
});
    },
  },
  async mounted() {
    this.creatScan();
  },
};
</script>