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
      let key = value;
      if (value == "jack") {
        key = "J";
      } else if (value == "queen") {
        key = "Q";
      } else if (value == "king") {
        key = "K";
      } else if (value == "ace") {
        key = "A";
      }
      deck.push({ suit, value, key });
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
  let lastThree = [];
  function placeCard(player) {
    let card_to_place = {};
    if (player == 1) {
      card_to_place = player_1_cards.pop();
      console.log(player_1_cards);
    } else {
      card_to_place = player_2_cards.pop();
    }

    placed_cards.push(card_to_place);

    lastThree = placed_cards.slice(-3);
    lastThree = lastThree;
    placed_cards = placed_cards;
    player_1_cards = player_1_cards;
    player_2_cards = player_2_cards;
  }
</script>

<main style="transform: translateY(-100px);">
  <button
    on:click={() => {
      placeCard(1);
    }}
  >
    <strong>Player 1: Place Card</strong>
    <br />
    Cards Left: {player_1_cards.length}
  </button>

  <button
    on:click={() => {
      placeCard(2);
    }}
  >
    <strong>Player 2: Place Card</strong>
    <br />
    Cards Left: {player_2_cards.length}
  </button>

  <p>
    {#each lastThree as placed_card, i}
      <img
        src={`../public/cards-images/${placed_card.suit}s_${placed_card.key}.png`}
        alt={`${placed_card.value} of ${placed_card.suit}`}
        style="position: absolute; transform: translateX({-50 +
          10 * i}%) translateY({10 * i}%);"
      />
      <br />
    {/each}
  </p>
</main>

<style>
</style>
