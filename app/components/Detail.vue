<template>
    <Page>
        <ActionBar title="Details">
        </ActionBar>
        <StackLayout>
            <Label :text="todoItem.name"></Label>
            <Button text="Back" @tap="onBackTap"></Button>
            <Button :text="statusText" @tap='toggle'></Button>
            <Button text="Take picture" @tap='takePicture'></Button>
            
            <!-- On affiche l'image déja existante pour la TODO -->
            <Image v-if='image==null' :src="originalUrlPicture" width="300" height="200"/>

            <Image v-if='image!=null' :src="image.src" width="300" height="200" @tap="saveImage()"/>
            <Label v-if='image!=null && !successUpload && !upload' text="Tap the picture to save"></Label>
            <!-- Affichage pendant l'upload -->
            <Label v-if='upload' text="Saving the picture..."></Label>
            <Progress v-if="upload" v-bind:value="progress" maxValue="100" color="pink"></Progress>
            
            <Label v-if='successUpload' text="Picture successfully saved"></Label>
        </StackLayout>
    </Page>
</template>

<script>
import * as camera from "nativescript-camera";
import {Image} from "tns-core-modules/ui/image";

const bghttp = require("nativescript-background-http");
const session = bghttp.session("image-upload");

export default {
    props: ['todoItem'],
    data: function() {
        return {
           image: null,
           upload:false,
           progress: 0,
           successUpload:false,
           originalUrlPicture: this.todoItem.imgUrl
        }
    },
    computed: {
        statusText: function() {
            return this.todoItem.done ? 'Done' : 'Not done';
        }
    },
    methods: {
        toggle: function() {
           this.todoItem.done = !this.todoItem.done;
        },
        onBackTap : function() {
            this.$navigateBack();
        },
        takePicture: function() {
            camera.requestPermissions()
            .then(() => {
                camera.takePicture({width: 300, height: 300, keepAspectRatio: true, saveToGallery: false})
                    .then(imageAsset => {
                        let img = new Image();
                        img.src = imageAsset;
                        this.image=img;
                    })
            })
        },
        saveImage: function() {
            const key = "92577857e3c453360d5429ac4c45e91c";
            let urlApi = "https://api.imgbb.com/1/upload?key=" + key;
            let path = this.image.src._android;

            const request = {
                url: urlApi,
                method: "POST",
                headers: {
                    "Content-type": "application/octet-stream"
                },
                description: "Uploading"
            };

            const params = [
                {
                    name: "image",
                    filename: path,
                    mimeType: "img/jpeg"
                }
            ];

            const task = session.multipartUpload(params, request);
            this.upload = true;
            task.on("progress", (e) => {
                this.progress = Math.floor(((e.currentBytes / e.totalBytes) * 100));
            });
            task.on("responded", (res) => {
                this.upload = false;
                this.successUpload = true;
                let result = JSON.parse(res.data);
                let urlImageUploaded = result.data.image.url;
                this.todoItem.imgUrl = urlImageUploaded;
            });
        }
    }
}
</script>

<style lang="scss" scoped>
    Button {
        background-color: rgb(228, 201, 201);
        margin-top:10px;
        margin-bottom:10px;
        width:60%;
        height:120px;
        font-size:14px;
        border-radius:5%;
    }

    Label {
        padding: 30px;
        font-size:20px;
        text-align: center;
    }

    ActionBar {
        background-color: rgb(228, 201, 201);
        color:black;
    }
</style>