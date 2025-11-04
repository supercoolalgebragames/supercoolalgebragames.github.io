# supercoolalgebragames.github.io
<html>
<javascript>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Algebra Adventure</title>
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

        // Game Data (Converted from Python)
        
        // **FIX 1: Updated CATEGORIES to match the PDF**
        const CATEGORIES = ["Identity", "Expression", "Term", "Inequality", "Equation", "Formula"];
        
        // **FIX 2: Updated ALL_CARDS to match new categories (24 total, >20 min)**
        // Format: [Algebraic Expression (LaTeX/MathJax), Correct Category]
        const ALL_CARDS = [
            // Identities
            ["2(x + 3) = 2x + 6", "Identity"],
            ["a^2 - b^2 = (a-b)(a+b)", "Identity"],
            ["(x+1)^2 = x^2 + 2x + 1", "Identity"],
            // Expressions
            ["3x + 2y - 5", "Expression"],
            ["4a^2", "Expression"],
            ["\\frac{x+1}{2}", "Expression"],
            ["5(y - 3)", "Expression"],
            // Terms
            ["5x^2", "Term"],
            ["-3y", "Term"],
            ["7", "Term"],
            ["ab", "Term"],
            // Inequalities
            ["x + 5 > 10", "Inequality"],
            ["3y - 2 \\le 7", "Inequality"],
            ["4a < 20", "Inequality"],
            ["b \\ge -1", "Inequality"],
            // Equations
            ["3x - 1 = 11", "Equation"],
            ["5a = 30", "Equation"],
            ["y^2 = 25", "Equation"],
            ["\\frac{x}{2} + 1 = 5", "Equation"],
            // Formulas
            ["A = \\pi r^2", "Formula"],
            ["C = 2\\pi r", "Formula"],
            ["F = ma", "Formula"],
            ["V = lwh", "Formula"],
            ["E = mc^2", "Formula"]
        ];
       
        const CARD_COUNT = 10; // Number of cards per round

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
            cardDisplay.innerHTML = '$\\text{Card Loading...}$';
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
           
            // Create the deck (shuffle and take the first 10)
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
                // Use index + 1 for user-friendly display (1-6)
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
           
            // Display the expression using MathJax notation
            cardDisplay.innerHTML = `$$\\text{Card ${gameState.currentCardIndex + 1} / ${CARD_COUNT}:} \\quad ${expression}$$`;
           
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
            cardDisplay.innerHTML = '$$\\text{Great job completing the challenge!}$$';
            startButton.textContent = "Play Again";
            startButton.classList.remove('hidden');
            gameInterface.classList.add('hidden');
        }

        /* --- GAME LOGIC END --- */
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
                        <a href="#lessons" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Lessons</a>
                        <a href="#puzzles" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Puzzles</a>
                        <a href="#game" class="text-pink-200 hover:bg-purple-700 hover:text-white px-3 py-2 rounded-md text-sm font-medium">Game</a>
                    </div>
                </div>
                <div class="-mr-2 flex md:hidden">
                    <button type="button" class="bg-purple-800 inline-flex items-center justify-center p-2 rounded-md text-pink-300 hover:text-white hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-purple-800 focus:ring-white">
                        <span class="sr-only">Open main menu</span>
                        <svg class="block h-6 w
<javascript>
<html>
