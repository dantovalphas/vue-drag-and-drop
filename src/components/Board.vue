<template>
  <div
    class="d-flex flex-column board"
    :style="`background-color:${board.color}`"
  >
    <div class="d-flex flex-row justify-space-between">
      <h3>{{ board.title }}</h3>
      <v-btn depressed @click="dialogCard = true" class="mt-auto"
        >Agregar</v-btn
      >
    </div>

    <div class="d-flex flex-row pr-6 pt-3">
      <div
        v-for="list in board.lists"
        @drop="drop($event, list.id)"
        @dragover="allowDrop($event)"
        class="d-flex flex-column pt-3 mr-6 list"
        v-bind:key="list.id"
      >
        <div class="d-flex flex-row justify-space-between">
          <h4>{{ list.title }}</h4>
        </div>

        <!--display cards-->
        <v-card
          v-for="card in list.cards"
          :draggable="true"
          @dragover.prevent
          @dragstart="drag($event, card)"
          class="mt-5"
          @click="editCard(card)"
          v-bind:key="card.id"
        >
          <v-card-text> {{ card.title }} </v-card-text>
        </v-card>
      </div>
      <v-dialog v-model="dialogCard" persistent max-width="600px">
        <v-card elevation="0">
          <v-card-title>
            <span class="headline">Titulo</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    label="Titulo de la tarjeta"
                    v-model="card.title"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialogCard = false">
              Cerrar
            </v-btn>
            <v-btn color="blue darken-1" text @click="createCard()">
              Guardar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="dialogEditCard" persistent max-width="600px">
        <v-card elevation="0">
          <v-card-title>
            <span class="headline">{{ currentCard.title }}</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    label="Edit title"
                    v-model="currentCard.title"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="red darken-1" text @click="deleteCard()">
              Eliminar
            </v-btn>
            <v-btn color="blue darken-1" text @click="dialogEditCard = false">
              Cerrar
            </v-btn>
            <v-btn color="blue darken-1" text @click="updateCard()">
              Guardar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import { v4 as uuidv4 } from "uuid";

export default {
  layout: "board",
  data() {
    return {
      board: {
        color: "#ffffff",
        title: "Reclutamiento",
        dateCreated: "2021-08-31",
        lists: [
          {
            id: "476edd1b-a98c-4d4f-9361-5b670c1a1450",
            title: "Buscar reunion",
            cards: [
              {
                id: "985cce56-195d-4164-a4b8-57b98d3477d1",
                title: "Prospecto #1",
                dateCreated: "2021-08-31",
              },
              {
                id: "42cec0be-4c6d-44be-be41-0d64664bffac",
                title: "Prospecto #2",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
          {
            id: "26198e95-49b7-4051-b612-3aee534dbe12",
            title: "Reunion planificada",
            cards: [
              {
                id: "12853af4-bffc-4954-91f8-cd8b3b27059d",
                title: "Prospecto #3",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
          {
            id: "22792a1b-e6b8-45b2-a4bb-1d12f657483f",
            title: "Reunion técnica",
            cards: [
              {
                id: "c2bc03c9-5c06-4b4c-b37e-8b4b180f6466",
                title: "Prospecto #4",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
          {
            id: "8d295ca8-f03e-4fe7-940e-b7ab288640d3",
            title: "Reunion psicotécnica",
            cards: [
              {
                id: "95239b20-f28d-459d-b4c4-861b82139ab3",
                title: "Prospecto #5",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
          {
            id: "21aa8166-76d7-43b0-9693-be7d9f3e6cd0",
            title: "Aceptación del cliente",
            cards: [
              {
                id: "f473404e-c57e-4000-b178-fee108719b2a",
                title: "Prospecto #6",
                dateCreated: "2021-08-31",
              },
              {
                id: "7768187d-96fb-478d-b85e-9581ceb34e3f",
                title: "Prospecto #6",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
          {
            id: "f3a0d2a5-93e4-4e17-8607-cc88e62c1482",
            title: "A contratar",
            cards: [
              {
                id: "d80625bc-b3f1-4d9f-8682-fd4008643a67",
                title: "Prospecto #6",
                dateCreated: "2021-08-31",
              },
            ],
            dateCreated: "2021-08-31",
          },
        ],
      },
      listId: "",
      list: {
        title: "",
      },
      card: {
        title: "",
      },
      currentCard: {},
      cardDraggedId: "",
      cardDraggedListId: "",
      dialog: false,
      dialogCard: false,
      dialogEditCard: false,
      drawer: false,
    };
  },
  methods: {
    formatDate(value) {
      if (!value) return "";
      return moment(value).format("dddd, MMMM Do YYYY");
    },
    allowDrop(ev) {
      ev.preventDefault();
    },
    drag(ev, card) {
      this.currentCard = card;
    },
    drop(ev, listId) {
      ev.preventDefault();
      this.updateCardList(listId);
    },
    async createCard() {
      let that = this;
      that.dialogCard = false;
      //show modal to capture card name
      //add card
      if (that.card.title != "") {
        //Let's give our card a created date.
        that.listId = "476edd1b-a98c-4d4f-9361-5b670c1a1450";
        that.card.id = uuidv4();
        that.card.dateCreated = Date.now();
        that.card.listId = that.listId;
        if (that.board.lists) {
          let index = -1;
          let count = 0;
          for (const list of that.board.lists) {
            if (list.id === that.listId) {
              index = count;
            }
            count++;
          }
          if (index != -1) {
            //we found the list, now push our card
            if (that.board.lists[index].cards) {
              that.board.lists[index].cards.push(that.card);
            } else {
              that.board.lists[index].cards = [];
              that.board.lists[index].cards.push(that.card);
            }
          }
        }
        await that.updateBoard();
        that.card = {};
        that.listId = "";
      }
    },
    editCard(card) {
      this.dialogEditCard = true;
      this.currentCard = card;
    },
    async updateCardList(newlistId) {
      let that = this;

      let tempListIndex = -1;
      let tempCardIndex = -1;
      let newListIndex = -1;
      let tempListCount = 0;
      let tempCardCount = 0;

      //get the index in current cards current list
      for (const list of that.board.lists) {
        if (list.id === newlistId) {
          newListIndex = tempListCount;
        }
        if (list.cards.includes(that.currentCard)) {
          //correct list, now find card
          tempListIndex = tempListCount;
          for (const card of list.cards) {
            if (card.id === that.currentCard.id) {
              tempCardIndex = tempCardCount;
            }
            tempCardCount++;
          }
        }
        tempListCount++;
      }

      //remove currentCard from current list
      that.board.lists[tempListIndex].cards.splice(tempCardIndex, 1);

      //add currentCard to its new list
      that.currentCard.listId = newlistId;
      that.board.lists[newListIndex].cards.push(that.currentCard);

      await that.updateBoard();
    },
    async updateCard() {
      let that = this;
      that.dialogEditCard = false;
      for (const list of that.board.lists) {
        if (that.currentCard.listId === list.id) {
          //correct list, now find card
          for (let card of list.cards) {
            if (card.id === that.currentCard.id) {
              console.log("card", card);
            }
          }
        }
      }
      await that.updateBoard();
    },
    async deleteCard() {
      let that = this;
      that.dialogEditCard = false;
      let i = 0;
      let j = 0;
      let index = {
        list: -1,
        card: -1,
      };
      for (const list of that.board.lists) {
        if (that.currentCard.listId === list.id) {
          //correct list, now find card
          for (const card of list.cards) {
            if (card.id === that.currentCard.id) {
              index.list = i;
              index.card = j;
            }
            j++;
          }
        }
        i++;
      }
      if (index.list > -1) {
        that.board.lists[index.list].cards.splice(index.card, 1);
        await that.updateBoard();
      }
    },
    async updateBoard() {
      console.log("Board updated!");
    },
  },
};
</script>

<style scoped>
.board {
  padding: 12px;
  margin: 20px;
  height: 100vh;
  overflow: auto;
}
.list {
  min-width: 250px;
  background-color: rgb(228 228 228 / 35%);
  padding: 25px;
  border-radius: 12px;
  min-height: 70vh;
}
</style>
