<template>
  <div>
    <div class="row">

      <div class="col">
        <h1>SNP Visualizations</h1>
        <br/>
        <breadcrumb :activeCrumb="processStep" :resultsLoaded="isReadyToDownLoad" :updateStep="setProcessStep"></breadcrumb>
        <file-selection :onDefinitionFileInputChanged="onDefinitionFileInputChanged" :onDataFileInputChanged="onDataFileInputChanged" v-if="processStep=='fileSelect'"></file-selection>
        <div class="card bg-light" v-if="processStep=='regionSpec'">
          <div class="card-header">
            <a name="regionSpec">Region specification</a>
          </div>
          <div class="card-body">
            <h4 class="card-title">Select chromosome and region</h4>
            <form>

              <div class="form-group">
                <label for="selectChromosome">Chromosome</label>
                <select class="form-control" id="selectChromosome" v-model="selectedChromosome"
                        @change="onChromosomeSelectChanged">
                  <option>1</option>
                  <option>2</option>
                  <option>3</option>
                  <option>4</option>
                  <option>5</option>
                  <option>6</option>
                  <option>7</option>
                  <option>8</option>
                  <option>9</option>
                  <option>10</option>
                  <option>11</option>
                  <option>12</option>
                  <option>13</option>
                  <option>14</option>
                  <option>15</option>
                  <option>16</option>
                  <option>17</option>
                  <option>18</option>
                  <option>19</option>
                  <option>20</option>
                  <option>21</option>
                  <option>22</option>
                  <option>X</option>
                  <option>Y</option>
                </select>
              </div>
              <div class="form-group">
                <label for="region-select">Region</label>
                <div class="form-check" v-if="analysisType=='upd'">
                  <label class="form-check-label">
                    <input type="checkbox" class="form-check-input" v-model="isCompleteChromosome" checked="analysisType=='upd'" :disabled="analysisType=='dup'">
                    Select complete chromosome
                  </label>
                </div>
                <div id="region-select" v-if="!isCompleteChromosome">
                  <small><label for="start">Start: </label></small>
                  <span><input type="number" id="start" v-model="regionStart"></span>
                  <small><label for="stop">Stop: </label></small>
                  <span><input type="number" id="stop" v-model="regionStop"></span>
                </div>
              </div>
            </form>
          </div>
        </div>
        <div class="card bg-light" v-if="processStep=='analysisSpec'">
          <div class="card-header">
            <a name="analysisSpec">Analysis specification</a>
          </div>
          <div class="card-body">
            <h4 class="card-title">Select type of analysis</h4>
            <form>
              <div class="form-group">
                <label for="typeRadiosGroup">Analysis type</label>
                <div id="typeRadiosGroup">
                  <div class="form-check">
                    <label class="form-check-label">
                      <input class="form-check-input" type="radio" name="typeRadios" id="upd" value="upd"
                             v-model="analysisType" @click="checkCompleteRegion" checked>
                      UPD/Deletion
                    </label>
                  </div>
                  <div class="form-check">
                    <label class="form-check-label">
                      <input class="form-check-input" type="radio" name="typeRadios" id="duplication" value="dup"
                             v-model="analysisType" @click="uncheckCompleteRegion">
                      Duplication
                    </label>
                  </div>
                  <div class="form-group" v-if="analysisType=='dup'">
                    <label for="selectChromosome">Select person with duplication</label>
                    <select class="form-control" required id="dropDown" v-model="personWithDuplication">
                      <option v-for="person in getPersonList">{{ person }}</option>
                    </select>
                  </div>
                </div>
              </div>

            </form>
          </div>
        </div>
      </div>
    </div>
    <div class="card bg-light" v-if="processStep=='results'">
      <div class="card-header">
        <a name="fileSelect">Overview</a>
      </div>
      <div class="card-body">
        <h4 class="card-title">Selection overview</h4>
        <table class="table table-bordered">
          <thead>
          <th scope="col">Setting</th>
          <th scope="col">Value</th>
          </thead>
          <tbody>
            <tr>
              <th scope="row">Definition file</th>
              <td>{{this.defFile}}</td>
            </tr>
            <tr>
              <th scope="row">Data file</th>
              <td>{{this.dataFile.name}}</td>
            </tr>
            <tr>
              <th scope="row">Selected chromosome</th>
              <td>{{this.selectedChromosome}}</td>
            </tr>
            <tr>
              <th scope="row">Region</th>
              <td v-if="isCompleteChromosome">
                Complete chromosome
              </td>
              <td v-else>
                  {{this.regionStart}} - {{this.regionStop}}
              </td>
            </tr>
            <tr>
              <th scope="row">Analysis type</th>
              <td v-if="analysisType=='upd'">UPD/deletion</td>
              <td v-else>Duplication</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="row" v-show="processStep=='analysisSpec'||processStep=='results'">
      <a name="results"></a>
      <div id="buttons" class="col-md-4">
        <button type="button" class="btn btn-primary" id="processFiles" @click="onProcessBtnClicked"
                :disabled="disableProcess" v-if="processStep=='analysisSpec'">Process data
        </button>

        <a id="download-btn" class="btn btn-primary" href="#" role="button" :disabled="!isReadyToDownLoad"
           v-bind:class="{ disabled: !isReadyToDownLoad }" v-show="processStep=='results'">
          <i class="fa fa-download" aria-hidden="true"></i><span> Download</span>
        </a>
      </div>

      <div id="status" class="col-md-4 text-center" v-if="processStep=='analysisSpec'||processStep=='results'">
        <div id="statusUpdate" v-bind:class="alertClass" v-if="status" role="alert">
          <small><i><span v-model="status"> {{status}} </span></i></small>
          <i class="fa fa-spinner fa-pulse fa-fw" v-if="isLoading"></i>
        </div>
      </div>
    </div>
    <br/>

    <div class="row" v-show="analysisType=='upd'&&isDisplayPlots&&processStep=='results'">
      <a name="results"></a>
      <div class="col">
        <div class="card">
          <div class="card-body">
            <div id="canvas-container">
              <canvas id="plot-canvas"></canvas>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row" v-show="analysisType=='dup'">
      <div class="col">
        <div class="card">
          <div class="card-body">
            <div id="dupication-analysis-results"></div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>
<script>
  import { SET_PARSED_DEF_OBJ, SET_DATA_INDEX } from '../store/mutations'
  import lineReader from '../service/lineReader'
  import identityByDecent from '../service/identityByDecent'
  import plotter from '../service/plotter'
  import dataDefinition from '../service/dataDefinition'
  import browser from 'browser-detect'
  import breadcrumb from './Breadcrumb'
  import fileSelection from './FileSelection'

  export default {
    name: 'snp-descent-plot',
    data: function () {
      return {
        regionStart: undefined,
        regionStop: undefined,
        processStep: 'fileSelect',
        isDisplayPlots: false,
        isReadyToDownLoad: false,
        isLoading: false,
        status: '',
        dataFile: undefined,
        hasDefFile: false,
        defFile: undefined,
        t0: 0,
        t1: 0,
        selectedChromosome: '1',
        alertClass: 'alert alert-primary',
        analysisType: 'upd',
        personWithDuplication: '',
        isCompleteChromosome: true
      }
    },
    components: { breadcrumb, fileSelection },
    created: function () {
      this.plotSizes = {
        height: 250,
        width: 1000,
        marginLeft: 25,
        marginRight: 25,
        marginBottom: 30,
        marginTop: 40,
        paddingLeft: 50,
        paddingRight: 50,
        bandWidth: 20,
        bandDistance: 50,
        titleOffset: 25,
        chromosomeBarHeight: 25,
        chromosomeBarRadius: 12
      }
      this.results = {}
    },
    methods: {
      setProcessStep (step) {
        this.processStep = step
      },
      checkCompleteRegion () {
        this.isCompleteChromosome = true
      },
      uncheckCompleteRegion () {
        this.isCompleteChromosome = false
      },
      onDefinitionFileInputChanged (event) {
        this.clear()
        const file = event.target.files[0]
        this.defFile = file.name
        if (file) {
          this.hasDefFile = true
          const self = this
          const reader = new FileReader()
          reader.onload = function () {
            const defObj = dataDefinition.readDefinitionLines(reader.result)
            self.$store.commit(SET_PARSED_DEF_OBJ, dataDefinition.calculatePlotCombinations(defObj))
          }
          reader.readAsText(file)
        } else {
          this.hasDefFile = false
        }
      },
      onDataFileInputChanged (event) {
        this.clear()
        this.dataFile = event.target.files[0]
      },
      onChromosomeSelectChanged () {
        plotter.clear()
        this.isDisplayPlots = false
        this.isReadyToDownLoad = false
        this.isLoading = false
        this.status = ''
        this.alertClass = 'alert alert-primary'
      },
      onProcessBtnClicked () {
        this.clear()
        this.isLoading = true
        this.isDisplayPlots = true
        this.status = 'Processing data'
        this.t0 = performance.now()
        const maxLines = 10000000
        if (this.analysisType === 'upd') {
          lineReader.readSomeLines(this.dataFile, maxLines, this.updProcessForEachLine, this.onCompleteUpdAnalysis, this.handleError)
        } else {
          lineReader.readSomeLines(this.dataFile, maxLines, this.duplicationForeachLine, this.onCompleteDuplicationAnalysis, this.handleError)
        }
      },
      clear () {
        plotter.clear()
        this.isDisplayPlots = false
        this.isReadyToDownLoad = false
        this.isLoading = false
        this.status = ''
        this.alertClass = 'alert alert-primary'
      },
      isSelectedChromosome (columns) {
        return columns[1] === this.selectedChromosome
      },
      handleError (error) {
        if (error.startsWith('[Invalid data]')) {
          console.error('[Mismatch Error] Definition file does not match data columns')
          this.status = 'Error! Does your data file match the definition file?'
        } else {
          console.error('[File read Error] Something went wrong reading the file')
          this.status = 'Error! Something went wrong while parsing your file.'
        }
        this.isLoading = false
        this.alertClass = 'alert alert-danger'
        plotter.clear()
        this.isDisplayPlots = false
        this.isReadyToDownLoad = false
        this.isLoading = false
      },
      onCompleteDuplicationAnalysis () {
        console.log('done')
      },
      duplicationForeachLine (line) {
        const columns = line.split('\t')
        let isSuccess = true
        console.log('process')
        if (this.isSelectedChromosome(columns)) {
          const combinationLabels = Object.keys(this.$store.state.dataIndex)
          console.log(combinationLabels)
          for (let combination of combinationLabels) {
            const index1 = this.$store.state.dataIndex[combination].gPos1
            const index2 = this.$store.state.dataIndex[combination].gPos2
            const geno1 = columns[index1]
            const measure1 = columns[index1 + 1]
            const geno2 = columns[index2]
            const measure2 = columns[index2 + 1]
            console.log(geno1, measure1, geno2, measure2)
          }
        } else if (columns[0] === 'Name') {
          const parsedDefData = this.$store.state.parsedDefObj
          const dataIndex = dataDefinition.buildDataIndex(parsedDefData, columns)
          this.$store.commit(SET_DATA_INDEX, dataIndex)
        }
        return isSuccess
      },
      /**
       * Process a single line from the data file.
       * Return true in case of successful processing of line to keep on reading.
       * Return false to indicate error and stop the read of the data file.
       **/
      updProcessForEachLine (line) {
        const columns = line.split('\t')
        let isSuccess = true
        if (this.isSelectedChromosome(columns)) {
          const combinationLabels = Object.keys(this.$store.state.dataIndex)
          for (let combination of combinationLabels) {
            const index1 = this.$store.state.dataIndex[combination].gPos1
            const index2 = this.$store.state.dataIndex[combination].gPos2
            const id1 = columns[index1]
            const id2 = columns[index2]
            const alleleScore = identityByDecent.computeScore(id1, id2)
            // The alleleScore is equal to the key in the counts object
            this.results[combination].counts[alleleScore] += 1
            this.results[combination].points.push([parseInt(columns[2]), alleleScore])
          }
        } else if (columns[0] === 'Name') {
          const parsedDefData = this.$store.state.parsedDefObj
          const dataIndex = dataDefinition.buildDataIndex(parsedDefData, columns)
          this.$store.commit(SET_DATA_INDEX, dataIndex)
          const combinations = Object.keys(parsedDefData)
          for (let combination of combinations) {
            if (dataIndex[combination].gPos1 === -1 || dataIndex[combination].gPos2 === -1) {
              isSuccess = false
            } else {
              this.results[combination] = {'counts': {1: 0, 2: 0, 0: 0, '-1': 0}, 'points': []}
            }
          }
        }
        return isSuccess
      },
      onCompleteUpdAnalysis () {
        this.t1 = performance.now()
        const plotFunction = plotter.plotIdentityByDecent
        plotter.plot(this.results, this.$store.state.dataIndex, this.plotSizes, this.selectedChromosome, plotFunction)
        this.results = {}
        this.isLoading = false
        this.isReadyToDownLoad = true
        this.status = `Completed in ${Math.round((this.t1 - this.t0) / 1000)} seconds`
        this.alertClass = 'alert alert-success'
        this.processStep = 'results'
      },
      /**
       * A click handler is added to the download button to have the download function execute in the context of the
       * click event. In other works; As a security feature the browser ( chrome ) will not allow the script to store
       * a file on the users computer, but will allow the user ( click event context ) to store the file.
       **/
      setDownLoadClickHandler () {
        const currentBrowser = browser()
        const isInternetExplorer = currentBrowser.name === 'edge' || currentBrowser.name === 'ie'

        /**
         * this function needs the execute in the context of the click event
         * @param link
         */
        function downloadCanvas (link) {
          const timestamp = plotter.buildTimeStamp().replace(/ /g, '_')
          const fileName = `${timestamp}.png`
          const canvas = document.getElementById('plot-canvas')
          if (!isInternetExplorer) {
            // non ie can use simple and fast method
            link.href = canvas.toDataURL()
            link.download = fileName
          } else {
            // ie needs to use blob as intermediate
            let blob = canvas.msToBlob()
            window.navigator.msSaveBlob(blob, fileName)
          }
        }

        document.getElementById('download-btn').addEventListener('click', function () {
          downloadCanvas(this)
        }, false)
      }
    },
    computed: {
      disableProcess: function () {
        return !(this.dataFile && this.hasDefFile)
      },
      getPersonList: function () {
        let persons = []
        for (let key of Object.keys(this.$store.state.parsedDefObj)) {
          let person1 = key.split('-')[0]
          let person2 = key.split('-')[1]
          persons.push(person1)
          persons.push(person2)
        }
        return [...new Set(persons)]
      }
    },
    mounted: function () {
      this.setDownLoadClickHandler()
    }
  }
</script>
