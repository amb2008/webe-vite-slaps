<script>
  import svelteLogo from "./assets/svelte.svg";
  import viteLogo from "/vite.svg";
  import Counter from "./lib/Counter.svelte";

  const suits = ["spade", "club", "heart", "diamond"];
  const values = [
    "2",
    "3",
    "4",
    "5",
    "6",
    "7",
    "8",
    "9",
    "10",
    "jack",
    "queen",
    "king",
    "ace",
  ];

  let deck = [];

  // Create the deck of Uno cards
  suits.forEach((suit) => {
    values.forEach((value) => {
      deck.push({ suit, value });
    });
  });

  function shuffleDeck() {
    deck.sort(() => Math.random() - 0.5);
  }

  shuffleDeck();

  let cards_per = Math.round(deck.length / 2);

  let player_1_cards = [];
  let player_2_cards = [];

  for (let i = 0; i < cards_per; i++) {
    player_1_cards.push(deck[i]);
    player_2_cards.push(deck[i + cards_per]);
  }

  console.log(player_1_cards);
  console.log(player_2_cards);

  let placed_cards = [];

  function placeCard(player) {
    let card_to_place = {};
    if (player == 1) {
      card_to_place = player_1_cards.pop();
      console.log(player_1_cards);
    } else {
      card_to_place = player_2_cards.pop();
    }

    placed_cards.push(card_to_place);
    console.log(placed_cards);

    placed_cards = placed_cards;
  }

  placeCard(1);
</script>

<main>
  <button
    on:click={() => {
      placeCard(1);
    }}
  >
    Player 1: Place Card
  </button>

  <button
    on:click={() => {
      placeCard(2);
    }}
  >
    Player 2: Place Card
  </button>

  <p>
    {#each placed_cards as placed_card}
      {placed_card.suit}
      {placed_card.value}
      <br />
    {/each}
  </p>
</main>

<style>
</style>
