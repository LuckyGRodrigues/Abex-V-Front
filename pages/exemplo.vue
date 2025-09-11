<template>
  <div class="degrade" style="height: 100%;">
    <v-card style="background-color: #fb911f;">
      <v-tabs v-model="tab" align-tabs="center" color="red" class="custom-tabs-height">
          <a href="./">
            <v-tab
              :value="1" 
              class="text-none custom-tab" 
              style="color: darkred; font-weight: bolder; font-size: larger; margin-right: 70px;"
            >
              Início
            </v-tab>
          </a>  

          <a href="./cardapio">
            <v-tab
              :value="2" 
              class="text-none custom-tab" 
              style="color: darkred; font-weight: bold; font-size: large; margin-right: 70px;"
            >
              Cardápio
            </v-tab>
          </a>

          <a href="./comanda">
            <v-tab
              :value="3" 
              class="text-none custom-tab" 
              style="color: darkred; font-weight: bold; font-size: large; margin-right: 70px;"
            >
              Comanda
            </v-tab>
          </a>

          <a href="./ademiro">
            <v-tab
              :value="4" 
              class="text-none custom-tab" 
              style="color: darkred; font-weight: bold; font-size: large; margin-right: 70px;"
            >
              Admin
            </v-tab>
          </a>
      </v-tabs>
    </v-card>

    <div class="scrollable-content">
      <template v-if="loading">
        <div class="loading-container">
          <v-progress-circular
            indeterminate
            color="orange"
            size="100"
            width="10"
            class="mx-auto mt-10"
          />
        </div>
      </template>

      <template v-else>
        <v-card
          color="red"
          class="centerText centerCard mt-10"
        >
          Refrigerantes
        </v-card>
  
        <v-row>
          <v-col 
            v-for="(item, index) in soda"
            :key="index"
            cols="12"
            md="4"
          >
            <v-card
              class="mx-auto mt-10"
              max-width="325"
              elevation="15"
              style="border-radius: 10px;"
            >
              <v-img
                height="225px"
                :src="item.imagem"
                cover
              />
      
              <v-card-title>
                {{ item.nome }}
              </v-card-title>
      
              <v-card-subtitle>
                {{ item.descricaoProduto }}
              </v-card-subtitle>
      
              <v-card-actions>
                <v-btn
                  color="orange-lighten-2"
                  text="Requisitar"
                  @click="openDialog(item.id)"
                />
      
                <v-spacer/>
      
                <v-card-text> 
                  R$ {{ item.valor }}
                </v-card-text>
              </v-card-actions>
            </v-card>
  
            <v-dialog v-model="ativo" max-width="500">
              <v-card height="325" width="500" theme="dark">

                <v-card-title>
                  Realizar Pedido
                </v-card-title>

                <v-card-text>
                  <v-row>
                    <v-col>
                      <v-text-field 
                        v-model="pedido.quantidade"
                        placeholder="Quantidade de Produtos"
                        item-title="quantidade" 
                        item-value="quantidade"
                      />
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col>
                      <v-autocomplete 
                        v-model="pedido.idProduto"
                        :items="soda" 
                        placeholder="Produto"
                        item-title="nome" 
                        item-value="id"
                        disabled="true"
                      />
                    </v-col>
                    <v-col>
                      <v-autocomplete 
                        v-model="pedido.idToken"
                        :items="token" 
                        placeholder="Token"
                        item-title="id" 
                        item-value="id"
                      />
                    </v-col>
                  </v-row>
                </v-card-text>
                
                <v-card-actions>
                  <v-btn variant="outlined" class="text-none" @click="createPedido()">
                    Enviar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-col>
        </v-row>
  
        <v-card
          color="red"
          class="centerText centerCard mt-10"
        >
          Hambúrguers
        </v-card>
  
        <v-row>
          <v-col 
            v-for="(item, index) in items"
            :key="index"
            cols="12"
            md="4"
          >
            <v-card
              class="mx-auto mt-10"
              max-width="325"
              elevation="15"
              style="border-radius: 10px;"
            >
              <v-img
                height="225px"
                :src="item.imagem"
                cover
              />
      
              <v-card-title>
                {{ item.nome }}
              </v-card-title>
      
              <v-card-subtitle>
                {{ item.descricaoProduto }}
              </v-card-subtitle>
      
              <v-card-actions>
                <v-btn
                  color="orange-lighten-2"
                  text="Requisitar"
                  @click="openDialog2(item.id)"
                />
      
                <v-spacer/>
      
                <v-card-text> 
                  R$ {{ item.valor }}
                </v-card-text>
              </v-card-actions>
            </v-card>
  
            <v-dialog v-model="ativo2" max-width="500">
              <v-card height="325" width="500" theme="dark">

                <v-card-title>
                  Realizar Pedido
                </v-card-title>

                <v-card-text>
                  <v-row>
                    <v-col>
                      <v-text-field 
                        v-model="pedido.quantidade"
                        placeholder="Quantidade de Produtos"
                        item-title="quantidade" 
                        item-value="quantidade"
                      />
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col>
                      <v-autocomplete 
                        v-model="pedido.idProduto"
                        :items="items" 
                        placeholder="Produto"
                        item-title="nome" 
                        item-value="id"
                        disabled="true"
                      />
                    </v-col>
                    <v-col>
                      <v-autocomplete 
                        v-model="pedido.idToken"
                        :items="token" 
                        placeholder="Token"
                        item-title="id" 
                        item-value="id"
                      />
                    </v-col>
                  </v-row>
                </v-card-text>
                
                <v-card-actions>
                  <v-btn variant="outlined" class="text-none" @click="createPedido()">
                    Enviar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-col>
        </v-row>
      </template>
    </div>
  </div>
</template>
  
<script>
  export default {
    data: () => ({
      tab: 2,
      show: false,
      ativo: false,
      ativo2: false,
      selectedItemId: null,
      loading: true,
      pedido: {
        id: null,
        idCategoria:null,
        idToken: null,
      },
      items: [],
      soda: [],
      token: [],
    }),

    async created() {
      try {
        await this.getItems();
        await this.getPerCategory();
        await this.getToken();
        await this.getProduct();
      } finally {
        this.loading = false;
      }
    },

    methods: {
      async getItems() {
        this.loading = true;
        try {
          const response = await this.$api.get("/produto/categoria/2");
          this.items = response.response;
        } catch (error) {
          console.error("Erro ao carregar itens:", error);
        } finally {
          this.loading = false;
          console.log("dados carregados");
        }
      },

      async getPerCategory () {
        this.loading = true;
        try {
          const response = await this.$api.get("/produto/categoria/1");
          this.soda = response.response;
        } catch (error) {
          console.error("Erro ao carregar itens:", error);
        } finally {
          this.loading = false;
          console.log("dados carregados");
        }
      },

      async getToken () {
        this.loading = true;
        try {
          const response = await this.$api.get("/token");
          this.token = response.response;
        } catch (error) {
          console.error("Erro ao carregar itens:", error);
        } finally {
          this.loading = false;
          console.log("dados carregados");
        }
      },

      async createPedido() {
        this.loading = true;
        try {
          const response = await this.$api.post("/pedido/create", this.pedido);
          console.log("Pedido criado com sucesso:", response);
        } catch (error) {
          console.error("Erro ao criar pedido:", error);
        } finally {
          this.loading = false;
          this.ativo = false;
          this.ativo2 = false;
        }
      },

      openDialog(id) {
        this.selectedItemId = id;
        this.pedido.idProduto = id;
        this.ativo = true;
      },

      openDialog2(id) {
        this.selectedItemId = id;
        this.pedido.idProduto = id;
        this.ativo2 = true;
      },
    },
  }
</script>

<style>
  .scrollable-content {
    height: calc(100vh - 70px);
    overflow-y: auto;
    overflow-x: hidden;
    padding-bottom: 24px;
  }

  .loading-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .custom-tabs-height {
    height: 70px !important;
    display: flex;
    align-items: center;
  }

  .custom-tab {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
  }

  .signOut {
    color: darkred;
  }

  .centerCard {
    width: 200px;
    height: 50px;
    margin: 0 auto;
  }

  .centerText {
    height: 50px;
    line-height: 50px;
    text-align: center;
    font-size: 26px;
    font-weight: bolder;
    color:yellow !important;
  }

  .degrade {
    background: linear-gradient(0deg, rgba(2, 0, 36, 1) 0%, rgba(182, 52, 25, 1) 0%, rgba(255, 190, 0, 1) 100%);
    background-repeat: no-repeat;
    background-size: cover;
    height: 100vh;
  }
</style>