<h1>Svelte Trivia App</h1>

<p>Correct guesses: {rightCount}</p>
<p>Incorrect guesses: {wrongCount}</p>
<h3>Difficulty</h3>
<button id="easy" on:click={() => setDifficulty('e')}>Easy</button>
<button id="medium" on:click={() => setDifficulty('m')}>Medium</button>
<button id="hard" on:click={() => setDifficulty('h')}>Hard</button>

{#if difficulty != null}
    <h3>Category</h3>
    <button id="general" on:click={() => setCategory('g')}>General Knowledge</button>
    <button id="science" on:click={() => setCategory('s')}>Science</button>
    <button id="entertainment" on:click={() => setCategory('e')}>Entertainment</button>
{:else}
    <p>Choose a difficulty...</p>
{/if}

{#if category != null}
  <ul>
    {#each difficultyTriviaList as trivia}
        <li>{trivia.question}</li>
        {#each trivia.answersList as answer}
            <button on:click={() => checkAnswer(trivia, answer)}>{answer}</button>
        {/each}
    {/each}
  </ul>
{:else if difficulty != null}
  <p>Choose a category...</p>
{/if}

<script>
    

    let generalTriviaList = []
    let scienceTriviaList = []
    let entertainmentTriviaList = []
    let difficultyTriviaList = []
    let difficulty = null
    let category = null
    let rightCount = 0
    let wrongCount = 0

    async function getTriviaData() {
        const generalResponse = await fetch('https://opentdb.com/api.php?amount=50&category=9&type=multiple')
        const generalData = await generalResponse.json()
        generalTriviaList = generalData.results
        const scienceResponse = await fetch('https://opentdb.com/api.php?amount=50&category=17&type=multiple')
        const scienceData = await scienceResponse.json()
        scienceTriviaList = scienceData.results
        const entertainmentResponse = await fetch('https://opentdb.com/api.php?amount=50&category=11&type=multiple')
        const entertainmentData = await entertainmentResponse.json()
        entertainmentTriviaList = entertainmentData.results
        combineAnswers()
    }

    getTriviaData()


    function setDifficulty(setting) {
        var easyButton = document.getElementById('easy')
        var mediumButton = document.getElementById('medium')
        var hardButton = document.getElementById('hard')
        if(setting == 'e') {
            difficulty = 'easy'
            easyButton.disabled = true
            mediumButton.disabled = false
            hardButton.disabled = false
        }
        else if(setting == 'm') {
            difficulty = 'medium'
            easyButton.disabled = false
            mediumButton.disabled = true
            hardButton.disabled = false
        }
        else if(setting == 'h') {
            difficulty = 'hard'
            easyButton.disabled = false
            mediumButton.disabled = false
            hardButton.disabled = true
        }
        else {
            throw new Error('Invalid difficulty code') 
        }
        category = null
        document.getElementById('general').disabled = false
        document.getElementById('science').disabled = false
        document.getElementById('entertainment').disabled = false
    }

    function setCategory(setting) {
        var generalButton = document.getElementById('general')
        var entertainmentButton = document.getElementById('entertainment')
        var scienceButton = document.getElementById('science')
        if(setting == 'g') {
            category = 'general knowledge'
            generalButton.disabled = true
            entertainmentButton.disabled = false
            scienceButton.disabled = false
            filterTrivia(generalTriviaList)
        }
        else if(setting == 'e') {
            category = 'entertainment'
            generalButton.disabled = false
            entertainmentButton.disabled = true
            scienceButton.disabled = false
            filterTrivia(entertainmentTriviaList)
        }
        else if(setting == 's') {
            category = 'science'
            generalButton.disabled = false
            entertainmentButton.disabled = false
            scienceButton.disabled = true
            filterTrivia(scienceTriviaList)
        }
        else {
            throw new Error('Invalid category code') 
        }
    }

    function filterTrivia(triviaList) {
        difficultyTriviaList = triviaList.filter(trivia => trivia.difficulty == difficulty)
    }

    function combineAnswers() {
        generalTriviaList.forEach(e => {
            e.answersList = e.incorrect_answers.slice()
            e.answersList.push(e.correct_answer)
            for (let i = e.answersList.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [e.answersList[i], e.answersList[j]] = [e.answersList[j], e.answersList[i]];
            }
        });
        scienceTriviaList.forEach(e => {
            e.answersList = e.incorrect_answers.slice()
            e.answersList.push(e.correct_answer)
            for (let i = e.answersList.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [e.answersList[i], e.answersList[j]] = [e.answersList[j], e.answersList[i]];
            }
        });
        entertainmentTriviaList.forEach(e => {
            e.answersList = e.incorrect_answers.slice()
            e.answersList.push(e.correct_answer)
            for (let i = e.answersList.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [e.answersList[i], e.answersList[j]] = [e.answersList[j], e.answersList[i]];
            }
        });
    }

    function checkAnswer(triviaSet, userGuess) {
        if(triviaSet.correct_answer == userGuess) {
            rightCount++
        }
        else {
            wrongCount++
        }
    }

</script>