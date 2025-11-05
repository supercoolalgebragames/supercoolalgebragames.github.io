<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Algebra Adventure</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
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
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        /* Modern gradient background with animated mesh */
        @keyframes gradient-shift {
            0%, 100% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
        }
        
        .animated-gradient {
            background: linear-gradient(-45deg, #1a0b2e, #2d1b4e, #4a1d6f, #6b2d8f);
            background-size: 400% 400%;
            animation: gradient-shift 15s ease infinite;
            position: fixed;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            z-index: -1;
        }
        
        /* Glassmorphism effect */
        .glass {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .glass-strong {
            background: rgba(139, 92, 246, 0.1);
            backdrop-filter: blur(30px);
            border: 1px solid rgba(236, 72, 153, 0.3);
        }
        
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 10px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(30, 27, 58, 0.5);
        }
        ::-webkit-scrollbar-thumb {
            background: linear-gradient(180deg, #ec4899, #a855f7);
            border-radius: 5px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: linear-gradient(180deg, #f472b6, #c084fc);
        }

        /* Smooth animations */
        .fade-in {
            animation: fadeIn 0.6s ease-out;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        /* Floating animation for hero section */
        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
        }
        
        .float {
            animation: float 3s ease-in-out infinite;
        }
        
        /* Glow effect */
        .glow {
            box-shadow: 0 0 30px rgba(236, 72, 153, 0.3);
        }
        
        .glow-hover:hover {
            box-shadow: 0 0 40px rgba(236, 72, 153, 0.5);
            transform: translateY(-2px);
        }

        .main-content-wrapper {
            position: relative;
            z-index: 10;
            min-height: 100vh;
        }
        
        /* Ensure MathJax content is centered and large */
        #puzzle-equation .MathJax_Display {
            text-align: center !important;
            font-size: 1.5em; /* Ensure the text is large */
        }
    </style>
    <script>
        /* --- GAME LOGIC START --- */

        const CATEGORIES = ["Equation", "Inequality", "Expression", "Constant", "Identity", "Function"];
        
        const ALL_CARDS = [
            // (Card data for the sorting game remains unchanged)
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
            ["\\log(xy) = \\log(x) + \\log(y)", "Identity"], ["x^0 = 1", "Identity"], ["\\tan(\\theta) = \\frac{\sin(\\theta)}{\\cos(\\theta)}", "Identity"], ["x + 0 = x", "Identity"],
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
        
        const ALL_PUZZLES = [
            ["2x + 3 = 11", "4"], ["5(x - 2) = 15", "5"], ["\\frac{x}{4} = 3", "12"], ["x - 7 = 3", "10"],
            ["3x - 5 = 10", "5"], ["\\frac{x}{2} + 1 = 7", "12"], ["4x = 20", "5"], ["x + 9 = 12", "3"],
            ["2(x + 1) = 8", "3"], ["6x - 2 = 22", "4"], ["10 - x = 7", "3"], ["\\frac{x}{3} - 1 = 2", "9"],
            ["5x + 1 = 16", "3"], ["2x - 8 = 0", "4"], ["-3x = 18", "-6"], ["\\frac{x}{5} = 5", "25"],
            ["x + 4 = 4", "0"], ["2x + 7 = 1", "-3"], ["3(x - 3) = 6", "5"], ["\\frac{2x}{3} = 4", "6"],
            ["4x - 1 = 7", "2"], ["15 - x = 10", "5"], ["x + 2.5 = 5", "2.5"], ["2x + 1 = 0", "-0.5"],
            ["7x = 49", "7"], ["\\frac{x}{10} = 2", "20"], ["x - 11 = -1", "10"], ["3x + 4 = 13", "3"],
            ["5x = 0", "0"], ["\\frac{x}{2} = -8", "-4"], ["2(x + 5) = 14", "2"], ["4 - x = 1", "3"],
            ["10x = 100", "10"], ["x - 1 = 9", "10"], ["6x = 18", "3"], ["\\frac{x}{4} + 1 = 3", "8"],
            ["2x - 3 = 5", "4"], ["3x + 1 = 10", "3"], ["5x - 10 = 5", "3"], ["7x + 2 = 16", "2"],
            ["x - 5 = -8", "-3"], ["-2x = -10", "5"], ["\\frac{x}{6} = 3", "18"], ["8x - 8 = 8", "2"],
            ["3(x + 2) = 15", "3"], ["\\frac{x}{2} - 3 = 0", "6"], ["4x + 5 = 13", "2"], ["9x = 81", "9"],
            ["x + 12 = 10", "-2"], ["2x - 5 = -1", "2"]
        ];

        const CARD_COUNT = 20;

        let gameState = {
            score: 0,
            deck: [],
            currentCardIndex: 0,
            startTime: 0,
            timerInterval: null,
            isGameActive: false
        };

        let currentPuzzle = {
            equation: "",
            answer: ""
        };

        let cardDisplay, statusMessage, timerDisplay, scoreDisplay, categoryButtonsContainer, startButton, gameInterface;
        
        let puzzleInput, puzzleCheckBtn, puzzleFeedback, puzzleEquation;

        window.onload = function() {
            // Game Elements
            cardDisplay = document.getElementById('card-display');
            statusMessage = document.getElementById('status-message');
            timerDisplay = document.getElementById('timer-display');
            scoreDisplay = document.getElementById('score-display');
            categoryButtonsContainer = document.getElementById('category-buttons');
            startButton = document.getElementById('start-button');
            gameInterface = document.getElementById('game-interface');
            
            // Puzzle Elements
            puzzleInput = document.getElementById('puzzle-input');
            puzzleCheckBtn = document.getElementById('puzzle-check-btn');
            puzzleFeedback = document.getElementById('puzzle-feedback');
            puzzleEquation = document.getElementById('puzzle-equation');

            // Game Listener
            startButton.addEventListener('click', startGame);
            
            // Puzzle Listener
            if (puzzleCheckBtn) {
                puzzleCheckBtn.addEventListener('click', checkPuzzleAnswer);
            }
            
            resetUI();
            displayCategories();

            // Load the first puzzle AFTER all elements are defined
            loadNextPuzzle(); 
        };

        function resetUI() {
            gameInterface.classList.add('hidden');
            startButton.classList.remove('hidden');
            statusMessage.textContent = `Challenge: Sort ${CARD_COUNT} random algebraic items by type!`;
            cardDisplay.innerHTML = '<span class="text-3xl text-purple-100">Ready to start...</span>';
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
            
            let fullDeck = [...ALL_CARDS];
            shuffleArray(fullDeck);
            gameState.deck = fullDeck.slice(0, CARD_COUNT);

            startButton.classList.add('hidden');
            gameInterface.classList.remove('hidden');
            scoreDisplay.textContent = `${gameState.score}/${CARD_COUNT}`;
            statusMessage.textContent = "Game started! Classify the expression below.";
            
            gameState.startTime = performance.now();
            clearInterval(gameState.timerInterval);
            gameState.timerInterval = setInterval(updateTimer, 100); 
            
            nextCard(); // Start the sorting game
        }

        function updateTimer() {
            if (gameState.isGameActive) {
                const elapsed = (performance.now() - gameState.startTime) / 1000;
                timerDisplay.textContent = `${elapsed.toFixed(2)}s`;
            }
        }

        function displayCategories() {
            categoryButtonsContainer.innerHTML = '';
            CATEGORIES.forEach((category, index) => {
                const button = document.createElement('button');
                button.textContent = category;
                button.className = 'glass-strong text-white font-semibold py-3 px-4 rounded-xl transition-all duration-300 hover:scale-105 hover:bg-pink-500/30 disabled:opacity-50 disabled:cursor-not-allowed text-sm md:text-base glow-hover';
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
            
            // 1. Wrap the expression in $$...$$ for MathJax display mode
            cardDisplay.innerHTML = `<div class="text-center"><div class="text-sm text-purple-300 mb-2">Question ${gameState.currentCardIndex + 1} of ${CARD_COUNT}</div><div class="text-4xl font-bold text-white">$$${expression}$$</div></div>`;
            
            // 2. Queue MathJax to process the newly inserted content in cardDisplay
            MathJax.Hub.Queue(["Typeset", MathJax.Hub, cardDisplay]);

            categoryButtonsContainer.querySelectorAll('button').forEach(btn => btn.disabled = false);
        }

        function checkAnswer(userChoiceIndex) {
            if (!gameState.isGameActive) return;

            categoryButtonsContainer.querySelectorAll('button').forEach(btn => btn.disabled = true);

            const [example, correctCategory] = gameState.deck[gameState.currentCardIndex];
            const userCategory = CATEGORIES[userChoiceIndex];

            if (userCategory === correctCategory) {
                gameState.score++;
                statusMessage.textContent = `Correct! It's a ${correctCategory}.`;
                statusMessage.className = 'text-lg text-green-300 mb-6 transition-all duration-500 font-medium';
            } else {
                statusMessage.textContent = `Not quite. The correct answer was: ${correctCategory}.`;
                statusMessage.className = 'text-lg text-red-300 mb-6 transition-all duration-500 font-medium';
            }

            scoreDisplay.textContent = `${gameState.score}/${CARD_COUNT}`;
            
            gameState.currentCardIndex++;
            
            setTimeout(nextCard, 800);
        }
        
        function endGame() {
            gameState.isGameActive = false;
            clearInterval(gameState.timerInterval);
            
            const totalTime = (performance.now() - gameState.startTime) / 1000;
            const percentage = Math.round((gameState.score / CARD_COUNT) * 100);

            statusMessage.textContent = `Game Complete! You scored ${gameState.score}/${CARD_COUNT} (${percentage}%) in ${totalTime.toFixed(2)} seconds!`;
            statusMessage.className = 'text-lg text-purple-200 mb-6 transition-all duration-500 font-medium';
            cardDisplay.innerHTML = `<div class="text-center"><div class="text-2xl text-white font-bold">${percentage >= 80 ? 'Excellent Work!' : percentage >= 60 ? 'Good Job!' : 'Keep Practicing!'}</div></div>`;
            startButton.textContent = "Play Again";
            startButton.classList.remove('hidden');
            gameInterface.classList.add('hidden');
        }

        function loadNextPuzzle() {
            if (!puzzleEquation || !puzzleInput || !puzzleFeedback) return;

            const [equation, answer] = ALL_PUZZLES[Math.floor(Math.random() * ALL_PUZZLES.length)];
            
            currentPuzzle.equation = equation;
            currentPuzzle.answer = answer;

            puzzleEquation.innerHTML = `$$${equation}$$`;
            
            // Queue MathJax to process the puzzle equation element
            MathJax.Hub.Queue(["Typeset", MathJax.Hub, puzzleEquation]);
            
            puzzleInput.value = "";
            puzzleFeedback.textContent = "";
            puzzleFeedback.className = 'text-lg text-purple-200 mt-6 h-6 transition-all duration-300';
        }

        function checkPuzzleAnswer() {
            if (!puzzleInput || !puzzleFeedback) return;
            
            const userAnswer = puzzleInput.value.trim();
            const correctAnswer = currentPuzzle.answer;
            
            if (userAnswer === correctAnswer) {
                puzzleFeedback.textContent = `Correct! The answer is ${correctAnswer}. Loading next puzzle...`;
                puzzleFeedback.className = 'text-lg text-green-300 mt-6 h-6 transition-all duration-300';
                
                setTimeout(loadNextPuzzle, 1500);

            } else if (userAnswer === "") {
                puzzleFeedback.textContent = "Please enter a value.";
                puzzleFeedback.className = 'text-lg text-yellow-300 mt-6 h-6 transition-all duration-300';
            } else {
                puzzleFeedback.textContent = "Not quite. Try again!";
                puzzleFeedback.className = 'text-lg text-red-300 mt-6 h-6 transition-all duration-300';
                
                puzzleInput.value = "";
            }
            
            if (userAnswer !== correctAnswer) {
                setTimeout(() => {
                    if(puzzleFeedback) {
                        puzzleFeedback.textContent = "";
                        puzzleFeedback.className = 'text-lg text-purple-200 mt-6 h-6 transition-all duration-300';
                    }
                }, 3500);
            }
        }

    </script>
</head>
<body class="font-sans text-purple-50 antialiased">

    <div class="animated-gradient"></div>

    <div class="main-content-wrapper">

        <nav class="glass sticky top-0 z-50 border-b border-purple-500/20">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex items-center justify-between h-16">
                    <div class="flex items-center">
                        <a href="#" class="text-2xl font-bold text-white flex items-center gap-2">
                            <span>Algebra Adventure</span>
                        </a>
                    </div>
                    <div class="hidden md:flex items-center space-x-1">
                        <a href="#home" class="text-purple-200 hover:text-white hover:bg-white/10 px-4 py-2 rounded-lg text-sm font-medium transition-all">Home</a>
                        <a href="#lessons" class="text-purple-200 hover:text-white hover:bg-white/10 px-4 py-2 rounded-lg text-sm font-medium transition-all">Lessons</a>
                        <a href="#puzzles" class="text-purple-200 hover:text-white hover:bg-white/10 px-4 py-2 rounded-lg text-sm font-medium transition-all">Puzzles</a>
                        <a href="#game" class="bg-gradient-to-r from-pink-500 to-purple-600 text-white px-4 py-2 rounded-lg text-sm font-medium hover:from-pink-600 hover:to-purple-700 transition-all glow">Play Game</a>
                    </div>
                </div>
            </div>
        </nav>

        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 md:py-20">

            <section id="home" class="text-center mb-20 fade-in">
                <div class="float mb-8">
                    <div class="inline-block text-7xl md:text-9xl mb-6"></div>
                </div>
                <h1 class="text-5xl md:text-7xl font-extrabold text-white mb-6 bg-gradient-to-r from-pink-400 via-purple-400 to-indigo-400 bg-clip-text text-transparent">
                    Algebra Adventure
                </h1>
                <p class="text-xl md:text-2xl text-purple-200 mb-10 max-w-2xl mx-auto font-light">
                    Master algebraic concepts through interactive challenges. Test your skills with 20 questions from a pool of 120!
                </p>
                <a href="#game" class="inline-block bg-gradient-to-r from-pink-500 to-purple-600 text-white font-bold py-4 px-8 rounded-2xl text-lg transition-all duration-300 hover:scale-105 glow">
                    Start Challenge →
                </a>
            </section>

            <section id="lessons" class="mb-20 fade-in">
                <h2 class="text-4xl font-bold text-white mb-10 text-center">Key Concepts</h2>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div class="glass-strong rounded-2xl p-8 hover:scale-105 transition-all duration-300 glow-hover">
                        <div class="text-4xl mb-4"></div>
                        <h3 class="text-2xl font-bold text-pink-300 mb-4">Equations & Inequalities</h3>
                        <p class="text-purple-200 leading-relaxed">Statements that show relationships between expressions using equality or comparison operators.</p>
                    </div>
                    <div class="glass-strong rounded-2xl p-8 hover:scale-105 transition-all duration-300 glow-hover">
                        <div class="text-4xl mb-4"></div>
                        <h3 class="text-2xl font-bold text-pink-300 mb-4">Expressions & Constants</h3>
                        <p class="text-purple-200 leading-relaxed">Mathematical phrases combining numbers and variables, alongside fixed numerical values.</p>
                    </div>
                    <div class="glass-strong rounded-2xl p-8 hover:scale-105 transition-all duration-300 glow-hover">
                        <div class="text-4xl mb-4"></div>
                        <h3 class="text-2xl font-bold text-pink-300 mb-4">Functions & Identities</h3>
                        <p class="text-purple-200 leading-relaxed">Mappings between variables and equations that hold true for all values.</p>
                    </div>
                </div>
            </section>
            
            <section id="puzzles" class="mb-20 fade-in">
                <h2 class="text-4xl font-bold text-white mb-10 text-center">Algebra Puzzle</h2>
                <div class="glass-strong rounded-2xl p-8 md:p-12 text-center glow">
                    <div class="text-6xl mb-6"></div>
                    <h3 class="text-2xl font-bold text-pink-300 mb-4">Solve for \(x\)!</h3>
                    <p class="text-xl text-purple-100 mb-6 max-w-2xl mx-auto">
                        Can you find the value of \(x\) in the equation below?
                    </p>
                    
                    <div id="puzzle-equation" class="glass rounded-xl p-6 mb-6 min-h-[80px] flex justify-center items-center text-4xl font-bold text-white">
                        Loading puzzle...
                    </div>
                    
                    <div class="flex flex-col md:flex-row justify-center items-center gap-4 max-w-md mx-auto">
                        <input type="text" id="puzzle-input" class="w-full md:w-1/2 bg-white/10 border border-purple-400 text-white text-lg rounded-lg p-3 text-center focus:ring-pink-500 focus:border-pink-500" placeholder="Enter your answer">
                        <button id="puzzle-check-btn" class="w-full md:w-1/2 bg-gradient-to-r from-pink-500 to-purple-600 text-white font-bold py-3 px-6 rounded-lg text-lg transition-all duration-300 hover:scale-105 glow">
                            Check Answer
                        </button>
                    </div>
                    
                    <p id="puzzle-feedback" class="text-lg text-purple-200 mt-6 h-6 transition-all duration-300"></p> 
                </div>
            </section>

            <section id="game" class="glass-strong rounded-3xl p-8 md:p-12 text-center fade-in glow">
                <h2 class="text-4xl md:text-5xl font-bold text-white mb-4">Sorting Challenge</h2>
                <p id="status-message" class="text-lg text-purple-200 mb-8 transition-all duration-500 font-medium">
                    Challenge: Sort 20 random algebraic items by type!
                </p>
                
                <div class="flex justify-center gap-8 mb-10">
                    <div class="glass rounded-xl px-6 py-3">
                        <div class="text-sm text-purple-300 mb-1">Score</div>
                        <div id="score-display" class="text-2xl font-bold text-pink-400">0/20</div>
                    </div>
                    <div class="glass rounded-xl px-6 py-3">
                        <div class="text-sm text-purple-300 mb-1">Time</div>
                        <div id="timer-display" class="text-2xl font-bold text-pink-400">0.00s</div>
                    </div>
                </div>

                <button id="start-button" class="w-full max-w-md bg-gradient-to-r from-pink-500 to-purple-600 text-white font-bold py-4 px-8 rounded-2xl text-xl transition-all duration-300 hover:scale-105 glow mb-8">
                    Start Game
                </button>
                
                <div id="game-interface" class="hidden">
                    
                    <div id="card-display" class="glass-strong rounded-2xl p-10 mb-8 min-h-[140px] flex justify-center items-center glow">
                        <span class="text-3xl text-white font-bold">
                            Ready to start...
                        </span>
                    </div>

                    <div id="category-buttons" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-3">
                    </div>
                </div>

            </section>

        </div>

        <footer class="text-center py-8 border-t border-purple-500/20 mt-20 glass">
            <p class="text-purple-300">its a me mario. painstakingly bugfixed</p>
        </footer>

    </div>

</body>
</html>
