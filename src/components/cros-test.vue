<template>
  <div class="cros-test">
      <label for="fname">URL:</label>&nbsp;
      <input type="text" id="webapi" name="webapi" v-model="webapiUrl" /><br /><br />
      <input type="file" @change="fileChange" accept="image/*" /><br /><br />
      <input type="button" value="SEND" @click="processing()"><br /><br />
      <div v-if="responseText" style="font-size:24px;">{{ responseText }}</div>
      <img v-else-if="responseMedia" :src="'data:image/png;base64,'+responseMedia" id="largeImage" class="preview-img" width="100%" height="100%" />
  </div>
</template>

<script>
import $ from 'jquery';

export default {
    name: 'cros-test',
    data: function() {
        return {
            webapiUrl: 'https://services.kneron.com.tw/masked_face/',
            file: '',
            fileUrl: '',
            responseMedia: '',
            responseText: '',
        }
    },
    methods: {
        fileChange: function(formData) {
            this.file = formData.target.files[0];
            this.fileUrl = URL.createObjectURL(this.file);
        },
        processing: function () {
            var self = this;
            var formData = new FormData();

            formData.append("file", this.file);

            $.ajax({
                url: self.webapiUrl,
                method: "POST",
                data: formData,
                cache: false,
                processData : false,
                contentType : false,
                success: function (response) {
                    if (200 !== response.errorCode) {
                        alert("Error!"+response.errorCode);
                        return;
                    }

                    self.responseMedia = response.img;
                    self.responseText = '';
                },
                error: function(data) {
                    var message = '';

                    if (undefined !== data.responseJSON) {
                        message = data.responseJSON.errorCode + ', ' + data.responseJSON.message;
                    } else {
                        message = data.status + ', ' +data.statusText;
                    }

                    self.responseMedia = '';
                    self.responseText = message
                }
            });
        },
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#webapi {
    width: 500px;
}

.preview-img {
    object-fit: contain; /* Do not scale the image */
    object-position: center; /* Center the image within the element */
}
</style>
