<script>
  // In-memory data (resets on refresh)
  let records = [];
  let nextId = 1;

  // Form state (id=null means "create", otherwise "edit")
  let form = { id: null, firstName: "", lastName: "", age: "" };

  function resetForm() {
    form = { id: null, firstName: "", lastName: "", age: "" };
  }

  function submit() {
    const firstName = form.firstName.trim();
    const lastName = form.lastName.trim();
    const ageNum = Number(form.age);

    if (!firstName || !lastName || !Number.isInteger(ageNum) || ageNum < 0) {
      alert("Enter first name, last name, and a valid non-negative integer age.");
      return;
    }

    if (form.id === null) {
      // CREATE
      records = [...records, { id: nextId++, firstName, lastName, age: ageNum }];
    } else {
      // UPDATE
      records = records.map((r) =>
        r.id === form.id ? { ...r, firstName, lastName, age: ageNum } : r
      );
    }

    resetForm();
  }

  function edit(rec) {
    form = { id: rec.id, firstName: rec.firstName, lastName: rec.lastName, age: String(rec.age) };
  }

  function remove(id) {
    if (!confirm("Delete this record?")) return;
    records = records.filter((r) => r.id !== id);
    if (form.id === id) resetForm();
  }
</script>

<div class="min-h-screen bg-gray-50">
  <div class="mx-auto max-w-4xl p-6">
    <h1 class="text-3xl font-bold">CRUD Workshop</h1>
    <p class="mt-1 text-gray-600">Data is stored in memory (refresh clears it).</p>

    <!-- Form -->
    <div class="mt-6 rounded-2xl bg-white p-6 shadow-sm ring-1 ring-gray-200">
      <h2 class="text-lg font-semibold">
        {form.id === null ? "Create record" : `Update record (ID: ${form.id})`}
      </h2>

      <form class="mt-4 grid gap-4 sm:grid-cols-3" on:submit|preventDefault={submit}>
        <div>
          <label class="block text-sm font-medium text-gray-700">First name</label>
          <input
            class="mt-1 w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-gray-200"
            bind:value={form.firstName}
            placeholder="First"
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">Last name</label>
          <input
            class="mt-1 w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-gray-200"
            bind:value={form.lastName}
            placeholder="Last"
          />
        </div>

        <div>
          <label class="block text-sm font-medium text-gray-700">Age</label>
          <input
            class="mt-1 w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-gray-200"
            bind:value={form.age}
            inputmode="numeric"
            placeholder="Age"
          />
        </div>

        <div class="sm:col-span-3 flex gap-2">
          <button class="rounded-lg bg-black px-4 py-2 text-white hover:opacity-90" type="submit">
            {form.id === null ? "Create" : "Update"}
          </button>

          {#if form.id !== null}
            <button
              class="rounded-lg border border-gray-300 bg-white px-4 py-2 hover:bg-gray-50"
              type="button"
              on:click={resetForm}
            >
              Cancel
            </button>
          {/if}
        </div>
      </form>
    </div>

    <!-- Table -->
    <div class="mt-6 rounded-2xl bg-white p-6 shadow-sm ring-1 ring-gray-200">
      <div class="flex items-center justify-between">
        <h2 class="text-lg font-semibold">Records</h2>
        <span class="text-sm text-gray-600">Total: <span class="font-medium">{records.length}</span></span>
      </div>

      <div class="mt-4 overflow-x-auto">
        <table class="min-w-full">
          <thead class="border-b text-left text-xs uppercase tracking-wide text-gray-500">
            <tr>
              <th class="py-2 pr-4">ID</th>
              <th class="py-2 pr-4">First name</th>
              <th class="py-2 pr-4">Last name</th>
              <th class="py-2 pr-4">Age</th>
              <th class="py-2 pr-4">Actions</th>
            </tr>
          </thead>

          <tbody>
            {#if records.length === 0}
              <tr>
                <td class="py-6 text-center text-gray-500" colspan="5">No records yet.</td>
              </tr>
            {:else}
              {#each records as r (r.id)}
                <tr class="border-b last:border-b-0">
                  <td class="py-3 pr-4 font-medium">{r.id}</td>
                  <td class="py-3 pr-4">{r.firstName}</td>
                  <td class="py-3 pr-4">{r.lastName}</td>
                  <td class="py-3 pr-4">{r.age}</td>
                  <td class="py-3 pr-4">
                    <div class="flex gap-2">
                      <button
                        class="rounded-lg border border-gray-300 bg-white px-3 py-1.5 text-sm hover:bg-gray-50"
                        on:click={() => edit(r)}
                      >
                        Edit
                      </button>
                      <button
                        class="rounded-lg bg-red-600 px-3 py-1.5 text-sm text-white hover:opacity-90"
                        on:click={() => remove(r.id)}
                      >
                        Delete
                      </button>
                    </div>
                  </td>
                </tr>
              {/each}
            {/if}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>

