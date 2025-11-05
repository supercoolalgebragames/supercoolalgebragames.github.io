<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Algebra Adventure - 20 Question Challenge</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        /* Custom scrollbar for a more thematic feel */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #231d4b; /* Dark purple */
        }
        ::-webkit-scrollbar-thumb {
            background: #ec4899; /* Pink */
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #f472b6; /* Lighter pink */
        }
    </style>
    <script>
        // Configuration for Tailwind
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        theme: {
                            purple: '#4c1d95',
                            'purple-light': '#6d28d9', 
                            pink: '#ec4899',
                            'pink-light': '#f9a8d4',
                            red: '#ef4444',
                            'red-light': '#f87171',
                        }
                    }
                }
            }
        }

        /* --- GAME LOGIC START (CARD_COUNT = 20 from 120-Card Pool) --- */

        const CATEGORIES = ["Equation", "Inequality", "Expression", "Constant", "Identity", "Function"];
        
        // Full deck remains at 120 questions for variety
        const ALL_CARDS = [
            // -------------------- Equations (20 Examples) --------------------
            ["4x - 1 = 15", "Equation"], ["a + 2b = 10", "Equation"], ["\\frac{y}{2} = 10", "Equation"], ["z^2 = 81", "Equation"],
            ["5m + 3 = 18", "Equation"], ["7k - 2 = 12", "Equation"], ["\\sqrt{x} = 4", "Equation"], ["\\frac{3}{p} = 6", "Equation"],
            ["2(r+1) = 8", "Equation"], ["t^2 - 4 = 0", "Equation"], ["x^3 = 27", "Equation"], ["w/5 + 1 = 3", "Equation"],
            ["-2y + 5 = 1", "Equation"], ["c + 3 = -9", "Equation"], ["3(d-2) = 15", "Equation"], ["\\sin(\\theta) = 0.5", "Equation"],
            ["x^2 + 2x + 1 = 0", "Equation"], ["\\log(n) = 2", "Equation"], ["\\frac{1}{a} + 5 = 7", "Equation"], ["b^3 - 8 = 0", "Equation"],

            // -------------------- Inequalities (20 Examples) --------------------
            ["3z + 1 > 7", "Inequality"], ["x - 5 \\le 20", "Inequality"], ["2y < 12", "Inequality"], ["\\frac{m}{3} \\ge 4", "Inequality"],
            ["-4p > 8", "Inequality"], ["k + 1 \\ne 5", "Inequality"], ["5a - 1 < 19", "Inequality"], ["2w + 5 \\ge 15", "Inequality"],
            ["\\frac{t}{2} - 3 > 1", "Inequality"], ["10 - x \\le 5", "Inequality"], ["\\vert y \\vert < 3", "Inequality"], ["\\vert z - 1 \\vert \\ge 2", "Inequality"],
            ["3(r+2) > 9", "Inequality"], ["-c + 4 \\le 10", "Inequality"], ["0 < x < 5", "Inequality"], ["-1 \\le 2q + 1 \\le 3", "Inequality"],
            ["x^2 > 4", "Inequality"], ["\\frac{n-1}{4} < 2", "Inequality"], ["d^2 + 1 \\ge 1", "Inequality"], ["\\vert 2a \\vert \\ne 6", "Inequality"],

            // -------------------- Constants (20 Examples) --------------------
            ["10", "Constant"], ["-3.5", "Constant"], ["\\pi", "Constant"], ["\\sqrt{16}", "Constant"],
            ["e", "Constant"], ["\\log_{10}(100)", "Constant"], ["\\cos(0)", "Constant"], ["2^3", "Constant"],
            ["-7", "Constant"], ["\\frac{1}{4}", "Constant"], ["(5+3)/2", "Constant"], ["0", "Constant"],
            ["\\vert -12 \\vert", "Constant"], ["\\sqrt{2}", "Constant"], ["10\\%", "Constant"], ["-1", "Constant"],
            ["\\frac{22}{7}", "Constant"], ["i^2", "Constant"], ["\\tan(45^{\\circ})", "Constant"], ["\\ln(e)", "Constant"],
            
            // -------------------- Identities (20 Examples) --------------------
            ["(x+1)^2 = x^2 + 2x + 1", "Identity"], ["a^2 - b^2 = (a-b)(a+b)", "Identity"], ["\\frac{x}{x} = 1", "Identity"], ["3x - x = 2x", "Identity"],
            ["\\sin^2(\\theta) + \\cos^2(\\theta) = 1", "Identity"], ["x(y+z) = xy + xz", "Identity"], ["(x-y)^2 = x^2 - 2xy + y^2", "Identity"], ["(a^m)(a^n) = a^{m+n}", "Identity"],
            ["\\frac{x+2x}{x} = 3", "Identity"], ["\\vert x^2 \\vert = x^2", "Identity"], ["(x+y)(x-y) + y^2 = x^2", "Identity"], ["\\frac{x^2-1}{x-1} = x+1", "Identity"],
            ["\\log(xy) = \\log(x) + \\log(y)", "Identity"], ["x^0 = 1", "Identity"], ["\\tan(\\theta) = \\frac{\\sin(\\theta)}{\\cos(\\theta)}", "Identity"], ["x + 0 = x", "Identity"],
            ["\\sqrt{x^2} = \\vert x \\vert", "Identity"], ["(a+b)^3 = a^3+3a^2b+3ab^2+b^3", "Identity"], ["x - (-y) = x + y", "Identity"], ["-1(x) = -x", "Identity"],
            
            // -------------------- Functions (20 Examples) --------------------
            ["f(x) = 2x + 3", "Function"], ["g(t) = t^2 - 1", "Function"], ["h(z) = \\frac{1}{z}", "Function"], ["y = 5x", "Function"],
            ["A(r) = \\pi r^2", "Function"], ["P(t) = 100e^{0.05t}", "Function"], ["L(m) = \\vert m \\vert", "Function"], ["s(d) = \\sqrt{d}", "Function"],
            ["y = \\frac{1}{2}x - 4", "Function"], ["f(x) = \\cos(x)", "Function"], ["g(a,b) = a + b", "Function"], ["f(x) = \\lfloor x \\rfloor", "Function"],
            ["H(p) = p^3", "Function"], ["V = \\frac{4}{3} \\pi r^3", "Function"], ["f(x) = \\ln(x)", "Function"], ["y = x^2", "Function"],
            ["h(t) = 4.9t^2", "Function"], ["C(n) = 10 + 2n", "Function"], ["g(x) = \\frac{x-1}{x+1}", "Function"], ["y = \\sqrt{25 - x^2}", "Function"],

            // -------------------- Expressions (20 Examples) --------------------
            ["x^3 + 2x^2 - 5", "Expression"], ["a + 2b - 3c", "Expression"], ["\\frac{y+7}{x}", "Expression"], ["-4mn^2", "Expression"],
            ["7p - 1", "Expression"], ["(x+5)(x-5)", "Expression"], ["\\frac{k}{k+1}", "Expression"], ["\\sqrt{r} + 2r", "Expression"],
            ["e^t - 1", "Expression"], ["\\frac{x}{y} + 2", "Expression"], ["\\sin(2\\theta)", "Expression"], ["a^2 + b^2", "Expression"],
            ["\\log_2(z)", "Expression"], ["10 - 2w", "Expression"], ["\\vert 3q \\vert", "Expression"], ["(x-1)^3", "Expression"],
            ["-x^2", "Expression"], ["\\frac{m+n}{mn}", "Expression"], ["5\\sqrt{3x}", "Expression"], ["(a+b+c)/3", "Expression"],
        ];
        
        // KEY SETTING: Only 20 questions are played per game session.
        const CARD_COUNT = 20; 
        const ALL_CARDS_LENGTH = ALL_CARDS.length; // 120 (total pool)

        let gameState = {
            score: 0,
            deck: [],
            currentCardIndex: 0,
            startTime: 0,
            timerInterval: null,
            isGameActive: false
        };

        // DOM elements
        let cardDisplay, statusMessage, timerDisplay, scoreDisplay, categoryButtonsContainer, startButton, gameInterface;

        // Initialize elements after the DOM is loaded
        window.onload = function() {
            cardDisplay = document.getElementById('card-display');
            statusMessage = document.getElementById('status-message');
            timerDisplay = document.getElementById('timer-display');
            scoreDisplay = document.getElementById('score-display');
            categoryButtonsContainer = document.getElementById('category-buttons');
            startButton = document.getElementById('start-button');
            gameInterface = document.getElementById('game-interface');

            // Set up event listeners
            startButton.addEventListener('click', startGame);
            
            // Initial UI setup
            resetUI();
            displayCategories();
        };

        function resetUI() {
            gameInterface.classList.add('hidden');
            startButton.classList.remove('hidden');
            statusMessage.textContent = `Challenge: Sort ${CARD_COUNT} random algebraic items by type!`;
            cardDisplay.innerHTML = '<span class="text-4xl text-pink-100">Card Loading...</span>';
            timerDisplay.textContent = '0.00s';
            scoreDisplay.textContent = `0/${CARD_COUNT}`;
        }

        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        }

        function startGame() {
            gameState.score = 0;
            gameState.currentCardIndex = 0;
            gameState.isGameActive = true;
            
            // DECK CREATION: Shuffle ALL 120 cards, but only take the first CARD_COUNT (20)
            let fullDeck = [...ALL_CARDS];
            shuffleArray(fullDeck);
            gameState.deck = fullDeck.slice(0, CARD_COUNT);

            // UI changes
            startButton.classList.add('hidden');
            gameInterface.classList.remove('hidden');
            scoreDisplay.textContent = `${gameState.score}/${CARD_COUNT}`;
            statusMessage.textContent = "Game started! Sort the expression below.";
            
            // Start Timer
            gameState.startTime = performance.now();
            clearInterval(gameState.timerInterval);
            gameState.timerInterval = setInterval(updateTimer, 100); 
            
            nextCard();
        }

        function updateTimer() {
            if (gameState.isGameActive) {
                const elapsed = (performance.now() - gameState.startTime) / 1000;
                timerDisplay.textContent = `${elapsed.toFixed(2)}s`;
            }
        }

        function displayCategories() {
            categoryButtonsContainer.innerHTML = ''; // Clear previous buttons
            CATEGORIES.forEach((category, index) => {
                const button = document.createElement('button');
                button.textContent = `${index + 1}. ${category}`; 
                button.className = 'w-full flex-1 bg-pink-600 hover:bg-pink-700 text-white font-semibold py-3 px-2 rounded-lg transition duration-300 shadow-md transform hover:scale-105 disabled:bg-gray-500 text-sm md:text-base';
                button.setAttribute('data-index', index);
                button.addEventListener('click', () => checkAnswer(index));
                categoryButtonsContainer.appendChild(button);
            });
            categoryButtonsContainer.className = 'grid grid-cols-3 md:grid-cols-6 gap-3';
        }
        
        function nextCard() {
            if (gameState.currentCardIndex >= CARD_COUNT) {
                endGame();
                return;
            }

            const [expression, correctCategory] = gameState.deck[gameState.currentCardIndex];
            
            // Display the current question number and the expression
            cardDisplay.innerHTML = `<span class="text-4xl font-bold text-white">Question ${gameState.currentCardIndex + 1} / ${CARD_COUNT}: &nbsp; ${expression}</span>`;
            
            // Rerender MathJax to process the new expression
            MathJax.Hub.Queue(["Typeset", MathJax.Hub, cardDisplay]);

            // Enable buttons
            categoryButtonsContainer.querySelectorAll('button').forEach(btn => btn.disabled = false);
        }

        function checkAnswer(userChoiceIndex) {
            if (!gameState.isGameActive) return;

            // Disable buttons temporarily
            categoryButtonsContainer.querySelectorAll('button').forEach(btn => btn.disabled = true);

            const [example, correctCategory] = gameState.deck[gameState.currentCardIndex];
            const userCategory = CATEGORIES[userChoiceIndex];

            if (userCategory === correctCategory) {
                gameState.score++;
                statusMessage.textContent = `✅ Correct! "${example}" is a(n) ${correctCategory}.`;
            } else {
                statusMessage.textContent = `❌ Incorrect. The correct answer was: ${correctCategory}.`;
            }

            scoreDisplay.textContent = `${gameState.score}/${CARD_COUNT}`;
            
            gameState.currentCardIndex++;
            
            // Short delay before moving to the next card
            setTimeout(nextCard, 1000); 
        }
        
        function endGame() {
            gameState.isGameActive = false;
            clearInterval(gameState.timerInterval);
            
            const totalTime = (performance.now() - gameState.startTime) / 1000;
            const finalMessage = `Game Over! You scored ${gameState.score}/${CARD_COUNT} in ${totalTime.toFixed(2)} seconds.`;

            // Display final message and 'Play Again' button
            statusMessage.textContent = finalMessage;
            cardDisplay.innerHTML = '<span class="text-4xl text-white font-bold">Great job completing the 20-question challenge!</span>';
            startButton.textContent = "Play Again";
            startButton.classList.remove('hidden');
            gameInterface.classList.add('hidden');
        }

        /* --- GAME LOGIC END (CARD_COUNT = 20 from 120-Card Pool) --- */
    </script>
</head>
<body class="font-sans bg-gradient-to-br from-[#1e1b4b] via-[#312e81] to-[#4c1d95] text-pink-100 min-h-screen antialiased">

    <nav class="bg-purple-900/50 backdrop-blur-md sticky top-0 z-50 border-b border-purple-700/50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <div class="flex items-center">
                    <a href="#" class="text-2xl font-bold text-white">
                        <span class="text-pink-400">{</span> Algebra Adventure <span class="text-pink-400">}</span>
                    </a>
                </div>
                <div class="hidden md:block">
                    <div class="ml-10 flex items-baseline space-x-4">
                        <a href="#home" class="bg-purple-800 text-white px-3 py-2 rounded-md text-sm font-medium">Home</a>
                        <a href="#game" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Game (20 Qs)</a>
                    </div>
                </div>
                <div class="-mr-2 flex md:hidden">
                    <button type="button" class="bg-purple-800 inline-flex items-center justify-center p-2 rounded-md text-pink-300 hover:text-white hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-purple-800 focus:ring-white">
                        <span class="sr-only">Open main menu</span>
                        <svg class="block h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                        </svg>
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">

        <section id="home" class="text-center mb-16 rounded-lg bg-purple-800/30 backdrop-blur-sm border border-purple-700/50 p-10">
            <h1 class="text-4xl md:text-6xl font-bold text-white mb-4">Welcome to <span class="text-pink-400">{</span> Algebra Adventure <span class="text-pink-400">}</span>!</h1>
            <p class="text-xl text-pink-200 mb-8">Ready for the **20-Question Challenge**? High variety, quick test!</p>
            <a href="#game" class="bg-pink-600 text-white hover:bg-pink-700 font-bold py-3 px-6 rounded-lg text-lg transition duration-300 shadow-lg hover:shadow-xl">
                Jump to Game
            </a>
        </section>

        <section id="lessons" class="mb-16">
            <h2 class="text-3xl font-bold text-white mb-6">Key Concepts</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Equations & Inequalities</h3>
                    <p class="text-pink-200">Statements showing equality <code class="text-pink-400 bg-purple-900 px-2 rounded">=</code> or comparison <code class="text-pink-400 bg-purple-900 px-2 rounded">>, <</code> between expressions.</p>
                </div>
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Expressions & Constants</h3>
                    <p class="text-pink-200">Expressions are groupings like <code class="text-pink-400 bg-purple-900 px-2 rounded">x² + 5</code>. Constants are fixed values like <code class="text-pink-400 bg-purple-900 px-2 rounded">9</code> or <code class="text-pink-400 bg-purple-900 px-2 rounded">π</code>.</p>
                </div>
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Functions & Identities</h3>
                    <p class="text-pink-200">Functions <code class="text-pink-400 bg-purple-900 px-2 rounded">f(x)</code> define relationships. Identities are always true, e.g., <code class="text-pink-400 bg-purple-900 px-2 rounded">x/x = 1</code>.</p>
                </div>
            </div>
        </section>

        <section id="game" class="bg-purple-900/70 backdrop-blur-lg border border-purple-700 rounded-lg shadow-2xl p-6 md:p-8 text-center">
            <h2 class="text-3xl font-bold text-white mb-4">Algebra Sorting Challenge (20 Questions!)</h2>
            <p id="status-message" class="text-lg text-pink-200 mb-6 transition duration-500">
                Challenge: Sort 20 random algebraic items by type! (120 card pool)
            </p>
            
            <div class="flex justify-center space-x-8 mb-8">
                <div class="text-lg font-semibold text-purple-300">
                    Score: <span id="score-display" class="text-pink-400">0/20</span>
                </div>
                <div class="text-lg font-semibold text-purple-300">
                    Time: <span id="timer-display" class="text-pink-400">0.00s</span>
                </div>
            </div>

            <button id="start-button" class="w-full max-w-sm bg-red-600 text-white hover:bg-red-700 font-bold py-4 px-6 rounded-lg text-xl transition duration-300 shadow-lg hover:shadow-xl">
                Start Game
            </button>
            
            <div id="game-interface" class="hidden">
                
                <div id="card-display" class="bg-purple-700/50 border-2 border-pink-400 rounded-xl p-8 mb-8 flex justify-center items-center h-28">
                    <span class="text-4xl text-white font-bold">
                        Card Loading...
                    </span>
                </div>

                <div id="category-buttons" class="grid grid-cols-3 md:grid-cols-6 gap-3">
                    </div>
            </div>

        </section>

    </div>

    <footer class="text-center py-6 border-t border-purple-700/50 mt-16">
        <p class="text-purple-300">&copy; 2025 Algebra Adventure. All rights reserved.</p>
    </footer>

</body>
</html>
