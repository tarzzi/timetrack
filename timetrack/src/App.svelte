<script>
  // Get current clock time and update every second
  let clockNow = new Date().toLocaleTimeString();
  setInterval(() => {
    clockNow = new Date().toLocaleTimeString();
  }, 1000);
  let setTimeString = localStorage.getItem("taskItems");
  // check if localstorage item exists
  let localStorageTasks = [];
  if (setTimeString) {
    localStorageTasks = JSON.parse(setTimeString);
  }

  function roundToNearest5() {
    //handle input
    let inputElement = document.getElementById("task");
    let input = inputElement.value;
    inputElement.value = "";

    let arr = [];

    //handle date and task
    let date = new Date();
    const minutes = 15;
    const ms = 1000 * 60 * minutes;
    let taskItems = new Date(Math.floor(date.getTime() / ms) * ms);
    let newStorageItem = { task: input, time: taskItems.toLocaleTimeString() };
    let currentStorage = JSON.parse(localStorage.getItem("taskItems"));
    arr.push(newStorageItem);
    if (currentStorage) {
      arr.push(...currentStorage);
    }
    localStorage.setItem("taskItems", JSON.stringify(arr));

    //update list
    localStorageTasks = JSON.parse(localStorage.getItem("taskItems"));
  }

  function downloadAllItems() {
    let data = JSON.parse(localStorage.getItem("taskItems"));

    let csvContent = "data:text/csv;charset=utf-8,";
    // add headers
    csvContent += "Task,Time\n";
    data.forEach(function (rowArray) {
      let row = rowArray.task + "," + rowArray.time;
      csvContent += row + "\n";
    });
    // download file
    var encodedUri = encodeURI(csvContent);
    var link = document.createElement("a");
    link.setAttribute("href", encodedUri);
    link.setAttribute("download", "my_data.csv");
    document.body.appendChild(link); // Required for FF

    link.click();

  }

  function clearLocalStorage() {
    let isAbsolutelySure = confirm("Are you sure you want to clear all data?");
    if (isAbsolutelySure) {
      localStorage.clear();
      localStorageTasks = [];
    }
  }

  // pop up notification when time is up
    function notifyMe() {
        if (!("Notification" in window)) {
        alert("This browser does not support desktop notification");
        }
    
        // Let's check whether notification permissions have already been granted
        else if (Notification.permission === "granted") {
        // If it's okay let's create a notification
        //if half hour has passed

        var notification = new Notification("Time's up!");
        }
    
        // Otherwise, we need to ask the user for permission
        else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function (permission) {
            // If the user accepts, let's create a notification
            if (permission === "granted") {
            var notification = new Notification("Time's up!");
            }
        });
        }

        // At last, if the user has denied notifications, and you
        // want to be respectful there is no need to bother them any more.

    }
</script>

<main>
  <section>
    <h1>TimeTrack<span style="font-size: 12px;"> v1.0</span></h1>
    <h2>{clockNow}</h2>
  </section>
  <section>
    <form on:submit|preventDefault={roundToNearest5}>
      <label for="task">
        What are you doing?
        <input type="text" id="task" name="task" />
      </label>
    </form>
  </section>
  {#if localStorageTasks}
    <ul>
      {#each localStorageTasks as task}
        <li>{task.task} - {task.time}</li>
      {/each}
    </ul>
  {/if}
  <section />
  <div class="float">
    <button class="btn green" on:click={downloadAllItems}>Download</button>
    <button class="btn red" on:click={clearLocalStorage}>Clear</button>
  </div>
</main>

<style>
</style>
