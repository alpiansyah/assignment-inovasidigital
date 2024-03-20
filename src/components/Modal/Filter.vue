<template>
    <div>
        <div class="modal fade" id="filterModal" tabindex="-1" aria-labelledby="filterModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-lg modal-dialog-centered">
              <div class="modal-content">
                <div class="modal-header">
                  <h1 class="modal-title fs-5" id="filterModalLabel">Filter Map</h1>
                  <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                  <div class="container text-left">
                    <div class="row align-items-start" >
                        <div class="col-md-2">
                          By Country
                        </div>
                        <div class="col">
                            <select class="form-select" @change="submit(['Country',selectedCountry])" v-model="selectedCountry" aria-label="Default select example">
                                <option value="">All</option>
                                <option v-for="(item, index) in listCountry" :key="index">{{ item['Country']}}</option>
                              </select>
                        </div>
                      </div>
                      <div class="row align-items-start mt-3" >
                        <div class="col-md-2">
                          By District
                        </div>
                        <div class="col">
                            <select class="form-select" @change="submit(['District',selectedDistrict])" v-model="selectedDistrict" aria-label="Default select example">
                                <option value="">All</option>
                                <option v-for="(item, index) in listDistrict" :key="index">{{ item['District']}}</option>
                              </select>
                        </div>
                      </div>
                  </div>
                </div>
                
              </div>
            </div>
          </div>
    </div>
</template>

<script>
export default {
    name: 'filter-component',
    props: ['themecolor', 'datasets'],
    data(){
        return {
            selectedCountry: null,
            selectedDistrict: null
        }
    },
    computed: {
        listCountry(){
            let filtered = null
            if(!localStorage.countries){
                const unique = this.datasets.map(({ Country }) => Country);
                filtered = this.datasets.filter(({ Country }, index) =>
                    !unique.includes(Country, index + 1));
                localStorage.countries = JSON.stringify(filtered)
            } else {
                filtered = JSON.parse(localStorage.countries)
            }
            
            return filtered
        },
        listDistrict(){
            let filtered = null
            if(!localStorage.districts){
                const unique = this.datasets.map(({ District }) => District);
                filtered = this.datasets.filter(({ District }, index) =>
                    !unique.includes(District, index + 1));
                localStorage.districts = JSON.stringify(filtered)
            } else {
                filtered = JSON.parse(localStorage.districts)
            }
            
            return filtered
        }
    },
    methods: {
        submit(value){
            this.$emit('submitted', value)
            if(value[0] === 'Country') this.selectedDistrict = null
            else this.selectedCountry = null
            this.hideModal()
        },
        hideModal(){
            var myModalEl = document.getElementById('filterModal');
            myModalEl.style.display = 'none'
            document.body.classList.remove('modal-open');
            document.body.style = ''
            document.querySelector(".modal-backdrop").remove();
        }
    }
}
</script>

<style>
#filterModal {
    filter: v-bind('themecolor')
}
  
</style>