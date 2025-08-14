<!-- Script section for the To Do Application -->
<script>
    // Importing necessary Svelte functions
    import { onMount } from 'svelte';

    // Declare variables for the new task input and the list container
	let newTask = '';
	let listContainer;


// Function to add a new task
function addTask() {
    if (newTask.trim() === '') {
        alert('Please enter a task.');
        return;
    }
    
    // Check if the task already exists
    const tasks = Array.from(listContainer.querySelectorAll('li')).map(li => {
        // Get only the text content without the buttons (×, ✎, 💬)
        let taskText = li.textContent.replace('\u00d7', '').replace('\u270E', '').replace('💬', '').trim();
        return taskText;
    });
    if (tasks.includes(newTask.trim())) {
        alert('Task already exists!');
        return;
    }
    
    // Create a new list item and append it to the list container
    const li = document.createElement('li');
    li.innerHTML = newTask;
    listContainer.appendChild(li);
    
    // Create a span element for the delete button
    let span = document.createElement('span');
    span.innerHTML = '\u00d7';
    li.appendChild(span);
    // Create a span element for the edit button
    let editSpan = document.createElement('span');
    editSpan.innerHTML = '\u270E'; // ✎ (edit icon)
    editSpan.className = 'edit';
    li.appendChild(editSpan);


    //Create a span element for the comment button
    let commentSpan = document.createElement('span');
    commentSpan.innerHTML = '💬';
    commentSpan.className = 'comment';
    li.appendChild(commentSpan);
    // Add status select dropdown
    addStatusSelect(li);

    // Add date input
    addDateInput(li);
    // Save the current state of the list to localStorage
    saveData();
    
    newTask = ''; // Clear input after adding
}


// Function to handle click events on the list items
function handleClick(event) {
    // Check if the clicked element is a list item or a delete button
    if (event.target.tagName === 'LI') {
        event.target.classList.toggle('checked');
        saveData();
    } else if (event.target.tagName === 'SPAN') {
        // Now we know it's a span - check which type
        if (event.target.className === 'edit') {
            // Edit functionality - use prompt to get new text
            const li = event.target.parentElement;
            const currentText = li.firstChild.textContent;
            const newText = prompt('Edit task:', currentText);
            if (newText && newText.trim() !== '') {
                li.firstChild.textContent = newText.trim();
                saveData();
            }
        } else if (event.target.className === 'comment') {
            // Comment functionality
            const comment = prompt('Add a comment:');
            if (comment && comment.trim() !== '') {
                saveData();
                alert('Comment added: ' + comment);
                saveData();
                // You can extend this later to save comments
            }
        } else {
            // Delete functionality (spans without className)
            
            event.target.parentElement.remove();
            saveData();
        }
    }
}


// Function to save the current state of the list to localStorage
function saveData() {
    // Save the current state of the list to localStorage
    localStorage.setItem('data', listContainer.innerHTML);
}

// Function to add a status select dropdown to a list item
function addStatusSelect(li) {
    // Check if the status select already exists
    if (!li.querySelector('select')) {
        // Create the status select element
        let status = document.createElement('select');
        status.innerHTML = `
            <option value="clear">Clear</option>
            <option value="low">Low</option>
            <option value="normal">Normal</option>  
            <option value="high">High</option>
            <option value="urgent">Urgent</option>
        `;
        status.className = 'status';

        // Add event listener for status change
        status.addEventListener('change', (event) => {
            li.className = event.target.value; // Set class based on status
            li.setAttribute('data-status', event.target.value); // Save status as attribute
            saveData(); // Save the updated state
        });

        li.appendChild(status);
    }
}


function addDateInput(li) {
    if(!li.querySelector('input[type="date"]')) {
        let dateInput = document.createElement('input');
        dateInput.type = 'date';
        dateInput.className = 'date';
        li.appendChild(dateInput);

        dateInput.addEventListener('change', (event) => {
            li.setAttribute('data-date', event.target.value);
            saveData();
        });
    }
}


// function to load data from the localStorage
function loadData() {
    listContainer.innerHTML = localStorage.getItem('data');
    
    // Re-attach event listeners to all existing status selects
    const allListItems = listContainer.querySelectorAll('li');
    allListItems.forEach(li => {
        const statusSelect = li.querySelector('select');
        if (statusSelect) {
            // Restore the selected value from saved data
            const savedStatus = li.getAttribute('data-status');
            if (savedStatus) {
                statusSelect.value = savedStatus;
                li.className = savedStatus;
            }
            
            statusSelect.addEventListener('change', (event) => {
                li.className = event.target.value; // Set class based on status
                li.setAttribute('data-status', event.target.value); // Save status as attribute
                saveData(); // Save the updated state
            });
        }
    });
}
// Load data when the component is mounted
onMount(() => {
    loadData();
});




</script>



<!-- html for the todo application --> 
<header>
    <h1>
        To Do Application 
    </h1>
</header>
<main>
    <section>
        <h2>Welcome to the To Do Application</h2>
          <h2>
            Your Tasks
        </h2>
    </section>
    <!-- Form for adding new tasks -->
    <form class="todo-list" action="">
         <input class="newTask" type="text" placeholder="Add a new task" bind:value={newTask}/>
         <!-- Button to add a new task -->
        <button on:click|preventDefault={addTask} class="add-task-button" type="submit">Add Task</button>
        <!-- List container for displaying tasks -->
            <ul bind:this={listContainer} id="listContainer" on:click={handleClick} on:keydown={handleClick}>

            </ul>
    </form >

  </main> 
<footer>
    <p class="footer-text">© 2025 To Do Application. All rights reserved.</p> 
</footer>

<!-- Style section for the To Do Application -->
<style>
    :global(body) {
        margin: 0;
        font-family: Arial, sans-serif;
        background-color: #E5EEEF; /* Vann 10% */
    }

    header {
        background-color: #005260; /* Vann */
        color: white;
        padding: 10px 0;
        text-align: center;
    }

    main {
        padding: 20px;
    }

    h1, h2 {
        margin: 0 0 10px;
        text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
        font-weight: 600;
    }

    p {
        margin-bottom: 20px;
    }

    h2 {
        text-align: center;
        display: flex;
        justify-content: center;
    }

    footer {
        background-color: #1C6C6C; /* Hav */
        padding: 10px 0;
        text-align: center;
        margin-top: 30px;
    }

    .footer-text {
        text-align: center;
        display: flex;
        justify-content: center;
        margin-top: 20px;
        color: #ffffff;
    }

    .todo-list {
        padding-top: 10px;
        background-color: #ffffff;
        border-style: solid;
        border-width: 2px;
        border-color: #B8D9DC; /* Fjord 30% */
        height: 700px;
        width: 700px;
        margin-left: auto;
        margin-right: auto;
        display: block;
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    .newTask {
        width: 60%;
        margin-left: 80px;
        padding: 10px;
        border: 1px solid #B8D9DC; /* Fjord 30% */
        border-radius: 5px;
    }

    .add-task-button {
        padding: 10px 20px;
        margin-left: 10px;
        background-color: #14828C; /* Fjord */
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    .add-task-button:hover {
        background-color: #0F6B74; /* Fjord mørkere */
        transform: scale(1.1);
    }

    .add-task-button:active {
        transform: translateY(4px);
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    }

    #listContainer {
        list-style: none;
        padding: 0;
        margin: 20px 100px;
        background-color: #E5F5F9; /* Himmel 10% */
        border-radius: 8px;
        border: 1px solid #B2E1ED; /* Himmel 30% */
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }

    :global(#listContainer li) {
        padding: 12px 20px 12px 40px;
        border-bottom: 1px solid #A4C4C4; /* Hav 40% */
        position: relative;
        transition: background-color 0.2s ease;
        color: #333;
        font-size: 15px;
        font-weight: 400;
    }

    :global(#listContainer li:last-child) {
        border-bottom: none;
    }

    :global(#listContainer li:hover) {
        background-color: #D0E6E8; /* Fjord 20% */
    }

    :global(#listContainer li::before) {
        content: "";
        position: absolute;
        width: 10px;
        height: 10px;
        border-style: solid;
        border-width: 2px;
        border-radius: 50%;
        left: 10px;
        top: 50%;
        transform: translateY(-50%);
        line-height: 1;
    }

    :global(ul li.checked) {
        text-decoration: line-through;
        color: #999;
        background-color: #B8D9DC; /* Fjord 30% */
    }

    :global(ul li.checked::before) {
        background-color: #B7173D; /* Nype */
    }

    :global(#listContainer li span) {
        position: absolute;
        right: 10px;
        top: 50%;
        transform: translateY(-50%);
        width: 30px;
        height: 30px;
        font-size: 18px;
        color: #555;
        line-height: 30px;
        text-align: center;
        cursor: pointer;
        border-radius: 50%;
        transition: background-color 0.2s ease;
    }

    :global(#listContainer li span:hover) {
        background: #B7173D; /* Nype */
        color: white;
    }

    :global(#listContainer li .edit) {
        position: absolute;
        right: 50px;
        top: 50%;
        transform: translateY(-50%);
        width: 30px;
        height: 30px;
        font-size: 18px;
        color: #555;
        line-height: 30px;
        text-align: center;
        cursor: pointer;
        border-radius: 50%;
        transition: background-color 0.2s ease;
    }

    :global(#listContainer li .edit:hover) {
        background: #14828C; /* Fjord */
        color: white;
    }

    :global(#listContainer li .comment) {
        position: absolute;
        right: 90px;
        top: 50%;
        transform: translateY(-50%);
        width: 30px;
        height: 30px;
        font-size: 18px;
        color: #555;
        line-height: 30px;
        text-align: center;
        cursor: pointer;
        border-radius: 50%;
        transition: background-color 0.2s ease;
    }

    :global(#listContainer li .comment:hover) {
        background: #1F9562; /* Gress */
        color: white;
    }
    :global(#listContainer li .status) {
    position: absolute;
    right: 130px;
    top: 50%;
    transform: translateY(-50%);
    
    width: 90px; /* litt bredere for bedre lesbarhet */
    height: 28px;
    padding: 2px 6px;

    font-size: 14px;
    color: #333;
    background-color: #f8fdfd; /* Lys bakgrunn for kontrast */
    
    border: 1px solid #B8D9DC; /* Fjord 30% */
    border-radius: 6px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);

    cursor: pointer;
    transition: all 0.2s ease;
}

/* Hover-effekt */
:global(#listContainer li .status:hover) {
    background-color: #E5F5F9; /* Himmel 10% */
    border-color: #14828C; /* Fjord */
}

/* Fokus (når man klikker på select) */
:global(#listContainer li .status:focus) {
    outline: none;
    border-color: #14828C;
    box-shadow: 0 0 4px rgba(20, 130, 140, 0.4);
}

:global(#listContainer li .date) {
    position: absolute;
    left: 510px;
    top: 50%;
    width: 15px;
    
    transform: translateY(-50%);
}

</style>
