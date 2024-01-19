<template>
  <q-layout view="hHh lpR fFf" class="q-mb-xl">
    <q-header  class="q-pa-sm flex row items-center bg-white text-black no-wrap">
      <q-icon class="q-pa-sm" size="25px" name="menu" style="cursor: pointer" @click="toggleLeftDrawer"></q-icon>
      <span class="q-pl-md" v-if="!focusSearchBar" style="flex: 1; font-size: 22px">Busão JF</span>
      <q-icon @click="showSearchBar" style="cursor: pointer" class="q-animate--scale" v-if="showSearch === false" size="sm" name="search"></q-icon>
      <q-input v-model="pesquisaText" style="flex: 1" @focusout="searchBarOnfocus(false)" @focusin="searchBarOnfocus(true)" autofocus class="q-animate--fade q-pl-md" v-else hide-bottom-space borderless label="Pesquisar Linhas" >
        <template v-slot:after>
          <q-icon name="search"></q-icon>
        </template>
      </q-input>
    </q-header>

    <q-drawer elevated  v-model="rightDrawerOpen" side="left" bordered class="flex column">
      <div class="flex column">
        <span class="q-pa-sm">Tamanho fonte do Texto</span>
        <q-slider
          class="q-pa-sm"
          v-model="tamanhoTexto"
          markers
          marker-labels
          :min="1"
          :max="3"
          @update:model-value="newTamText"
        >
          <template v-slot:marker-label-group="{ markerMap }">
            <q-badge v-if="tamanhoTexto === 1" color="primary" style="font-size: 16px; font-weight: bold; padding: 5px">00:00</q-badge>
            <q-badge v-if="tamanhoTexto === 2" color="primary" style="font-size: 18px; font-weight: bold; padding: 5px">00:00</q-badge>
            <q-badge v-if="tamanhoTexto === 3" color="primary" style="font-size: 22px; font-weight: bold; padding: 5px">00:00</q-badge>
          </template>
        </q-slider>
      </div>
<!--      <div class="q-mb-xl flex column">-->
<!--        <span class="q-pa-sm">Gostou do App ? Então avalie-o na Play Store!</span>-->
<!--        <a style="text-decoration: none" href="https://play.google.com/store/apps/details?id=com.vinie4app.busaojf" class="flex">-->
<!--          <q-btn style="flex: 1" color="primary" icon="reviews" label="Avaliar"></q-btn>-->
<!--        </a>-->
<!--      </div>-->
      <q-space/>
<!--      <span class="q-pa-sm">Acesse nosso site:</span>-->
<!--      <q-btn color="primary" label="Busão Nacional" icon="directions_bus" @click="goToSite">-->

<!--      </q-btn>-->
      <div class="flex justify-end items-end column q-pa-sm">
        <div class="items-end q-gutter-x-sm">
          <span>Feito por Vinie Fortes</span>
          <a href="https://github.com/VinieFortes">
            <q-icon  style="font-size: 16px" class="fa fa-github text-black"></q-icon>
          </a>
        </div>
      </div>
      <span class="q-mb-xl q-ml-md" style="font-size: medium"> Ultima Atualização dos horários: Janeiro de 2024</span>
    </q-drawer>

    <q-page-container class="q-mb-md">

      <q-card v-if="pesquisaText" class="q-mt-sm flex column">
        <q-card-section class="no-padding flex justify-center">
          <div class="flex items-center q-gutter-x-sm">
            <q-icon name="search"></q-icon>
            <span>Sua pesquisa</span>
          </div>
        </q-card-section>
        <q-card-section>
          <q-list v-for="(linha) in searchEngine()" class="q-mb-sm"  style="border-radius: 16px" separator>
            <q-item style="border-radius: 16px; background-color: rgba(142, 142, 147, 0.2)" @click="this.$router.push({path: '/Horario', query: {linha: showNumber(linha)}})" class="q-gutter-x-sm" clickable v-ripple>
              <q-badge :color="colorBus(linha)">{{showNumber(linha)}}</q-badge>
              <q-item-section>{{ showName(linha) }}</q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>

      <q-card v-if="listaFav.length > 0" class="q-mt-sm flex column">
        <q-card-section class="no-padding flex justify-center">
          <div class="flex items-center q-gutter-x-sm">
            <q-icon name="star"></q-icon>
            <span>Linhas favoritas</span>
          </div>
        </q-card-section>
        <q-card-section>
          <q-list v-for="(linha) in listaFav" class="q-mb-sm"  style="border-radius: 16px" separator>
            <q-item style="border-radius: 16px; background-color: rgba(142, 142, 147, 0.2)" @click="this.$router.push({path: '/Horario', query: {linha: linha.numero}})" class="q-gutter-x-sm" clickable v-ripple>
              <q-badge :color="colorBus(linha, true)" >{{linha.numero}}</q-badge>
              <q-item-section>{{ linha.nome }}</q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>

      <q-card class="q-mt-sm flex column">
        <q-card-section class="no-padding flex justify-center">
          <div class="flex items-center q-gutter-x-sm">
            <q-icon name="directions_bus"></q-icon>
            <span>Todas as linhas</span>
          </div>
        </q-card-section>
        <q-card-section>
          <q-list v-for="(linha) in json" class="q-mb-sm" style="border-radius: 16px" separator>
            <q-item style="border-radius: 16px; background-color: rgba(142, 142, 147, 0.2)" @click="this.$router.push({path: '/Horario', query: {linha: showNumber(linha)}})" class="q-gutter-x-sm" clickable v-ripple>
              <q-badge :color="colorBus(linha)">{{showNumber(linha)}}</q-badge>
              <q-item-section>{{ showName(linha) }}</q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>
    </q-page-container>

  </q-layout>
</template>

<script lang="ts">

import { defineComponent, ref } from 'vue'
import dados from "assets/output.json"

export default defineComponent({
  name: 'MainLayout',

  components: {},

  mounted() {
    if (cordova.platformId == 'android') {

      const admobid = {
        banner: 'ca-app-pub-9515612908682608/2557703352', // or DFP format "/6253334/dfp_example_ad"
        interstitial: 'ca-app-pub-9515612908682608/2480793995'
      };

      if(AdMob){} AdMob.createBanner({
        adId: admobid.banner,
        position: AdMob.AD_POSITION.BOTTOM_CENTER,
        autoShow: true });

      if(AdMob) AdMob.prepareInterstitial( {adId:admobid.interstitial, autoShow:false} );

      StatusBar.overlaysWebView(false);
      StatusBar.backgroundColorByHexString("#FFFFFF");
      StatusBar.styleDefault()
    }


    if (cordova.platformId == 'ios') {

      StatusBar.overlaysWebView(false);
      StatusBar.backgroundColorByHexString("#FFFFFF");
      StatusBar.styleDefault()
    }
    if(window.localStorage.getItem('tam_txt')){
      switch (window.localStorage.getItem('tam_txt')){
        case '16':
          this.tamanhoTexto = 1;
          break;
        case '18':
          this.tamanhoTexto = 2;
          break;
        case '22':
          this.tamanhoTexto = 3;
          break;
      }
    }
  },

  methods:{
    goToSite(){
      window.open('https://busaonacional.com.br', '_blank')
    },
    newTamText(){
      switch (this.tamanhoTexto){
        case 1:
          window.localStorage.setItem('tam_txt', '16');
          break;
        case 2:
          window.localStorage.setItem('tam_txt', '18');
          break;
        case 3:
          window.localStorage.setItem('tam_txt', '22');
          break;
      }
    },
    searchEngine(){
      let newArray: { numero: number; nome: string; H_ida: { semana: string[]; sabado: string[]; domingo: string[] }[]; H_volta: { semana: string[]; sabado: string[]; domingo: string[] }[]; intinerario: { ida: string; volta: string }[] }[][] = []
      dados.forEach(linha =>{
        newArray.push(linha.filter(val => (val.nome.toUpperCase().includes(this.pesquisaText.toUpperCase())) || val.numero.toString().startsWith(this.pesquisaText)))
      })
      return newArray.filter(val => val.length > 0);
    },

    toggleLeftDrawer(){
      this.rightDrawerOpen = !this.rightDrawerOpen
    },
    showNumber(linha: any[]){
      let numero = null
      linha.forEach((item)=>{
        numero = item.numero
      })
      return numero;
    },
    showName(linha: any[]){
      let nome = null
      linha.forEach((item)=>{
        nome = item.nome
      })
      return nome;
    },
    showSearchBar(){
      this.showSearch = !this.showSearch
    },
    searchBarOnfocus(args: any){
      this.focusSearchBar = args
      if(args === false && !this.pesquisaText){
        this.showSearch = false;
      }
    },
    colorBus(linha: { numero: number; forEach: (arg0: (item: any) => void) => void }, isFav: any){
      let color = null
      if(isFav){
        if(linha.numero <= 300){
          color = 'primary'
        }else if(linha.numero < 600 && linha.numero > 300){
          color = 'negative'
        }else{
          color = 'secondary'
        }
        return color;
      }else{
        linha.forEach((item)=>{
          if(item.numero <= 300){
            color = 'primary'
          }else if(item.numero < 600 && item.numero > 300){
            color = 'negative'
          }else{
            color = 'secondary'
          }
        })
        return color;
      }
    }
  },


  data() {
    const favoritos = window.localStorage.getItem('favoritos');
    let listaFavoritos: { numero: number; nome: string }[] = [];
    let dadosLocalStorage = [];
    let showBar = false;
    let focusSerachBar = false;
    let tamanhoTexto = 1;


    if(window.localStorage.getItem('favoritos')){
      dadosLocalStorage = JSON.parse(window.localStorage.getItem('favoritos') as string);
      dadosLocalStorage.forEach((dado: string) =>{
        dados.forEach(linha=>{
          linha.forEach(item =>{
            if(item.numero === parseInt(dado)){
              listaFavoritos.push({numero: item.numero, nome: item.nome})
            }
          })
        })
      })
    }


    return {
      json: dados,
      rightDrawerOpen: ref(false),
      favoritos: favoritos,
      listaFav: listaFavoritos,
      pesquisaText: ref(''),
      showSearch: showBar,
      focusSearchBar: focusSerachBar,
      tamanhoTexto: tamanhoTexto
    }
  }
})
</script>

<style scoped>
*{
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>
