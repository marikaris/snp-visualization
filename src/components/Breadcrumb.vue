<template>
  <small>
    <nav aria-label="breadcrumb" role="navigation">
      <ol class="breadcrumb">
        <li class="breadcrumb-item">
          <span v-if="activeCrumb=='fileSelect'">1. File selection</span>
          <a href="#fileSelect" @click="updateStep('fileSelect')" v-else>1. File selection</a>
        </li>
        <li class="breadcrumb-item">
          <span v-if="activeCrumb=='regionSpec'">2. Region specification</span>
          <a href="#regionSpec" @click="updateStep('regionSpec')" v-else>2. Region specification</a>
        </li>
        <li class="breadcrumb-item">
          <span v-if="activeCrumb=='analysisSpec'">3. Analysis specification</span>
          <a href="#analysisSpec" @click="updateStep('analysisSpec')" v-else>3. Analysis specification</a>
        </li>
        <li class="breadcrumb-item">
          <span v-if="activeCrumb=='results'">4. Results</span>
          <span v-else>
            <a href="#results" @click="updateStep('results')" v-if="resultsLoaded">4. Results</a>
            <span style="color:#868e96" v-else>4. Results</span>
          </span>
        </li>
      </ol>
    </nav>
  </small>
</template>
<script>
  export default {
    name: 'breadcrumb',
    props: ['activeCrumb', 'resultsLoaded', 'updateStep']
  }
</script>
