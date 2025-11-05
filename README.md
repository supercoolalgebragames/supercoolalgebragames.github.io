# supercoolalgebragames.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Algebra Adventure</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- MathJax for rendering beautiful algebraic expressions -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML"></script>
    <!-- Google Font: Inter -->
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
                        // Extending the palette to make your colors easier to use
                        theme: {
                            purple: '#4c1d95', // deep purple
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

        /* --- GAME LOGIC START --- */

        // Updated Game Data: Removed Monomial, Binomial, and Trinomial
        const CATEGORIES = ["Equation", "Inequality", "Constant"];
        // Format: [Algebraic Expression (LaTeX/MathJax), Correct Category]
        const ALL_CARDS = [
            // Equations (Has an '=' sign)
            ["4x - 1 = 15", "Equation"], ["a + b = c", "Equation"], ["\\frac{y}{2} = 10", "Equation"],
            // Inequalities (Has <, >, \\le, or \\ge)
            ["3z + 1 > 7", "Inequality"], ["x - 5 \\le 20", "Inequality"], ["2y < 12", "Inequality"],
            // Constants (Number without a variable)
            ["10", "Constant"], ["-3.5", "Constant"], ["\\pi", "Constant"],
        ];
        
        const CARD_COUNT = ALL_CARDS.length; // Max cards is now 9

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
            statusMessage.textContent = "Click 'Start Game' to test your algebra vocabulary!";
            // Updated: Simple text display for loading
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
            
            // Create the deck (shuffle and take all available cards)
            shuffleArray(ALL_CARDS);
            gameState.deck = ALL_CARDS.slice(0, CARD_COUNT);

            // UI changes
            startButton.classList.add('hidden');
            gameInterface.classList.remove('hidden');
            scoreDisplay.textContent = `${gameState.score}/${CARD_COUNT}`;
            statusMessage.textContent = "Game started! Sort the expression below.";
            
            // Start Timer
            gameState.startTime = performance.now();
            clearInterval(gameState.timerInterval);
            gameState.timerInterval = setInterval(updateTimer, 100); // Update timer every 100ms
            
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
                // Use index + 1 for user-friendly display (1-3)
                button.textContent = `${index + 1}. ${category}`; 
                button.className = 'w-full md:w-auto flex-1 bg-pink-600 hover:bg-pink-700 text-white font-semibold py-3 px-4 rounded-lg transition duration-300 shadow-md transform hover:scale-105 disabled:bg-gray-500';
                button.setAttribute('data-index', index);
                button.addEventListener('click', () => checkAnswer(index));
                categoryButtonsContainer.appendChild(button);
            });
        }
        
        function nextCard() {
            if (gameState.currentCardIndex >= CARD_COUNT) {
                endGame();
                return;
            }

            const [expression, correctCategory] = gameState.deck[gameState.currentCardIndex];
            
            // REMOVED $ delimiters, relying on MathJax's ability to render the content without explicit delimiters.
            cardDisplay.innerHTML = `<span class="text-4xl font-bold text-white">Card ${gameState.currentCardIndex + 1} / ${CARD_COUNT}: &nbsp; ${expression}</span>`;
            
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
            // Updated: Simple text display for game over
            cardDisplay.innerHTML = '<span class="text-4xl text-white font-bold">Great job completing the challenge!</span>';
            startButton.textContent = "Play Again";
            startButton.classList.remove('hidden');
            gameInterface.classList.add('hidden');
        }

        /* --- GAME LOGIC END --- */
    </script>
</head>
<body class="font-sans bg-gradient-to-br from-[#1e1b4b] via-[#312e81] to-[#4c1d95] text-pink-100 min-h-screen antialiased">

    <!-- Header Navigation -->
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
                        <a href="#lessons" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Lessons</a>
                        <a href="#puzzles" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Puzzles</a>
                        <a href="#game" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Game</a>
                    </div>
                </div>
                <!-- Mobile Menu Button (optional) -->
                <div class="-mr-2 flex md:hidden">
                    <button type="button" class="bg-purple-800 inline-flex items-center justify-center p-2 rounded-md text-pink-300 hover:text-white hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-purple-800 focus:ring-white">
                        <span class="sr-only">Open main menu</span>
                        <!-- Icon for menu (hamburger) -->
                        <svg class="block h-6 w-6" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                        </svg>
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Content Area -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">

        <!-- Hero Section -->
        <section id="home" class="text-center mb-16 rounded-lg bg-purple-800/30 backdrop-blur-sm border border-purple-700/50 p-10">
            <h1 class="text-4xl md:text-6xl font-bold text-white mb-4">Welcome to <span class="text-pink-400">{</span> Algebra Adventure <span class="text-pink-400">}</span>!</h1>
            <p class="text-xl text-pink-200 mb-8">Your new home for exploring variables, equations, and functions.</p>
            <a href="#lessons" class="bg-pink-600 text-white hover:bg-pink-700 font-bold py-3 px-6 rounded-lg text-lg transition duration-300 shadow-lg hover:shadow-xl">
                Start Learning
            </a>
        </section>

        <!-- Lessons Section -->
        <section id="lessons" class="mb-16">
            <h2 class="text-3xl font-bold text-white mb-6">Key Concepts</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- Card 1 -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Variables</h3>
                    <p class="text-pink-200">Learn all about the mysterious 'x' and 'y' and how they represent unknown numbers.</p>
                </div>
                <!-- Card 2 -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Equations</h3>
                    <p class="text-pink-200">Master the art of balancing equations to find the solution. Like <code class="text-pink-400 bg-purple-900 px-2 py-1 rounded">2x + 5 = 15</code>!</p>
                </div>
                <!-- Card 3 -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-purple-700 rounded-lg shadow-lg p-6 hover:shadow-pink-500/20 hover:border-pink-500 transition-all">
                    <h3 class="text-2xl font-semibold text-pink-300 mb-3">Functions</h3>
                    <p class="text-pink-200">Discover how functions like <code class="text-pink-400 bg-purple-900 px-2 py-1 rounded">f(x) = x^2</code> create predictable patterns.</p>
                </div>
            </div>
        </section>

        <!-- Puzzles Section -->
        <section id="puzzles" class="mb-16">
            <h2 class="text-3xl font-bold text-white mb-6">Solve the Puzzles!</h2>
            <p class="text-lg text-pink-200 mb-8">Put your new skills to the test with these quick challenges. Hover over the solution button to reveal the answer!</p>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                
                <!-- Puzzle 1: Linear Equation -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-red-500 rounded-lg shadow-lg p-6">
                    <h3 class="text-2xl font-semibold text-red-400 mb-3">Puzzle 1: Find X</h3>
                    <p class="text-pink-100 text-xl font-bold mb-4">
                        If <span class="text-pink-300">3x + 7 = 25</span>, what is the value of x?
                    </p>
                    <div class="relative group">
                        <button class="w-full bg-red-600 text-white font-bold py-2 px-4 rounded-lg transition duration-300 hover:bg-red-700">
                            Show Solution
                        </button>
                        <div class="absolute inset-0 flex items-center justify-center bg-red-900/90 text-white text-2xl font-extrabold rounded-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none">
                            x = 6
                        </div>
                    </div>
                </div>

                <!-- Puzzle 2: Substitution -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-red-500 rounded-lg shadow-lg p-6">
                    <h3 class="text-2xl font-semibold text-red-400 mb-3">Puzzle 2: Substitute</h3>
                    <p class="text-pink-100 text-xl font-bold mb-4">
                        If <span class="text-pink-300">a = 5</span> and <span class="text-pink-300">b = 2a - 3</span>, what is b²?
                    </p>
                    <div class="relative group">
                        <button class="w-full bg-red-600 text-white font-bold py-2 px-4 rounded-lg transition duration-300 hover:bg-red-700">
                            Show Solution
                        </button>
                        <div class="absolute inset-0 flex items-center justify-center bg-red-900/90 text-white text-2xl font-extrabold rounded-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none">
                            b² = 49
                        </div>
                    </div>
                </div>

                <!-- Puzzle 3: Function Evaluation -->
                <div class="bg-purple-800/60 backdrop-blur-sm border border-red-500 rounded-lg shadow-lg p-6">
                    <h3 class="text-2xl font-semibold text-red-400 mb-3">Puzzle 3: The Function</h3>
                    <p class="text-pink-100 text-xl font-bold mb-4">
                        If <span class="text-pink-300">f(x) = x² + 2x + 1</span>, what is f(5)?
                    </p>
                    <div class="relative group">
                        <button class="w-full bg-red-600 text-white font-bold py-2 px-4 rounded-lg transition duration-300 hover:bg-red-700">
                            Show Solution
                        </button>
                        <div class="absolute inset-0 flex items-center justify-center bg-red-900/90 text-white text-2xl font-extrabold rounded-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none">
                            f(5) = 36
                        </div>
                    </div>
                </div>

            </div>
        </section>


        <!-- Interactive Game Section -->
        <section id="game" class="bg-purple-900/70 backdrop-blur-lg border border-purple-700 rounded-lg shadow-2xl p-6 md:p-8 text-center">
            <h2 class="text-3xl font-bold text-white mb-4">Algebra Sorting Challenge (Playable!)</h2>
            <p id="status-message" class="text-lg text-pink-200 mb-6 transition duration-500">
                Click 'Start Game' to test your algebra vocabulary!
            </p>
            
            <!-- Game Stats -->
            <div class="flex justify-center space-x-8 mb-8">
                <div class="text-lg font-semibold text-purple-300">
                    Score: <span id="score-display" class="text-pink-400">0/9</span>
                </div>
                <div class="text-lg font-semibold text-purple-300">
                    Time: <span id="timer-display" class="text-pink-400">0.00s</span>
                </div>
            </div>

            <!-- Start Button -->
            <button id="start-button" class="w-full max-w-sm bg-red-600 text-white hover:bg-red-700 font-bold py-4 px-6 rounded-lg text-xl transition duration-300 shadow-lg hover:shadow-xl">
                Start Game
            </button>
            
            <!-- Game Interface (Hidden until started) -->
            <div id="game-interface" class="hidden">
                
                <!-- Card Display -->
                <div id="card-display" class="bg-purple-700/50 border-2 border-pink-400 rounded-xl p-8 mb-8 flex justify-center items-center h-28">
                    <!-- The expression is now plain text -->
                    <span class="text-4xl text-white font-bold">
                        Card Loading...
                    </span>
                </div>

                <!-- Category Buttons -->
                <div id="category-buttons" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <!-- Buttons will be generated by JavaScript -->
                </div>
            </div>

        </section>

    </div>

    <!-- Footer -->
    <footer class="text-center py-6 border-t border-purple-700/50 mt-16">
        <p class="text-purple-300">&copy; 2025 Algebra Adventure. All rights reserved.</p>
    </footer>

</body>
</html>
