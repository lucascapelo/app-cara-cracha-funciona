

<template>
  <!-- Salvar o id do andar em uma varialvel e fazer uma chamada no banco utilizando o Where para renderizar  -->
  <!-- os moradores específicos daquele andar -->
  <div>
    <div class="header">
      <!-- Titulo da página -->
      <v-toolbar>
        <v-spacer></v-spacer>
        <span class="headline text-uppercase">Lista de apartamentos</span>
        <v-spacer></v-spacer>
      </v-toolbar>
      <MenuPorteiro />
      <!-- Renderizando os botoes de andares -->
    </div>
    <v-row align="center">
      <v-col v-for="andar in apartamentos" :key="andar.num" class="text-center" cols="3">
        <v-row justify="center">
          <v-btn
            dark
            x-large
            color="#7a9fea"
            v-bind="andar.num"
            @click="openMoradoresCard(andar)"
          >{{andar}}</v-btn>
        </v-row>
      </v-col>
    </v-row>

    <!-- COMPONENT DE MORADORES -->
    <v-dialog fullscreen v-model="dialog">
      <v-card>
        <v-toolbar dark color="#0f3252">
          <v-btn color="primary" class="mr-2" text @click="closeDialog">
            <v-icon color="white">mdi-arrow-left</v-icon>
          </v-btn>
          <span class="headline">Moradores</span>
        </v-toolbar>
        <v-container fluid>
          <v-row dense>
            <!-- CADA CARD DE MORADOR -->
            <div class="cards" v-for="individuos in moradores" :key="individuos.id">
              <v-card v-if="individuos.tipo === 'Morador'" class="ma-2" max-width="200">
                <v-avatar class="profile" color="grey" size="164" tile>
              <v-img
                v-if="individuos.foto"
                :src="individuos.foto"
              ></v-img>
            </v-avatar>
               <v-card-title class="headline">
              {{individuos.nome}}
              <br />
              {{individuos.sobrenome}}
            </v-card-title>
                <v-card-text>
                  <div>
                    <v-chip>
                      <span>{{individuos.tipo}}</span>
                    </v-chip>
                  </div>
                  <div>
                    <strong>{{individuos.sexo}}</strong>
                  </div>
                  <div>
                    <strong>{{individuos.apartamento}}</strong>
                  </div>
                </v-card-text>
                <v-card-actions>
                  <v-btn v-if="session === 'sindico'" small color="red">
                    <v-icon color="white">mdi-delete</v-icon>
                  </v-btn>
                </v-card-actions>
              </v-card>
            </div>
          </v-row>
        </v-container>
        <v-toolbar dark color="#0f3252">
          <span class="headline">Agregados</span>
        </v-toolbar>
        <v-container fluid>
          <v-row dense>
            <!-- CADA CARD DE AGREGADOS -->
            <div class="cards" v-for="individuos in moradores" :key="individuos.id">
              <v-card v-if="individuos.tipo === 'Agregado'" class="ma-2" max-width="200">
               <v-avatar class="profile" color="grey" size="164" tile>
              <v-img
                v-if="individuos.foto"
                :src="individuos.foto"
              ></v-img>
            </v-avatar>
               <v-card-title class="headline">
              {{individuos.nome}}
              <br />
              {{individuos.sobrenome}}
            </v-card-title>
                <v-card-text>
                  <div>
                    <v-chip>
                      <span>{{individuos.tipo}}</span>
                    </v-chip>
                  </div>
                  <div>
                    <strong>{{individuos.sexo}}</strong>
                  </div>
                  <div>
                    <strong>{{individuos.apartamento}}</strong>
                  </div>
                </v-card-text>
                <v-card-actions v-if="session === 'sindico'">
                  <v-btn small color="red">
                    <v-icon color="white">mdi-delete</v-icon>
                  </v-btn>
                </v-card-actions>
              </v-card>
            </div>
          </v-row>
        </v-container>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import MenuPorteiro from "./MenuLateralP";
import bancoDados from "@/firebase/init";

export default {
  name: "ViewPorteiro",
  components: { MenuPorteiro },
  props: ["session"],
  data() {
    return {
      dialog: false,
      apartamentos: [],
      moradores: [],
      andarId: null
    };
  },
  methods: {
    openMoradoresCard(andar) {
      this.andarId = andar;
      bancoDados
        .collection("morador")
        .where("apartamento", "==", this.andarId)        
        .get()
        .then(snapshot => {
          snapshot.forEach(liver => {
            let morador = liver.data();
            morador.id = liver.id;
            this.moradores.push(morador);
          });
          console.log(this.moradores);
        });
      this.dialog = true;
      console.log(andar);
    },
    closeDialog() {
      this.dialog = false;
      this.moradores = [];
    }
  },
  created() {
    //chamada de apartamentos
    bancoDados
      
.collection("apartamentos")
.orderBy("num","asc")

      .get()
      .then(snapshot => {
        snapshot.forEach(doc => {
          let apto = doc.id;
          this.apartamentos.push(apto);
        });
      });
  }
};
</script>

<style>
</style>