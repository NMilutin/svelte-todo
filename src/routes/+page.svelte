<script>
  let inputText = '';
  const cookiesToArr = function () {
    if (document.cookie !== '') {
      return document.cookie.split('; ').map(function (cookie) {
        return {
          name: cookie.slice(0, cookie.indexOf('=')),
          val: cookie.slice(cookie.indexOf('=') + 1),
        };
      });
    }
    return null;
  };
  const writeCookie = function (name, val, age) {
    document.cookie = `${name}=${val};max-age=${age};samesite=lax`;
    cookies = cookiesToArr();
  };
  const getCookie = function (name) {
    cookies = cookiesToArr();
    return cookies?.find(cookie => cookie.name === name)?.val;
  };
  const delCookie = function (name) {
    document.cookie = `${name}=0;max-age=-1`;
    cookies = cookiesToArr();
  };
  let cookies = cookiesToArr();
  let tasks = getCookie('tasks') ? JSON.parse(getCookie('tasks')) : [];
  const addTask = function (e) {
    console.log(e);
    e.target.classList.add('button_create-task-click');
    if (e.inputType === 'insertParagraph' || !e.inputType) {
      e.preventDefault();
      tasks = [...tasks, { i: tasks.length, text: inputText, done: false }];
      writeCookie('tasks', JSON.stringify(tasks), 30 * 24 * 3600);
      inputText = '';
    }
    setTimeout(
      () => e.target.classList.remove('button_create-task-click'),
      250,
    );
  };
  if (document.cookie) cookies = cookiesToArr();
  writeCookie('mode', getCookie('mode') || 'light', 30 * 24 * 3600);
  let mode = getCookie('mode');
  const doTask = function (i) {
    tasks[i].done = !tasks[i].done;
    writeCookie('tasks', JSON.stringify(tasks), 30 * 24 * 3600);
  };
  const removeTask = function (i, taskEl) {
    taskEl.classList.remove('js_task-add');
    setTimeout(() => taskEl.classList.add('js_task-remove'), 5);
    setTimeout(() => {
      taskEl.classList.remove('js_task-remove');
      tasks.splice(i, 1);
      tasks = tasks;
      writeCookie('tasks', JSON.stringify(tasks), 30 * 24 * 3600);
    }, 695);
  };
</script>

<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.css"
  integrity="sha512-tx5+1LWHez1QiaXlAyDwzdBTfDjX07GMapQoFTS74wkcPMsI3So0KYmFe6EHZjI8+eSG0ljBlAQc3PQ5BTaZtQ=="
  crossorigin="anonymous"
  referrerpolicy="no-referrer"
/>
<div class="container_app {mode}">
  <ul class="list_tasks">
    <h1 class="title">Svelte To-Do list</h1>
    <li class="item_new-task">
      <!-- svelte-ignore a11y-interactive-supports-focus -->
      <span
        contenteditable
        role="textbox"
        class="input_new-task"
        type="text"
        spellcheck="false"
        bind:textContent={inputText}
        on:beforeinput={addTask}
      ></span>
      <button class="button_create-task" on:click={addTask}
        ><i class="fa-regular fa-square-plus"></i></button
      >
    </li>
    {#each tasks as task, i}
      <li class="item_task js_task-add">
        <span class="text_task">{task.text}</span>
        <div class="container_task-buttons">
          <button class="button_do-task" on:click={() => doTask(i)}
            ><i class="fa-regular fa-square{task.done ? '-check' : ''}"
            ></i></button
          >
          <button
            class="button_delete-task"
            on:click={e => removeTask(i, e.target.closest('.item_task'))}
            ><i class="fa-regular fa-square-minus"></i></button
          >
        </div>
      </li>
    {/each}
  </ul>

  <button
    class="color-mode_button {mode}"
    on:click={() => {
      writeCookie('mode', mode === 'light' ? 'dark' : 'light');
      mode = getCookie('mode');
    }}><i class="fa-solid fa-{mode === 'light' ? 'sun' : 'moon'}"></i></button
  >
</div>

<!-- TODO Edit, Dark Mode Toggle -->

<style>
  .container_app {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: monospace;
    margin: 0;
  }
  .title {
    text-align: center;
  }
  .list_tasks {
    padding: 40px 20px;
    padding-top: 20px;
    border: 2px solid lightgray;
    border-radius: 10px;
    width: 450px;
    max-width: 450px;
  }
  .item_new-task,
  .item_task {
    list-style: none;
    box-sizing: border-box;
    padding: 20px 30px;
    padding-bottom: 10px;
    display: flex;
    justify-content: space-between;
    width: 100%;
    gap: 15px;
    border-bottom: 1px solid lightgray;
  }
  .js_task-add,
  .js_task-add * {
    animation: remove-task 0.3s ease reverse;
  }
  li:global(.js_task-remove),
  li:global(.js_task-remove) * {
    animation: remove-task 0.5s ease 0.2s;
  }
  .fa-square {
    animation: none;
  }
  i::before,
  i {
    max-height: 30px;
  }
  .input_new-task,
  .text_task {
    font-family: monospace;
    font-size: 20px;
    border: none;
    outline: none;
    overflow: hidden;
    word-wrap: break-word;
    resize: none;
  }
  .input_new-task {
    flex: 1;
  }
  .input_new-task[contenteditable]:empty::before {
    content: 'New Task';
    color: gray;
  }
  .container_task-buttons {
    min-width: fit-content;
    max-width: fit-content;
  }
  .button_create-task,
  .button_delete-task,
  .button_do-task {
    border: none;
    outline: none;
    padding: 0;
    font-size: 28px;
  }
  .fa-square-check {
    animation: jump 0.25s linear;
  }
  .fa-square-plus:global(.button_create-task-click) {
    animation: jump 0.25s linear;
  }
  @keyframes jump {
    0% {
      transform: scale(100%);
    }
    30% {
      transform: scale(85%);
    }
    100% {
      transform: scale(100%);
    }
  }
  @keyframes remove-task {
    100% {
      opacity: 0;
      transform: translateX(-100%);
      max-height: 0px;
      font-size: 0px;
      padding: 0;
    }
  }
  :global(body):has(.light),
  .light,
  .light *,
  .light i::before,
  .light i {
    background-color: hsl(168, 80%, 98%);
    color: hsl(150, 3%, 12%);
    outline: none;
    transition:
      color 0.25s linear,
      background-color 0.25s linear;
  }
  :global(body):has(.dark),
  .dark,
  .dark *,
  .dark i::before,
  .dark i {
    color: hsl(168, 80%, 99%);
    background-color: hsl(150, 3%, 12%);
    outline: none;
    transition:
      color 0.25s linear,
      background-color 0.25s linear;
  }
  .color-mode_button {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 50px;
    height: 50px;
    font-size: 20px;
    border: lightgray solid 2px;
    border-radius: 10px;
    box-shadow: 0px 3px 0px lightgray;
  }
  .color-mode_button:active {
    transform: translateY(3px);
    box-shadow: inset 0px 3px 0px lightgray;
  }
  .light.color-mode_button:hover,
  .light.color-mode_button:hover i::before {
    background-color: hsl(168, 10%, 90%);
  }
  .dark.color-mode_button:hover,
  .dark.color-mode_button:hover i::before {
    background-color: hsl(150, 3%, 30%);
  }
  @media only screen and (max-width: 700px) {
    .container_app {
      flex-direction: column-reverse;
    }
    .list_tasks {
      max-width: 350px;
    }
    .list_tasks * {
      font-size: 18px;
    }
    .list_tasks i,
    .list_tasks i * {
      font-size: 28px;
    }
    .title {
      font-size: 20px;
    }
    .color-mode_button {
      position: static;
      width: 35px;
      height: 35px;
      font-size: 20px;
    }
  }
  @media only screen and (max-width: 425px) {
    .container_app {
      flex-direction: column-reverse;
    }
    .list_tasks {
      max-width: 250px;
    }
    .list_tasks * {
      font-size: 18px;
    }
    .list_tasks i,
    .list_tasks i * {
      font-size: 28px;
    }
    .title {
      font-size: 20px;
    }
    .color-mode_button {
      position: static;
      width: 35px;
      height: 35px;
      font-size: 20px;
    }
  }
</style>
