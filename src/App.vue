<template>
  <div id="app">
    <Header />
    <div class="board">
      <div
        class="lane"
        v-for="([lane, cards], index) in Object.entries(board)"
        :key="index"
      >
        <h2 class="lane-title">{{ lane }}</h2>
        <Container
          group-name="Kanban"
          @drag-start="handleDrag(lane, $event)"
          @drop="handleDrop(lane, $event)"
          :get-child-payload="getChildPayload"
          :drop-placeholder="{ class: 'placeholder' }"
        >
          <Draggable v-for="card in cards" :key="card.id">
            <Card> {{ card.title }} </Card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
import Header from './components/Header.vue';
import Card from './components/Card.vue';
import initialCards from './shared/fixtures/initialCards.js';
import { Container, Draggable } from 'vue3-smooth-dnd';

export default {
  components: {
    Header,
    Card,
    Container,
    Draggable,
  },
  data() {
    return {
      board: initialCards,
      draggingCard: {
        lane: '',
        index: -1,
        cardData: {},
      },
    };
  },
  methods: {
    handleDrop(lane, dropResult) {
      const { removedIndex, addedIndex } = dropResult;
      if (lane === this.draggingCard.lane && removedIndex === addedIndex) {
        return;
      }
      if (removedIndex !== null) {
        this.board[lane].splice(removedIndex, 1);
      }
      if (addedIndex !== null) {
        this.board[lane].splice(addedIndex, 0, this.draggingCard.cardData);
      }
    },
    handleDrag(lane, dragResult) {
      const { payload, isSource } = dragResult;
      if (isSource) {
        this.draggingCard = {
          lane,
          index: payload.index,
          cardData: {
            ...this.board[lane][payload.index],
          },
        };
      }
    },
    getChildPayload(index) {
      return {
        index,
      };
    },
  },
};
</script>

<style scoped>
.board {
  display: flex;
  justify-content: flex-start;
  margin: 1.2rem 0.8rem;
  padding: 0 0.7rem;
  align-items: flex-start;
}

.lane {
  background-color: var(--color-grey);
  width: 23rem;
  border-radius: 0.8rem;
  box-shadow: 0 0.1rem 0.2rem rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
}
.lane-title {
  padding: 0.8rem 0.5rem;
  margin-bottom: 0.6rem;
}
.placeholder {
  background-color: rgba(33, 33, 33, 0.008);
  border-radius: 0.4rem;
  transform: scaleY(0.85);
  transform-origin: 0% 0%;
}
</style>
