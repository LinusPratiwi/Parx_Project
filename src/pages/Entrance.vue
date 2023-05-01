<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-xl-3 col-md-6">
          <stats-card>
            <div slot="header" class="icon-warning">
              <i class="nc-icon nc-chart text-warning"></i>
            </div>
            <div slot="content">
              <p class="card-category">Confidence</p>
              <h4 class="card-title" v-if="recognitionResults.faceDetections != null">
                {{ Math.floor(recognitionResults.faceDetections.Confidence) }} %
              </h4>
            </div>
            <div slot="footer"><i class="fa fa-refresh"></i>Updated now</div>
          </stats-card>
        </div>

        <div class="col-xl-3 col-md-6">
          <stats-card>
            <div slot="header" class="icon-success">
              <i class="nc-icon nc-light-3 text-success"></i>
            </div>
            <div slot="content">
              <p class="card-category">Age Range</p>
              <h4 class="card-title" v-if="recognitionResults.faceDetections != null">
                {{ recognitionResults.faceDetections.AgeRange.Low }} - {{ recognitionResults.faceDetections.AgeRange.High }}
              </h4>
            </div>
            <div slot="footer"><i class="fa fa-calendar-o"></i>Last day</div>
          </stats-card>
        </div>

        <div class="col-xl-3 col-md-6">
          <stats-card>
            <div slot="header" class="icon-danger">
              <i class="nc-icon nc-vector text-danger"></i>
            </div>
            <div slot="content">
              <p class="card-category">Errors</p>
              <h4 class="card-title">23</h4>
            </div>
            <div slot="footer"><i class="fa fa-clock-o"></i>Last day</div>
          </stats-card>
        </div>

        <div class="col-xl-3 col-md-6">
          <stats-card>
            <div slot="header" class="icon-info">
              <i class="nc-icon nc-favourite-28 text-primary"></i>
            </div>
            <div slot="content">
              <p class="card-category">Followers</p>
              <h4 class="card-title">+45</h4>
            </div>
            <div slot="footer"><i class="fa fa-refresh"></i>Updated now</div>
          </stats-card>
        </div>
      </div>
      <div class="row">
        <div class="col-md-7 text-center">
          <card subTitle="video">
            <video ref="video" id="liveCam" v-if="liveOptions == 'live'"></video>
            <div v-if="liveOptions == 'upload'">
              <img
                v-if="imageUrl != ''"
                :src="imageUrl"
                class="uploadedImage mb-4"
                id="imgUpload"
                alt="image uploaded by user"
              />
              <img v-else src="img/upload2.png" class="w-50" alt="upload icon" />
            </div>
            <div class="input-group">
              <select class="custom-select mb-2" id="inputGroupSelect04" v-model="liveOptions">
                <option value="live">Live</option>
                <option value="upload">Upload File</option>
              </select>
              <button
                type="button"
                class="btn btn-primary active-btn-live btn-sm btn-block"
                v-if="liveOptions == 'live'"
              >
                START LIVE RECOGNITION
              </button>
            </div>
            <div class="input-group" v-if="liveOptions == 'upload'">
              <div class="custom-file">
                <input type="file" class="custom-file-input" @change="handleFileUpload" />
                <label class="custom-file-label">Choose file</label>
              </div>
            </div>
          </card>
        </div>

        <div class="col-md-5">
          <div class="card-header">Face</div>
          <div class="card-body border">
            <div class="text-center">
              <div v-if="isLoading" class="spinner-border" role="status"></div>
            </div>
            <div class="table-responsive" v-if="recognitionResults.faceDetections != null">
              <table class="table table-borderless" aria-label="face recognition results">
                <th class="px-2">Recognition Results</th>
                <tbody>
                  <tr class="detection">
                    <td class="col-5">Contains a Face (%)</td>
                    <td class="text-right">{{ recognitionResults.faceDetections.Confidence }}</td>
                  </tr>
                  <tr>
                    <td>Age Range (years old)</td>
                    <td class="text-right">
                      {{ recognitionResults.faceDetections.AgeRange.Low }} - {{ recognitionResults.faceDetections.AgeRange.High }}
                    </td>
                  </tr>
                  <tr>
                    <td>Gender</td>
                    <td class="text-right"> {{recognitionResults.faceDetections.Gender.Value}} </td>
                  </tr>
                  <tr class="">
                    <td>Bounding Box</td>
                    <tr>
                      <td class="text-center">Height</td>
                      <td class="text-right">{{ recognitionResults.faceDetections.BoundingBox.Height }}</td>
                    </tr>
                    <tr>
                      <td class="text-center">Left</td>
                      <td class="text-right">{{ recognitionResults.faceDetections.BoundingBox.Left }}</td>
                    </tr>
                    <tr>
                      <td class="text-center">Top</td>
                      <td class="text-right">{{ recognitionResults.faceDetections.BoundingBox.Top }}</td>
                    </tr>
                    <tr>
                      <td class="text-center">Width</td>
                      <td class="text-right">{{ recognitionResults.faceDetections.BoundingBox.Width }}</td>
                    </tr>
                  </tr>
                  <tr v-if="recognitionResults.faceMatches" class="text-center recognition-label">
                    <td colspan="2">Face is recognized as {{ recognitionResults.faceMatches.ExternalImageId }} </td>
                  </tr>
                </tbody>
              </table>
              <button class="btn btn-primary btn-sm" @click="indexFace()">
                Index Face
              </button>
            </div>
            <h5 class="card-title" v-if="!isLoading">{{ cardResponse.face }}</h5>
          </div>

          <!-- Plate Recognition -->
          <div class="card-header">Plate</div>
          <div class="card-body border">
            <h5 class="card-title" v-if="!isLoading && recognitionResults.faceDetections == null">No image to be analyzed</h5>
            <div class="text-center">
              <div v-if="isLoading" class="spinner-border" role="status"></div>
            </div>
            <div class="table-responsive" v-if="recognitionResults.faceDetections != null">
              <table class="table table-borderless" aria-label="plate recognition results">
                <th></th>
                <tbody>
                  <tr>
                    <td>Does not contain a plate</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>

      <div class="row">
        <div class="col-md-6">
          <chart-card :chart-data="pieChart.data" chart-type="Pie">
            <template slot="header">
              <h4 class="card-title">Email Statistics</h4>
              <p class="card-category">Last Campaign Performance</p>
            </template>
            <template slot="footer">
              <div class="legend">
                <i class="fa fa-circle text-info"></i> Open
                <i class="fa fa-circle text-danger"></i> Bounce
                <i class="fa fa-circle text-warning"></i> Unsubscribe
              </div>
              <hr />
              <div class="stats"><i class="fa fa-clock-o"></i> Campaign sent 2 days ago</div>
            </template>
          </chart-card>
        </div>

        <div class="col-md-6">
          <card>
            <template slot="header">
              <h5 class="title">Tasks</h5>
              <p class="category">Backend development</p>
            </template>
            <l-table :data="tableData.data" :columns="tableData.columns">
              <template slot="columns"></template>

              <template slot-scope="{ row }">
                <td>
                  <base-checkbox v-model="row.checked"></base-checkbox>
                </td>
                <td>{{ row.title }}</td>
                <td class="td-actions text-right">
                  <button
                    type="button"
                    class="btn-simple btn btn-xs btn-info"
                    v-tooltip.top-center="editTooltip"
                  >
                    <i class="fa fa-edit"></i>
                  </button>
                  <button
                    type="button"
                    class="btn-simple btn btn-xs btn-danger"
                    v-tooltip.top-center="deleteTooltip"
                  >
                    <i class="fa fa-times"></i>
                  </button>
                </td>
              </template>
            </l-table>
            <div class="footer">
              <hr />
              <div class="stats"><i class="fa fa-history"></i> Updated 3 minutes ago</div>
            </div>
          </card>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import ChartCard from 'src/components/Cards/ChartCard.vue'
import StatsCard from 'src/components/Cards/StatsCard.vue'
import LTable from 'src/components/Table.vue'

export default {
  components: {
    LTable,
    ChartCard,
    StatsCard,
  },
  data() {
    return {
      isLoading: null,
      editTooltip: 'Edit Task',
      deleteTooltip: 'Remove',
      pieChart: {
        data: {
          labels: ['40%', '20%', '40%'],
          series: [40, 20, 40],
        },
      },
      lineChart: {
        data: {
          labels: [
            '9:00AM',
            '12:00AM',
            '3:00PM',
            '6:00PM',
            '9:00PM',
            '12:00PM',
            '3:00AM',
            '6:00AM',
          ],
          series: [
            [287, 385, 490, 492, 554, 586, 698, 695],
            [67, 152, 143, 240, 287, 335, 435, 437],
            [23, 113, 67, 108, 190, 239, 307, 308],
          ],
        },
        options: {
          low: 0,
          high: 800,
          showArea: false,
          height: '245px',
          axisX: {
            showGrid: false,
          },
          lineSmooth: true,
          showLine: true,
          showPoint: true,
          fullWidth: true,
          chartPadding: {
            right: 50,
          },
        },
        responsiveOptions: [
          [
            'screen and (max-width: 640px)',
            {
              axisX: {
                labelInterpolationFnc(value) {
                  return value[0]
                },
              },
            },
          ],
        ],
      },
      barChart: {
        data: {
          labels: [
            'Jan',
            'Feb',
            'Mar',
            'Apr',
            'Mai',
            'Jun',
            'Jul',
            'Aug',
            'Sep',
            'Oct',
            'Nov',
            'Dec',
          ],
          series: [
            [542, 443, 320, 780, 553, 453, 326, 434, 568, 610, 756, 895],
            [412, 243, 280, 580, 453, 353, 300, 364, 368, 410, 636, 695],
          ],
        },
        options: {
          seriesBarDistance: 10,
          axisX: {
            showGrid: false,
          },
          height: '245px',
        },
        responsiveOptions: [
          [
            'screen and (max-width: 640px)',
            {
              seriesBarDistance: 5,
              axisX: {
                labelInterpolationFnc(value) {
                  return value[0]
                },
              },
            },
          ],
        ],
      },
      tableData: {
        data: [
          {
            title: 'Sign contract for "What are conference organizers afraid of?"',
            checked: false,
          },
          { title: 'Lines From Great Russian Literature? Or E-mails From My Boss?', checked: true },
          {
            title:
              'Flooded: One year later, assessing what was lost and what was found when a ravaging rain swept through metro Detroit',
            checked: true,
          },
          { title: 'Create 4 Invisible User Experiences you Never Knew About', checked: false },
          { title: 'Read "Following makes Medium better"', checked: false },
          { title: 'Unfollow 5 enemies from twitter', checked: false },
        ],
      },
      imageUrl: '',
      liveOptions: 'upload',
      recognitionResults: {},
      isIndex: false,
      cardResponse: {},
      rekognition: null,
    }
  },

  watch: {
    liveOptions(newVal, oldVal) {
      if (newVal == 'live') {
        this.openCam()
      }
      if (newVal == 'upload') {
        this.stopCam()
        this.imageUrl = ''
      }
    },
  },

  methods: {
    async handleFileUpload(event) {
      // this.refreshData()
      this.getCredentials()
      const file = event.target.files[0]
      const reader = new FileReader()
      reader.onload = () => {
        this.imageUrl = reader.result
        // this.processImage(reader.result)
        let jpg = true
        let _image = null
        try {
          // _image = Buffer.from(reader.result.replace(/^data:image\/[a-z]+;base64,/, ""),'base64')
          _image = window.atob(reader.result.split('data:image/jpeg;base64,')[1])
        } catch (e) {
          jpg = false
        }
        if (!jpg) {
          try {
            _image = window.atob(reader.result.split('data:image/png;base64,')[1])
          } catch (e) {
            alert('Not an image file Rekognition can process')
            return
          }
        }
        //unencode image bytes for Rekognition DetectFaces API
        let length = _image.length
        let imageBytes = new ArrayBuffer(length)
        let ua = new Uint8Array(imageBytes)
        for (let i = 0; i < length; i++) {
          ua[i] = _image.charCodeAt(i)
        }
        //Call Rekognition
        this.detectFaces(ua)
        this.recognizeFace(ua)
      }
      reader.readAsDataURL(file)
    },
    handleLiveOptions(event) {
      if (this.liveOptions == 'live') console.log('live')
      if (this.liveOptions == 'upload') this.handleFileUpload(event)
      else return
    },

    openCam() {
      navigator.mediaDevices
        .getUserMedia({
          video: true,
          width: { ideal: 1280 },
          height: { ideal: 720 },
        })
        .then((stream) => {
          const video = this.$refs.video
          video.srcObject = stream
          video.play()
        })
        .catch((e) => {
          console.error(e)
        })
    },

    stopCam() {
      let stream = this.$refs.video.srcObject
      stream.getTracks().forEach((track) => {
        track.stop()
      })
    },

    async detectFaces(img) {
      let param = {
        Image: {
          Bytes: img,
        },
        Attributes: ['ALL'],
      }
      let self = this
      self.isLoading = true
      try {
        await this.rekognition.detectFaces(param, function (err, res) {
          self.isLoading = false
          console.log(res, 'fas')
          self.recognitionResults['faceDetections'] = res.FaceDetails[0] ? res.FaceDetails[0] : []
        })
        // await this.recognizeFace(img)
      } catch (error) {
        console.log(error)
      }
    },

    async recognizeFace(img) {
      let param = {
        CollectionId : "vitefp_collection",
        MaxFaces:1,
        Image:{
            Bytes:img
        }
      }

      try {
        let self = this
        await this.rekognition.searchFacesByImage(param, function (err, res) {
          // this.recognitionResult/s.faceRecognition = res
          console.log(res, 'fasdfo')
          self.recognitionResults['faceMatches'] = res.FaceMatches.length > 0 ? res.FaceMatches[0].Face : []
        })
        console.log(self.recognitionResults,  'rsei')
      } catch (error) {
        
      }
    },

    getCredentials() {
      AWS.config.region = 'ap-southeast-1'
      AWS.config.credentials = new AWS.CognitoIdentityCredentials({
        IdentityPoolId: 'ap-southeast-1:3091bafe-81a1-4b1f-853b-ffd0b4e83b06',
      })

      this.rekognition = new AWS.Rekognition({
        region: AWS.config.region, 
        credentials: AWS.config.credentials
      })
    },

    indexFace() {
      this.isIndex = false
    },

    
  },
}
</script>
