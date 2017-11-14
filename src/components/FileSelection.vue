<template>
  <div class="card bg-light">
    <div class="card-header">
      <a name="fileSelect">File selection</a>
    </div>
    <div class="card-body">
      <h4 class="card-title">Select definition and data file</h4>
      <div class="form-group">
        <label for="devFileInput">Definition file</label>
        <input class="form-control" id="devFileInput" type="file" @change="onDefinitionFileInputChanged">
      </div>
      <div class="form-group">
        <label for="snpFileInput">Data file</label>
        <input class="form-control" id="snpFileInput" type="file" @change="onDataFileInputChanged">
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    name: 'file-selection',
    props: ['onDefinitionFileInputChanged', 'onDataFileInputChanged']
  }
</script>
