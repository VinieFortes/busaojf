<template>
  <q-layout class="q-mb-xl">
    <q-header :style="{backgroundColor: headerColor}"  class="q-pa-sm flex row items-center text-white no-wrap">
      <q-icon class="q-pa-sm" v-ripple size="25px" name="arrow_back" style="cursor: pointer; flex: 0" @click="this.$router.back()"></q-icon>
      <span class="q-pl-md" style="font-size: 22px; flex: 1; text-overflow: ellipsis; white-space: nowrap; overflow: hidden;">{{linha}} - {{ nome }}</span>
      <q-icon @click="showInti" size="25px" class="q-pr-sm" name="directions"></q-icon>
      <q-icon v-if="!isFavorito" @click="favoritar" v-ripple size="25px" name="star_outline"></q-icon>
      <q-icon v-else @click="favoritar" v-ripple size="25px" name="star"></q-icon>
    </q-header>

    <q-page-container class="q-mb-md">
      <span class="q-mt-md flex justify-center" style="flex: 1; font-size: 16px; font-weight: bold;">Saída de {{ generateNomeLinha(nome, 0, linha)}}</span>
      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Dias úteis</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_ida_semana">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Sábados</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_ida_sabado">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Domingos e Feriados</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_ida_domingo">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

      <span class="q-mt-md flex justify-center" style="flex: 1; font-size: 16px; font-weight: bold;">Saída de {{ generateNomeLinha(nome, 1, linha)}}</span>

      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Dias úteis</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_volta_semana">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Sábados</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_volta_sabado">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

      <q-card style="border-top-right-radius: 6px; border-top-left-radius: 6px" class="q-mt-sm flex column">
        <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">Domingos e Feriados</span>
        <q-card-section class="q-pa-sm flex column items-center justify-center">
          <td class="td q-gutter-sm">
            <q-badge :color="badgeColor" :style="{fontSize: tamanhoTexto + 'px', fontWeight: 'bold', padding: '5px'}" v-for="horario in h_volta_domingo">{{horario}}</q-badge>
          </td>
        </q-card-section>
      </q-card>

    </q-page-container>

    <q-dialog v-model="showDialog">
      <q-card class="q-dialog-plugin">
        <div class="flex items-center justify-center q-gutter-x-sm row q-pa-sm">
          <q-btn flat icon="arrow_back_ios" @click="showDialog = false"></q-btn>
          <q-icon style="flex: 0" size="23px" name="directions"></q-icon>
          <span style="font-weight: bold; font-size: 16px; flex: 1" >Itinerário {{linha}} - {{nome}}</span>
        </div>
        <q-card-section class="flex">
          <q-card class="flex">
            <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">IDA</span>
            <span class="q-pa-sm">{{inti_ida.split(',').join(', ')}}</span>
          </q-card>
        </q-card-section>
        <q-card-section class="flex">
          <q-card class="flex">
            <span class="q-mb-sm text-white text-center" :style="{fontSize: '16px', fontWeight: 'bold', backgroundColor: headerColor, flex: '1', borderTopRightRadius: '6px', borderTopLeftRadius: '6px'}">VOLTA</span>
            <span class="q-pa-sm">{{inti_volta.split(',').join(', ')}}</span>
          </q-card>
        </q-card-section>
      </q-card>
    </q-dialog>

  </q-layout>
</template>

<script>
import dados from "assets/output.json"
import {useQuasar} from "quasar";
export default {
  name: "Horario",

  methods: {
    generateNomeLinha(nome, index, linha){
      switch (index){
        case 0:
          if(parseInt(linha) === 115){
            return nome.split('/')[1];
          }else{
            return nome.split('/')[0];
          }
        case 1:
          if(parseInt(linha) === 115){
            return nome.split('/')[0];
          }else{
            return !nome.split('/')[1] ? 'Centro' : nome.split('/')[1]
          }
      }

    },
    showInti(){
      this.showDialog = true
    },
    favoritar(){
      this.isFavorito = !this.isFavorito;
      this.showNotif(this.isFavorito, this.linha);
      let dadosLocalStorage = [];
      if(window.localStorage.getItem('favoritos')){
        dadosLocalStorage = JSON.parse(window.localStorage.getItem('favoritos'));
        if(this.isFavorito){
          dadosLocalStorage.push(this.linha)
        }else{
          dadosLocalStorage = dadosLocalStorage.filter(item => item !== this.linha)
        }
        window.localStorage.setItem('favoritos', JSON.stringify(dadosLocalStorage));
      }else{
        window.localStorage.setItem('favoritos', JSON.stringify([this.linha]))
      }

    }
  },

  mounted() {
    if(this.$adShow === false){
      if(AdMob) AdMob.showInterstitial( (success)=>{
        if(success){
          this.$adShow = true;
        }
      });
    }

    let dadosLocalStorage = [];
    if(window.localStorage.getItem('favoritos')){
      dadosLocalStorage = JSON.parse(window.localStorage.getItem('favoritos'));
      dadosLocalStorage.includes(this.linha) === true ? this.isFavorito = true : this.isFavorito = false;
    }
    let tam_num = 1;
    if(window.localStorage.getItem('tam_txt')){
      tam_num = parseInt(window.localStorage.getItem('tam_txt'));
    }else {
      tam_num = '16'
    }
    this.tamanhoTexto = tam_num;
  },

  data(){
    let linha = ''
    linha = this.$route.query.linha;
    let nome = ''
    let tamanhoTexto = null
    let h_ida_semana = null
    let h_ida_sabado = null
    let h_ida_domingo = null

    let h_volta_semana = null
    let h_volta_sabado = null
    let h_volta_domingo = null

    let int_ida = null
    let int_volta = null

    let headerColor = '#FFFFFF';
    let badgeColor = 'primary';

    let isFavorito = false;

    const $q = useQuasar()

    if(linha < 300){
      headerColor = '#1976D2';
      badgeColor = 'primary'
      if (cordova.platformId === 'android' || cordova.platformId === "ios") {
        StatusBar.overlaysWebView(false);
        StatusBar.styleBlackTranslucent()
        StatusBar.backgroundColorByHexString("#1976D2");
      }
    }
    else if(linha > 300 && linha < 600){
      headerColor = '#C10015';
      badgeColor = 'negative'
      if (cordova.platformId === 'android' || cordova.platformId === "ios") {
        StatusBar.overlaysWebView(false);
        StatusBar.styleBlackTranslucent()
        StatusBar.backgroundColorByHexString("#C10015");
      }
    }else{
      headerColor = '#26A69A';
      badgeColor = 'secondary'
      if (cordova.platformId === 'android' || cordova.platformId === "ios") {
        StatusBar.overlaysWebView(false);
        StatusBar.styleBlackTranslucent()
        StatusBar.backgroundColorByHexString("#26A69A");
      }
    }

    dados.forEach(item=>{
      item.forEach((el)=>{
        if(parseInt(linha) === el.numero){
          nome = el.nome
          el.H_ida.forEach(ida =>{
            h_ida_semana = ida.semana
            h_ida_sabado = ida.sabado
            h_ida_domingo = ida.domingo
          })
          el.H_volta.forEach(volta =>{
            h_volta_semana = volta.semana
            h_volta_sabado = volta.sabado
            h_volta_domingo = volta.domingo
          })
          el.intinerario.forEach(int =>{
            int_ida = int.ida
            int_volta = int.volta
          })
        }
      })
    })

    return{
      linha: linha,
      json: dados,
      nome: nome,
      h_ida_semana: h_ida_semana,
      h_ida_sabado: h_ida_sabado,
      h_ida_domingo: h_ida_domingo,
      h_volta_semana: h_volta_semana,
      h_volta_sabado: h_volta_sabado,
      h_volta_domingo: h_volta_domingo,
      inti_ida: int_ida,
      inti_volta: int_volta,
      headerColor: headerColor,
      badgeColor: badgeColor,
      isFavorito: isFavorito,
      tamanhoTexto: tamanhoTexto,
      showNotif (fav, linha) {
        if(fav){
          $q.notify({
            message: `${this.nome} foi  adicionado aos favoritos`,
            icon: 'star_rate',
            color: 'primary',
            timeout: 1000
          })
        }else{
          $q.notify({
            message: `${this.nome} foi removido dos favoritos`,
            icon: 'delete_forever',
            color: 'negative',
            timeout: 1000
          })
        }
      },
      showDialog: false
    }
  }

}
</script>

<style scoped>
.td{
  display: grid;
  grid-template-columns: repeat(5, 1fr); /* Cada linha terá 5 colunas */
}
</style>
