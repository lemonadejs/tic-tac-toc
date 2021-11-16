# Tic tac toe
A super lightweight tic tac toe using LemonadeJS Reactive library.

The Tic tac toe is a classical game where two players take turns marking the spaces in a 
three-by-three grid with X or O. The player who succeeds in placing three of their marks in a horizontal, vertical, or diagonal row is the winner.

## How to run

`
var Tictactoe = function() {

    // Grid container
    var grid = null;

    // Game texts
    var text = [
        'Click play to start',
        'Turn to play',
        'Won the game',
    ]

    // Initializing self.
    var self = {
        // Property to set the turn.
        turn: '',
        // Property to define if bot will play.
        bot: true,
        // Property to store boxes marked and the player who's marked it..
        game: [],
        // Property to set what text will appear in the template.
        text: text[0],
        // Property to define if already reached a winner.
        winner: true,
        // Button play
        buttonLabel: 'Play',
    }

    /**
     * Checking if the span's id are the same as the playerSign. 
     * @param {number} val1 - Id of the span selected.
     * @param {number} val2 - Id of the span selected.
     * @param {number} val3 - Id of the span selected.
     * @returns Returns the id in win case.
     */
    var checkMatching = function(val1, val2, val3) {
        // Verify if all the spans was marked by the same player.
        if (self.game[val1] == self.game[val2] && self.game[val2] == self.game[val3]) {
            return self.game[val1];
        }
    }

    /**
     * self.clickedBox recieve the element clicked by the user and marks it with X or O depending the turn.
     * @param {element} element - used to determine what span will be marked.
     **/
    var clickedBox = function(element) {
        // Verify if a winner is defined to stop the game.
        if (self.winner || self.game[element.id]) {
            return false;
        }
        // Marks the box clicked according the player's turn.
        element.innerHTML = self.turn;
        // Increase the self.game array with the boxes already clicked.
        self.game[element.id] = self.turn;
        // Switch the text element to refers the player's turn.
        self.text = text[1];
        // Switch player's turn.
        if (self.turn == 'x') {
            self.turn = 'o';
        } else {
            self.turn = 'x';
        }
        // Variable to check if some player won the game.
        var winner = checkMatching(1, 2, 3) || checkMatching(4, 5, 6) ||
            checkMatching(7, 8, 9) || checkMatching(1, 4, 7) ||
            checkMatching(2, 5, 8) || checkMatching(3, 6, 9) ||
            checkMatching(1, 5, 9) || checkMatching(3, 5, 7);
        if (winner) {
            self.turn = winner;
            // Switch the text element to refers the winner.
            self.text = text[2];
            // A new winner
            self.winner = true;
        }

        return true;
    }

    /**
     * This will identifier the element clicked in the div if its a span, mark a boxes,
     * and if its a button, execute self.play. 
     * @param {element} o - Div element.
     */
    self.grid = function(o) {
        // Keep the grid container accessible
        grid = o;
        // Add an eventListener to wait for a click in the element.
        o.addEventListener('click', function(e) {
            // Create a var called element to store the target of the event e.
            var element = e.target;
            // Verify the tagName of the element.
            if (element.tagName == 'SPAN') {
                // Call the clickedBox function.
                if (clickedBox(element)) {
                    // If self.bot == true, this will do the bot move.
                    if (self.bot) {
                        // Define the pointerEvents to none, so the user cannot click while bot's turn.
                        o.style.pointerEvents = 'none';
                        // Set a timeout to the bot's turn.
                        setTimeout(() => {
                            // Variable to store the spans not yet marked.
                            var result = o.querySelectorAll('span:empty');
                            // Variable to select one of the not marked spans.
                            var randomBox = result[Math.floor(Math.random() * result.length)];
                            // Play in the random position
                            clickedBox(randomBox);
                            // Make the pointerEvents clickable again.
                            o.style.pointerEvents = '';
                        }, 500);
                    }
                }
            } else if (element.tagName == 'BUTTON') {
                self.play(o);
            }
        });
    }

    /**
     * Set all the game elements to the initial state.
     * @param {element} o - Div where is the game elements.
     */
    self.play = function() {
        // Start the game
        self.winner = false;
        // Start with player x
        self.turn = 'x';
        // Remove the elements from the game array.
        self.game = [];
        // Switch the text element to refers the player's turn.
        self.text = text[1];
        // Get all the spans elements to remove the marks.
        var result = grid.querySelectorAll('span');
        for (var i = 0; i < result.length; i++) {
            result[i].innerHTML = '';
        }
        self.buttonLabel = 'Restart';

    }

    // Game template
    var template = `
        <div>
            <h1>Tic tac toe</h1>
            <label class="jswitch">
                Bot is the second player <input type="checkbox" value='1' checked="checked" @bind='self.bot'> <i></i>
            </label>
            <div class="play-board">
                <div style="margin-top: 10px; margin-left: 70px; font-size: 20px">
                    <span style="text-transform: uppercase">{{self.turn}} </span> 
                    <span>{{self.text}}</span>
                </div>
                <div @ready="self.grid(this)" class="play-area">
                    <section>
                        <span id="1"></span>
                        <span id="2"></span>
                        <span id="3"></span>
                    </section>
                    <section>
                        <span id="4"></span>
                        <span id="5"></span>
                        <span id="6"></span>
                    </section>
                    <section>
                        <span id="7"></span>
                        <span id="8"></span>
                        <span id="9"></span>
                    </section>
                    <div class="btn"><button>{{self.buttonLabel}}</button></div>
                </div>
            </div>
        </div>`;

    return lemonade.element(template, self);
}

lemonade.render(Tictactoe, document.getElementById('root'));
`
