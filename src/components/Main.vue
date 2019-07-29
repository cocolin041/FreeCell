<template>
  <div id="main">
    <header>
      <div class="logo">
        <img src="assets/logo-bk.png" />
      </div>
      <div class="timer">
        <div><img src="assets/btn-more.png" /></div>
        <div>Time: {{ time_use }}</div>
        <div><img src="assets/btn-return.png" /></div>
      </div>
      <div class="score">
        <div><img src="assets/bone.png" /></div>
        <div>Score: {{ score }}</div>
      </div>
    </header>

    <main>
      <div class="top-card-area">
        <div id="temporary-put-area">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
        <div id="get-score-area">
          <div><img src="assets/heart.png" alt="heart-area" /></div>
          <div><img src="assets/club.png" alt="club-area" /></div>
          <div><img src="assets/spade.png" alt="spade-area" /></div>
          <div><img src="assets/dimond.png" alt="dimond-area" /></div>
        </div>
      </div>

      
      <div class="deck-folder">
        <!-- <draggable group="people" class="container" v-for="deck in decks" :key="deck.id" :move="moveCard">
          <img class="content" src = "assets/heart_cards/heart-1.png">
          <draggable group="people" class="container" :move="moveCard">
            <img class="content" src = "assets/heart_cards/heart-2.png">
            <draggable group="people" class="container" :move="moveCard">
              <img class="content" src = "assets/heart_cards/heart-3.png">
            </draggable>
          </draggable>
        </draggable> -->
      </div>

      <!-- <div class="bottom-card-area">
        <draggable v-for="deck in cardDecks" :key="deck.id" group="people" @start="drag=true" draggable=".row:last-child">
          <div class="row" v-for="card in deck.cardDeck" :key="card.url">
            <img v-bind:src = " 'assets/' + card.suit + '_cards/' + card.suit + '-' + card.id + '.png' ">
          </div>
        </draggable>
      </div> -->
    </main>

    <footer>
      <div class="bg-left">
        <img src="assets/bg-left.png" />
      </div>
      <div class="bg-JQK">
        <div class="quickly">quickly</div>
        <img src="assets/bg-JQK.svg" />
      </div>
      <div class="bg-right">
        <img src="assets/bg-right.png" />
      </div>
    </footer>
  </div>
</template>

<script>
import draggable from 'vuedraggable';
import Sortable from 'sortablejs';

export default {
  name: 'Main',
  components: {
    draggable
  },
  data: () => ({
    time_use: '00:00',
    score: '00',
    cardDecks: [],
    // test: [
    //   {
    //     cardDeck: [
    //       {id: 1, suit: 'heart', url: id+suit},
    //       {id: 1, suit: 'heart', url: id+suit}
    //     ]
    //   },
    //   {
    //     cardDeck: [
    //       {id: 1, suit: 'heart', url: id+suit},
    //       {id: 1, suit: 'heart', url: id+suit}
    //     ]
    //   }
    // ],
    decks: [
      {id: 1},
      {id: 2},
      {id: 3},
      {id: 4},
    ]
  }),
  props: {},
  created () {
    this.timer();
    // this.moveCard();
  },
  mounted () {
    this.assign_cards();
    // this.moveCard();
  },
  updated () {
    this.moveCard();
  },
  methods: {

    timer () {
      let data = this;
      let origin_min = Number(this.time_use.split(':')[0]);
      let origin_sec = Number(this.time_use.split(':')[1]);
      let origin_total = origin_min * 60 + origin_sec;
      let timer = setInterval(() => {
        origin_total += 1;
        origin_min = Math.floor(origin_total / 60);
        origin_sec = origin_total % 60;
        data.time_use = (origin_min>=10?origin_min:'0'+origin_min) + ':' + (origin_sec>=10?origin_sec:'0'+origin_sec);
      }, 1000);
    },

    deal_cards () {
      let data = this;

      // create all cards array
      let cards_id = Array.from(Array(14).keys());
      cards_id.shift(); //remove '0' index
      let suits = ['heart', 'spade', 'club', 'dimond'];
      let cards = [];
      cards_id.forEach( id => suits.forEach(suit => {
        cards.push( {id: id, suit: suit, url: id+suit} )
      }) );
      
      // shuffle cards
      let cards_shuffled = this.shuffle(cards);

      // assign cards into deck
      let cardDeck, card_num;
      let card_num_list = [6, 6, 6, 6, 7, 7, 7, 7];
      card_num_list = this.shuffle(card_num_list);
      for (let deck_num = 0; deck_num < 8; deck_num++) {
        card_num = card_num_list.splice(0, 1);
        cardDeck = cards_shuffled.splice(0, card_num);
        cardDeck = { cardDeck: cardDeck };
        data.cardDecks.push(cardDeck);
      };
    },

    shuffle (arr) {
      var i, j, temp;
      for (i = arr.length - 1; i > 0; i--) {
          j = Math.floor(Math.random() * (i + 1));
          temp = arr[i];
          arr[i] = arr[j];
          arr[j] = temp;
      }
      return arr;
    },

    assign_cards () {
      let data = this;
      this.deal_cards();
      let cardDecks = data.cardDecks;
      
      let single_deck, deck_el;
      let deck_folder = document.getElementsByClassName('deck-folder')[0];
      let container, container_idx = 0;

      cardDecks.forEach(deck => {
        single_deck = deck.cardDeck;

        container = `<draggable group="people" class="container"></draggable>`;
        deck_folder.insertAdjacentHTML('beforeend', container);
        single_deck.forEach( (card, i) => {
          i += 1;
          container = document.getElementsByClassName('container')[container_idx];
          if (i !== single_deck.length) {
            deck_el = `<img class="content" src = "assets/${card.suit}_cards/${card.suit}-${card.id}.png">
                      <draggable group="people" class="container"></draggable>`;
            container_idx += 1;
          } else {
            deck_el = `<img class="content" src = "assets/${card.suit}_cards/${card.suit}-${card.id}.png">`;
          }
          container.insertAdjacentHTML('beforeend', deck_el);
        });
        container_idx += 1;
      });
      
    },

    moveCard () {
      let dragged, related;
      var sortableOptions2 = {
        group: {
          name: "sortable-list-2",
          pull: true,
          put: true,
        },
        direction: 'vertical',
        dragoverBubble: true,
        animation: 250,

        onMove: function(evt, originalEvent) {
          if (evt.dragged.className.includes('content') || evt.related.children.length > 0 ) {
            return false;
          }
          else {
            dragged = evt.dragged;
            related = evt.related;
            dragged.id = ' dragged';
            related.id = ' related';
            var containers = null;
            containers = document.getElementById("related");
            console.log(containers);
            // for (var i = 0; i < containers.length; i++) {
              new Sortable(containers, sortableOptions2);
            // }
            return 1;
          }
        }
      };
      var containers = null;
      if (containers) {
      containers = document.getElementById("related");
      // console.log(containers);
      for (var i = 0; i < containers.length; i++) {
        new Sortable(containers, sortableOptions2);
      }
      }
    }

  }
};
</script>


<style lang="scss">
@import "../style/main.scss";
</style>
