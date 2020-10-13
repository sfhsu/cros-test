<template>
    <div id="cros-test" class="pr-5 pl-5">
        <div class="row d-flex justify-content-center">
            <div class="col mb-3">
                <div class="input-group flex-nowrap">
                    <span class="input-group-prepend">
                        <span class="input-group-text" id="url-prepend">URL</span>
                    </span>
                    <input type="text" class="form-control" id="webapi" name="webapi" v-model="webapiUrl" aria-describedby="url-prepend">
                </div>
            </div>
        </div>
        <div class="row d-flex justify-content-center">
            <div class="col mb-3">
                <div class="custom-file">
                    <input type="file" class="custom-file-input" id="customFile" name="filename" @change="fileChange" accept="image/*" />
                    <label class="custom-file-label d-flex justify-content-start" id="display-file-name" for="customFile">{{ fileName }}</label>
                </div>
            </div>
        </div>
        <div class="row d-flex justify-content-center">
            <button type="button" class="btn btn-primary btn-sm" :disabled="(!file || blProcessing)" @click="getOrientation()">SEND</button>
        </div>
        <br /><br />
        <div v-if="responseText" style="font-size:24px;">{{ responseText }}</div>
        <img v-else-if="responseMedia" :src="'data:image/png;base64,'+responseMedia" @click="openNewWindow()" id="largeImage" class="preview-img" width="70%" height="70%" />
        <div v-else-if="blProcessing">
                            <div class="spinner-grow text-primary" role="status"></div>&nbsp;
                            <div class="spinner-grow text-secondary" role="status"></div>&nbsp;
                            <div class="spinner-grow text-success" role="status"></div>&nbsp;
                            <div class="spinner-grow text-danger" role="status"></div>&nbsp;
                            <div class="spinner-grow text-warning" role="status"></div>
                            <div>Processing...</div>
                        </div>
        <br /><br />
    </div>
</template>

<script>
import $ from 'jquery';
import EXIF from 'exif-js';

export default {
    name: 'cros-test',
    data: function() {
        return {
            webapiUrl: 'https://services.kneron.com.tw/age_gender_detection',
            file: '',
            fileName: 'Choose file',
            fileUrl: '',
            blProcessing: false,
            responseMedia: '',
            responseText: '',
            imgOrientation: false,
        }
    },
    methods: {
        fileChange: function(formData) {
            this.file = formData.target.files[0];
            this.fileUrl = URL.createObjectURL(this.file);
            this.fileName = this.file.name;
        },
        processing: function () {
            var self = this;
            var formData = new FormData();

            if ((false != this.imgOrientation) &&
                (undefined != this.imgOrientation)) {
                formData.append("orientation", this.imgOrientation);
            }
            formData.append("file", this.file);

            self.responseMedia = '';
            self.responseText = '';
            self.blProcessing = true;

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

                    self.blProcessing = false;
                    self.imgOrientation = false;
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

                    self.blProcessing = false;
                    self.imgOrientation = false;
                    self.responseMedia = '';
                    self.responseText = message
                }
            });
        },
        getOrientation: function() {
            var self = this;
            self.imgOrientation = false;
            EXIF.getData(self.file, function() {
                self.imgOrientation = EXIF.getTag(this, 'Orientation');
                self.processing();
            });
        },
        openNewWindow: function() {
            if (-1 != this.file.type.indexOf('image')) {
                var image = new Image();
                image.src = "data:image/jpg;base64," + this.responseMedia;

                var w = window.open("");
                w.document.write(image.outerHTML);
            } else {
                window.open(this.responseMedia);
            }
        }
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

#display-file-name {
    overflow:hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

</style>
