<template>
  <router-view v-if="AdLoad"/>
</template>
<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'App',
  mounted() {
    if (cordova.platformId == 'ios') {

      const admobid = {
        banner: 'ca-app-pub-9515612908682608/1851745519', // or DFP format "/6253334/dfp_example_ad"
        interstitial: 'ca-app-pub-9515612908682608/8766290614'
      };

      if(AdMob){} AdMob.createBanner({
        adId: admobid.banner,
        position: AdMob.AD_POSITION.BOTTOM_CENTER,
        autoShow: true });

      if(AdMob) AdMob.prepareInterstitial( {adId:admobid.interstitial, autoShow:false} );

      document.addEventListener('onAdLoaded', (e) =>{
        this.AdLoad = true;
      });
    }
  },
  data(){
    const AdLoad = false;
    return{
      AdLoad: AdLoad,
    }
  }
})
</script>
