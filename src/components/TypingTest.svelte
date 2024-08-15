<script>
    import { onMount } from 'svelte';

    let paragraphs = [
        "Their politician was, in this moment, a notour paperback. The first armless grouse is, in its own way, a gear. The coat is a wash. However, a cake is the llama of a caravan. Snakelike armies show us how playgrounds can be viscoses. Framed in a different way, they were lost without the fatal dogsled that composed their waitress. Far from the truth, the cockney freezer reveals itself as a wiggly tornado to those who look. The first hawklike sack.",
        "Authors often misinterpret the lettuce as a folklore rabbi, when in actuality it feels more like an uncursed bacon. Pursued distances show us how mother-in-laws can be charleses. Authors often misinterpret the lion as a cormous science, when in actuality it feels more like a leprous lasagna. Recent controversy aside, their band was, in this moment, a racemed suit. The clutch of a joke becomes a togaed chair. The first pickled chess is.",
        // Add other paragraphs here
    ];

    let typingText = '';
    let inpField = '';
    let timeLeft = 60;
    let mistakes = 0;
    let wpm = 0;
    let cpm = 0;
    let charIndex = 0;
    let isTyping = false;
    let timer;

    // Initialize the game
    function loadParagraph() {
        const ranIndex = Math.floor(Math.random() * paragraphs.length);
        typingText = paragraphs[ranIndex];
        charIndex = 0;
        mistakes = 0;
        isTyping = false;
        timeLeft = 60;
        clearInterval(timer);
        wpm = 0;
        cpm = 0;
        updateTextDisplay();
    }

    // Update the display of typing text
    function updateTextDisplay() {
        const paragraphElement = document.querySelector(".typing-text");
        paragraphElement.innerHTML = typingText.split('').map(char => `<span>${char}</span>`).join('');
        paragraphElement.querySelectorAll('span')[0].classList.add('active');
    }

    // Handle typing input
    function handleInput(event) {
        let typedChar = inpField[charIndex];
        let characters = document.querySelectorAll(".typing-text span");

        if (charIndex < characters.length && timeLeft > 0) {
            if (!isTyping) {
                timer = setInterval(updateTimer, 1000);
                isTyping = true;
            }

            if (typedChar == null) {
                if (charIndex > 0) {
                    charIndex--;
                    if (characters[charIndex].classList.contains("incorrect")) {
                        mistakes--;
                    }
                    characters[charIndex].classList.remove("correct", "incorrect");
                }
            } else {
                if (characters[charIndex].innerText === typedChar) {
                    characters[charIndex].classList.add("correct");
                } else {
                    mistakes++;
                    characters[charIndex].classList.add("incorrect");
                }
                charIndex++;
            }

            characters.forEach(span => span.classList.remove("active"));
            if (characters[charIndex]) {
                characters[charIndex].classList.add("active");
            }

            wpm = Math.round(((charIndex - mistakes) / 5) / (60 - timeLeft) * 60);
            wpm = wpm < 0 || !wpm || wpm === Infinity ? 0 : wpm;

            cpm = charIndex - mistakes;
        } else {
            clearInterval(timer);
            inpField = '';
        }
    }

    // Update the timer
    function updateTimer() {
        if (timeLeft > 0) {
            timeLeft--;
        } else {
            clearInterval(timer);
        }
    }

    // Reset the game
    function resetGame() {
        loadParagraph();
        inpField = '';
    }

    // Setup on component mount
    onMount(() => {
        loadParagraph();
    });
</script>

<div class="wrapper">
	<h1>Test Your Typing Skills</h1>
    <input
        type="text"
        class="input-field"
        bind:value={inpField}
        on:input={handleInput}
    />
    <div class="content-box">
        <div class="typing-text"></div>
        <div class="content">
            <ul class="result-details">
                <li class="time">
                    <p>Time Left:</p>
                    <span><b>{timeLeft}</b>s</span>
                </li>
                <li class="mistake">
                    <p>Mistakes:</p>
                    <span>{mistakes}</span>
                </li>
                <li class="wpm">
                    <p>WPM:</p>
                    <span>{wpm}</span>
                </li>
                <li class="cpm">
                    <p>CPM:</p>
                    <span>{cpm}</span>
                </li>
            </ul>
            <button on:click={resetGame}>Try Again</button>
        </div>
    </div>
</div>

<style>

    /* Wrapper Styles */
    .wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        min-height: 100vh;
        padding: 3rem 0;
        background-color: #f5f5f5;
        background: linear-gradient( #007acc, #6DD5FA, #FFFFFF);
        overflow: hidden;
    }

    h1 {
        margin-bottom: 2rem;
        color: wheat;
    }

    /* Input Field Styles */
    .input-field {
        width: 60%;
        padding: 10px;
        font-size: 1rem;
        border: 2px solid #ccc;
        border-radius: 8px;
        margin-bottom: 20px;
        outline: none;
    }

    .input-field:focus {
        border-color: #007bff;
        box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
    }

    /* Typing Text Styles */
    .typing-text {
        width: 100%;
        font-size: 1.2rem;
        line-height: 1.5;
        margin-bottom: 50px;
        white-space: pre-wrap;
        text-align: justify;
        color: #111;
    }

    /* Content Box Styles */
    .content-box {
        width: 80%;
        max-width: 800px;
        text-align: center;
        margin-bottom: 20px;
    }

    .result-details {
        list-style-type: none;
        padding: 0;
        margin: 50px 0px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        flex-wrap: wrap;
    }

    .result-details li {
        font-size: 1rem;
        margin: 5px 0;
    }

    .result-details p {
        margin: 0;
        font-weight: bold;
    }

    .result-details span {
        font-size: 1.2rem;
    }

    /* Button Styles */
    button {
        padding: 10px 20px;
        font-size: 1rem;
        color: #fff;
        background-color: #007bff;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    button:hover {
        background-color: #0056b3;
    }

    button:focus {
        outline: none;
    }
</style>
