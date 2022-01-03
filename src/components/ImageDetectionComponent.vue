<template>
    <div>
        <LoadingComponent v-if="isLoading"></LoadingComponent>
        <header class="header-app">
            <div class="header-inline">
                <img class="img-logo" src="../assets/image/logotool.png" alt="">
                <div class="title-tools">EDGE DETECTION TOOL</div>
                <div class="icon-wrap display-flex align-item-center">
                    <div class="user-item display-flex">
                        <i class="fa fa-user icon-user"></i>
                        <span>
                        <span>Trần Hoàng Thiên</span>
                        <br>
                        <span>Nguyễn Viết Phúc</span>
                    </span>
                    </div>
                </div>
            </div>
        </header>
        <div class="main-app">
            <div class="main-group display-flex">
                <div class="options-group">
                    <div class="options-title">
                        <div class="add-title">1.Please select a photo from your device</div>
                    </div>
                    <div class="type-info-wrap">
                        <div class="add-input-img-group">
                            <input accept=".png, .jpg, .jpeg"
                                   ref="input-image" class="input-file" id="input-file" type="file"
                                   style="display: none" v-on:change="onInputImage()">
                            <label for="input-file">
                                <div class="add-img">
                                    <i class="icon-add-img">
                                        <svg viewBox="0 0 31 31" xmlns="http://www.w3.org/2000/svg">
                                            <g fill-rule="nonzero">
                                                <path d="M15 15V7.5a.5.5 0 1 1 1 0V15h7.5a.5.5 0 1 1 0 1H16v7.5a.5.5 0 1 1-1 0V16H7.5a.5.5 0 1 1 0-1H15z"></path>
                                                <path d="M15.5 31C6.94 31 0 24.06 0 15.5 0 6.94 6.94 0 15.5 0 24.06 0 31 6.94 31 15.5 31 24.06 24.06 31 15.5 31zm0-1C23.508 30 30 23.508 30 15.5S23.508 1 15.5 1 1 7.492 1 15.5 7.492 30 15.5 30z"></path>
                                            </g>
                                        </svg>
                                    </i>
                                </div>
                            </label>
                            <label class="label-avt pr-2 display-flex">
                                <span class="star-color-red">*</span>
                                Input image
                            </label>
                        </div>
                        <div class="input-img">
                            <img v-if="inputImage" class="img-result img-original"
                                :src="inputImage">
                            <div v-else class="img-result-1"></div>
                            <button class="btn-upload-file btn-confirm-detect" v-on:click="edgeDetection()">DETECT</button>
                        </div>
                    </div>
                    <div class="options-three">
                        <div class="add-title">3.Please select detect</div>
                    </div>
                </div>
                <div class="options-group">
                    <div class="">
                        <div class="options-title">
                            <div class="add-title">2.Please choose one of these options as below!</div>
                        </div>
                        <div class="display-flex btn-upload-group">
                            <div class="btn-upload-file-wrap">
                                <button :class="{active: bit24 === 'False'}"
                                        class="btn-upload-file btn-confirm-info"
                                        v-on:click="onChangeBit('False')">
                                    <span class="label-upload-file">
                                        8 Bit
                                    </span>
                                </button>
                            </div>
                            <div class="btn-upload-file-wrap">
                                <button :class="{active: bit24 === 'True'}"
                                        class="btn-upload-file btn-confirm-info"
                                        v-on:click="onChangeBit('True')">
                                    <span class="label-upload-file">
                                        24 Bit
                                    </span>
                                </button>
                            </div>
                        </div>
                        <div class="display-flex btn-upload-group">
                            <div class="btn-upload-file-wrap">
                                <button :class="{active: filterType === 'HIGH'}"
                                        class="btn-upload-file btn-confirm-info"
                                        v-on:click="onChangePass('HIGH')">
                                    <span class="label-upload-file">
                                        High pass
                                    </span>
                                </button>
                            </div>
                            <div class="btn-upload-file-wrap">
                                <button :class="{active: filterType === 'LOW'}"
                                        class="btn-upload-file btn-confirm-info"
                                        v-on:click="onChangePass('LOW')">
                                    <span class="label-upload-file">
                                        Low pass
                                    </span>
                                </button>
                            </div>
                        </div>
                        <div class="display-flex adjust-group">
                            <span class="adjust-title">Adjust</span>
                            <input v-model="scaleIn" class="filter-adjust" type="range" min="0" max="10000">
                            <label>{{scaleIn}}</label>
                        </div>
                    </div>
                </div>
            </div>
            <div class="result-group">
                <div class="img-group">
                    <div class="display-flex-between step-1-2">
                        <div class="output-image">Output Image</div>
                        <div v-for="(detectImage, index) in detectImages" :key="index"
                             class="img-result-group">
                            <div class="img-original-group">
                                <img class="img-result img-original"
                                     :src="detectImage.image">
                            </div>
                            <span class="img-result-text">{{detectImage.label}}</span>
                        </div>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
    import {Options, Vue} from "vue-class-component";
    import axios from "axios"
    import LoadingComponent from "./LoadingComponent.vue";

    @Options({
        components: {
            LoadingComponent
        },
    })

    export default class ImageDetectionComponent extends Vue {
        private inputImage: any = null;

        private red: any = null;
        private green: any = null;
        private blue: any= null;

        private isLoading: boolean = false;

        private useFilter :any = "GAUSSIAN";
        private filterType :any = null;
        private scaleIn :any = "600";
        private bit24 :any = null;

        private detectImages: any[] = [];

        onInputImage() {
            const element: any = this.$refs['input-image'];

            const fileReader = new FileReader();
            fileReader.readAsDataURL(element.files[0]);

            fileReader.onload = (e) => {
                this.inputImage = e && e.target && e.target.result;
            }
        }

        onChangePass(passType: string) {
            if (!this.inputImage){
                alert("Bạn cần chưa chọn hình ảnh! Vui lòng chọn hình ảnh trước!")
                return
            } else {
                this.filterType = passType;
            }
        }

        onChangeBit(flag: string) {
            if (!this.inputImage){
                alert("Bạn cần chưa chọn hình ảnh! Vui lòng chọn hình ảnh trước!")
                return
            } else {
                this.bit24 = flag;
            }
        }

        edgeDetection() {
            this.detectImages = [];

            if (!this.inputImage || !this.filterType || !this.bit24) {
                alert("Bạn cần chưa chọn đủ các thao tác! Vui lòng kiểm tra lại!");
                return;
            } else {
                this.isLoading = true;

                axios.post("http://edgarnguyen.com/?fbclid=IwAR15BnxNQSt3wI2w5KTK4glOZhFFeOHuCSZxJJ_v0wV4beCphoSvyQGPR40",
                    {
                        image: this.inputImage,
                        use_filter: this.useFilter,
                        filter_type: this.filterType,
                        scale_out: this.scaleIn,
                        bit_24: this.bit24
                    }).then((result) => {
                    if(this.bit24 === "False") {
                        this.detectImages = result.data.data
                    } else {
                        this.red = result.data.data.filter((image: any) => {
                            return image.label.includes(" red") || image.label.includes(" Red") || image.label.includes("Red");
                        });

                        this.green = result.data.data.filter((image: any) => {
                            return image.label.includes("green") || image.label.includes("Green");
                        });

                        this.blue = result.data.data.filter((image: any) => {
                            return image.label.includes("blue") || image.label.includes("Blue");
                        });

                        this.detectImages = this.detectImages.concat(this.red);
                        this.detectImages = this.detectImages.concat(this.green);
                        this.detectImages = this.detectImages.concat(this.blue);

                        const finalImage: any = result.data.data.find((image: any) => {
                            return image.label.includes("Final")
                        });

                        this.detectImages.push(finalImage);
                    }

                    this.detectImages = this.detectImages.filter((detectImage: any) => {
                       return detectImage.label !== 'Fourier spectrum' &&
                           detectImage.label !== 'Fourier red spectrum' &&
                           detectImage.label !== 'Fourier green spectrum' &&
                           detectImage.label !== 'Fourier blue spectrum'
                    });

                    this.detectImages = this.detectImages.map((detectImage: any) => {
                       if (detectImage.label.includes("center")) {
                           detectImage.label = "After FFT";
                       } else if (detectImage.label.includes("mask") || detectImage.label.includes("Masked")) {
                           detectImage.label = "FFT + Mask";
                       } else if (detectImage.label.includes("Filtered")) {
                           detectImage.label = "Inverse FFT";
                       }

                       return detectImage;
                    });

                    this.isLoading = false;
                })
            }
        }
    }
</script>
