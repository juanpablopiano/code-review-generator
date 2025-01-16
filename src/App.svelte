<script>
  // get developers from local storage
  const developersLS = JSON.parse(localStorage.getItem('developers')) || [];

  let developers = $state(developersLS);
  let currentDeveloper = $state('');
  let assignments = $state({});

  function addDeveloper() {
    // add to local storage
    if (!currentDeveloper) return;
    developers.push(currentDeveloper);
    localStorage.setItem('developers', JSON.stringify(developers));
    currentDeveloper = '';
  }

  /**
   * Assign reviewers to developers
   * @param {string[]} developers - List of developers
   * @returns {Object} - Object with developer assignments
   */
  function assignReviewers(developers) {
    if (developers.length < 3) {
      return null;
    }

    // Barajar la lista para asegurar aleatoriedad
    const shuffled = [...developers].sort(() => Math.random() - 0.5);

    const assignments = {}; // Objeto para guardar las asignaciones

    // Asignar revisores de forma cíclica
    shuffled.forEach((dev, index) => {
      // Seleccionar los próximos 2 desarrolladores en la lista (cíclicamente)
      const reviewer1 = shuffled[(index + 1) % shuffled.length];
      const reviewer2 = shuffled[(index + 2) % shuffled.length];

      // Asignar los revisores al desarrollador actual
      assignments[dev] = [reviewer1, reviewer2];
    });

    return assignments;
  }

  function handleAssignments() {
    assignments = {};
    assignments = assignReviewers(developers);
  }

  function clearDevelopers() {
    developers = [];
    localStorage.removeItem('developers');
  }

  function handleEnterKey(event) {
    if (event.key === 'Enter') {
      addDeveloper();
    }
  }
</script>

<main>
  <h1>Code reviewer assigner</h1>
  <div class="flex justify-center gap-3 w-full">
    <div class="w-96 h-96 bg-slate-400 relative">
      <div class="flex">
        <label class="w-full">
          <input
            class="text-gray-900 w-full"
            type="text"
            placeholder="Add developer name"
            onkeydown={handleEnterKey}
            bind:value={currentDeveloper}
          />
        </label>
        <button type="button" onclick={addDeveloper}>Add</button>
      </div>
      <div class="p-4">
        <ul>
          {#each developers as developer}
            <li class="text-gray-900">{developer}</li>
          {/each}
        </ul>
      </div>
      <div class="absolute bottom-0 left-0 w-full p-4 flex">
        <button class="w-2/3" onclick={handleAssignments}>Generate reviewers</button>
        <button class="w-1/3" onclick={clearDevelopers}>Clear</button>
      </div>
    </div>
    <div class="w-96 h-96 bg-slate-400">
      <h2 class="text-gray-900">Reviewers</h2>
      {#if developers.length < 3}
        <h3 class="text-gray-900">Add at least 3 developers to generate reviewers</h3>
      {:else}
      <ul>
        {#if assignments && Object.keys(assignments).length > 2}
          {#each developers as developer}
            <li class="text-gray-900">
              {developer} reviews:
              <ul>
                {#each assignments[developer] as reviewer}
                  <li>{reviewer}</li>
                {/each}
              </ul>
            </li>
          {/each}
        {/if}
      </ul>
      {/if}
      
    </div>
  </div>
</main>
