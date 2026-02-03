<script>
  import { onMount } from "svelte";

  const STORAGE_KEY = "crud-records";

  // Records + id counter will now persist
  let records = [];
  let nextId = 1;

  // Form state
  let form = { id: null, firstName: "", lastName: "", age: "" };

  function resetForm() {
    form = { id: null, firstName: "", lastName: "", age: "" };
  }

  function save() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify({ records, nextId }));
  }

  function load() {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (!raw) return;

    try {
      const parsed = JSON.parse(raw);
      records = parsed.records ?? [];
      nextId = parsed.nextId ?? 1;
    } catch (e) {
      // If storage is corrupted, reset it
      records = [];
      nextId = 1;
      save();
    }
  }

  // Run only in the browser (localStorage doesn't exist during SSR)
  onMount(() => {
    load();
  });

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

    save();
    resetForm();
  }

  function edit(rec) {
    form = {
      id: rec.id,
      firstName: rec.firstName,
      lastName: rec.lastName,
      age: String(rec.age)
    };
  }

  function remove(id) {
    if (!confirm("Delete this record?")) return;

    records = records.filter((r) => r.id !== id);
    if (form.id === id) resetForm();

    save();
  }
</script>
