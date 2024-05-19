<script>
  import svelteLogo from "./assets/svelte.svg";
  import viteLogo from "/vite.svg";
  import Counter from "./lib/Counter.svelte";
  import { set } from "firebase/database";

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
  let showInstructions = true;
  let chaos = false;

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

  let show_alert = false;
  let show_alert_2 = false;
  let placed_cards = [];
  let lastThree = [];
  function placeCard(player) {
    let card_to_place = {};
    if (player == 1) {
      if (player_1_cards.length == 0) {
        alert("Player 1 has no more cards left");
        return;
      } else {
        card_to_place = player_1_cards.pop();
        console.log(player_1_cards);
      }
    } else {
      if (player_2_cards.length == 0) {
        alert("Player 2 has no more cards left");
        return;
      } else {
        card_to_place = player_2_cards.pop();
        console.log(player_2_cards);
      }
    }

    placed_cards.push(card_to_place);

    lastThree = placed_cards.slice(-5);
    lastThree = lastThree;
    placed_cards = placed_cards;
    player_1_cards = player_1_cards;
    player_2_cards = player_2_cards;
  }

  let player_clicked = 2;
  let correct_click = 2;
  let wrong_click = 2;
  let can_trigger = true;
  let aCounter = 0;
  let lCounter = 0;

  document.addEventListener("keydown", function (event) {
    if (event.key == "q") {
      placeCard(1);
    } else if (event.key == "p") {
      placeCard(2);
    } else if (can_trigger == true) {
      can_trigger = false;
      if (event.key == "a" || event.key == "l") {
        player_clicked = 2;
        if (event.key == "a") {
          aCounter++;
          player_clicked = 1;
        } else if (event.key == "l") {
          lCounter++;
          player_clicked = 2;
        }

        let result = isPalindrome(placed_cards);
        console.log(result);

        if (result !== false) {
          if (!chaos || aCounter == 5 || lCounter == 5) {
            correct_click = player_clicked;
            let cards_to_take = placed_cards;
            placed_cards = [];
            console.log(cards_to_take);
            if (player_clicked == 1) {
              for (let i = cards_to_take.length - 1; i >= 0; i--) {
                player_1_cards.unshift(cards_to_take[i]);
              }
              console.log(player_1_cards);
            } else {
              for (let i = cards_to_take.length - 1; i >= 0; i--) {
                player_2_cards.unshift(cards_to_take[i]);
              }
              console.log(player_2_cards);
            }
            lastThree = [];
            show_alert_2 = true;
            setTimeout(() => {
              show_alert_2 = false;
            }, 3000);
            aCounter = 0;
            lCounter = 0;
          }
        } else {
          can_trigger = true;
          wrong_click = player_clicked;
          show_alert = true;
          placeCard(player_clicked);
          placeCard(player_clicked);
          setTimeout(() => {
            show_alert = false;
          }, 3000);
          aCounter = 0;
          lCounter = 0;
        }

        placed_cards = placed_cards;
        player_1_cards = player_1_cards;
        player_2_cards = player_2_cards;
      }

      if (!chaos || aCounter == 5 || lCounter == 5) {
        setTimeout(() => {
          can_trigger = true;
        }, 1000);
      } else {
        can_trigger = true;
      }
    }
  });

  function isPalindrome(placed_cards) {
    if (placed_cards.length > 1) {
      let nums_to_check = [placed_cards[placed_cards.length - 1].value];
      for (let i = placed_cards.length - 2; i > -1; i--) {
        nums_to_check.push(placed_cards[i].value);
        let reversed_nums = nums_to_check.slice().reverse();
        if (nums_to_check.join("") == reversed_nums.join("")) {
          return i;
        } else if (i == 0) {
          return false;
        }
      }
    } else {
      return false;
    }
  }
</script>

<main style="transform: translateY(-200px);">
  <h1>
    Slaps
    <br />
    <button
      style="font-size: 14px;"
      on:click={() => {
        showInstructions = !showInstructions;
      }}
    >
      {#if showInstructions}
        Hide Instructions{/if}
      {#if !showInstructions}
        Show Instructions{/if}
    </button>
    <button
      style="font-size: 14px;"
      on:click={() => {
        chaos = !chaos;
      }}
      ><input type="checkbox" bind:checked={chaos} />
      Chaos
    </button>
  </h1>
  {#if showInstructions}
    <p>
      Welcome to slaps! Player 1 and Player 2 will take turns placing cards. If
      there is a palindrome starting from the last card placed, the first player
      to slap the table will take all the cards. Player 1 can place a card by
      clicking the button or pressing the "Q" key. Player 2 can place a card by
      clicking the button or pressing the "P" key. Player 1 can slap by pressing
      the "A" key and Player 2 can slap by pressing the "L" key.
    </p>
  {/if}
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
  {#if show_alert}
    <div class="alert" role="alert">
      <strong>Oh snap!</strong> Player {wrong_click} miss-slapped! Two of your cards
      have been placed.
    </div>
  {/if}
  {#if show_alert_2}
    <div class="alert2" role="alert">
      <strong>Well done!</strong> Player {correct_click} slapped and took all the
      cards.
    </div>
  {/if}
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
  .alert {
    color: #721c24;
    background-color: #f8d7da;
    border-color: #f5c6cb;
    padding: 10px;
    margin: 10px;
    z-index: 1000;
  }

  .alert2 {
    color: #155724;
    background-color: #d4edda;
    border-color: #c3e6cb;
    padding: 10px;
    margin: 10px;
    z-index: 1000;
  }
</style>
