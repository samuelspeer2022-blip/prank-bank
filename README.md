# prank-bank
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>PFR Bank - Online Banking</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter&display=swap');
  body {
    margin: 0; padding: 0; font-family: 'Inter', sans-serif;
    background: #f5f7fa;
    color: #222;
  }
  header {
    background: #004080;
    color: white;
    padding: 15px 30px;
    font-size: 1.8rem;
    font-weight: 700;
    user-select: none;
  }
  main {
    max-width: 700px;
    margin: 30px auto;
    background: white;
    border-radius: 8px;
    box-shadow: 0 6px 12px rgb(0 0 0 / 0.1);
    padding: 20px 30px 40px;
  }
  .kontostand-container {
    margin-bottom: 30px;
  }
  label {
    font-weight: 600;
    display: block;
    margin-bottom: 6px;
  }
  input[type="text"], input[type="number"], input[type="date"] {
    width: 100%;
    padding: 8px 12px;
    font-size: 1rem;
    border: 1.8px solid #ccc;
    border-radius: 6px;
    box-sizing: border-box;
    margin-bottom: 14px;
    transition: border-color 0.3s;
  }
  input[type="text"]:focus, input[type="number"]:focus, input[type="date"]:focus {
    outline: none;
    border-color: #004080;
  }
  button {
    background-color: #004080;
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-weight: 700;
    font-size: 1rem;
    transition: background-color 0.3s;
  }
  button:hover {
    background-color: #003060;
  }
  .kontostand-display {
    font-size: 2.8rem;
    font-weight: 800;
    color: #004080;
    margin-top: 10px;
    margin-bottom: 30px;
    user-select: text;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 15px;
  }
  th, td {
    padding: 10px 12px;
    border-bottom: 1px solid #eee;
    text-align: left;
  }
  th {
    background-color: #f0f4ff;
    font-weight: 600;
    color: #004080;
  }
  tbody tr:hover {
    background-color: #f9fbff;
  }
  .betrag-positive {
    color: #2e7d32; /* grün */
    font-weight: 700;
  }
  .betrag-negative {
    color: #c62828; /* rot */
    font-weight: 700;
  }
  .scrollable {
    max-height: 220px;
    overflow-y: auto;
    border: 1px solid #ddd;
    border-radius: 6px;
  }
</style>
</head>
<body>
<header>
  PFR Bank - Online Banking
</header>

<main>
  <div class="kontostand-container">
    <label for="kontostandInput">Kontostand eingeben (€):</label>
    <input type="number" id="kontostandInput" step="0.01" min="0" value="12340000" />
    <div class="kontostand-display" id="kontostandDisplay">12.340.000,00 €</div>
  </div>

  <h2>Transaktionen</h2>

  <form id="transactionForm">
    <label for="datum">Datum:</label>
    <input type="date" id="datum" required value="">
    <label for="zweck">Verwendungszweck:</label>
    <input type="text" id="zweck" placeholder="z.B. Lotto-Gewinn" required />
    <label for="betrag">Betrag (€):</label>
    <input type="number" id="betrag" step="0.01" required />
    <label for="typ">Typ:</label>
    <select id="typ" required>
      <option value="gutschrift">Gutschrift (+)</option>
      <option value="lastschrift">Lastschrift (-)</option>
    </select>
    <button type="submit">Hinzufügen</button>
  </form>

  <div class="scrollable">
    <table id="transaktionsTabelle">
      <thead>
        <tr>
          <th>Datum</th>
          <th>Verwendungszweck</th>
          <th>Betrag</th>
          <th>Typ</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>18.09.2025</td>
          <td>Lottogewinn</td>
          <td class="betrag-positive">+10.000.000,00 €</td>
          <td>Gutschrift</td>
        </tr>
        <tr>
          <td>17.09.2025</td>
          <td>Lamborghini Aventador</td>
          <td class="betrag-negative">-450.000,00 €</td>
          <td>Lastschrift</td>
        </tr>
        <tr>
          <td>16.09.2025</td>
          <td>Elon Musk Überweisung</td>
          <td class="betrag-positive">+2.000.000,00 €</td>
          <td>Gutschrift</td>
        </tr>
      </tbody>
    </table>
  </div>
</main>

<script>
  const kontostandInput = document.getElementById('kontostandInput');
  const kontostandDisplay = document.getElementById('kontostandDisplay');
  const transaktionsTabelle = document.getElementById('transaktionsTabelle').querySelector('tbody');
  const transactionForm = document.getElementById('transactionForm');

  // Hilfsfunktion: Zahl formatieren wie Geld in DE (1.234.567,89 €)
  function formatEuro(amount) {
    return amount.toLocaleString('de-DE', { minimumFractionDigits: 2, maximumFractionDigits: 2 }) + ' €';
  }

  // Kontostand aktualisieren (anzeige)
  function updateKontostandDisplay() {
    const wert = parseFloat(kontostandInput.value) || 0;
    kontostandDisplay.textContent = formatEuro(wert);
  }

  // Initial update
  updateKontostandDisplay();

  // Kontostand Eingabe ändert Anzeige
  kontostandInput.addEventListener('input', () => {
    updateKontostandDisplay();
  });

  // Transaktion hinzufügen
  transactionForm.addEventListener('submit', e => {
    e.preventDefault();

    const datum = document.getElementById('datum').value;
    const zweck = document.getElementById('zweck').value.trim();
    let betrag = parseFloat(document.getElementById('betrag').value);
    const typ = document.getElementById('typ').value;

    if (!datum || !zweck || isNaN(betrag)) {
      alert('Bitte alle Felder korrekt ausfüllen!');
      return;
    }

    if (typ === 'lastschrift') {
      betrag = -betrag;
    }

    // Betrag als String mit + oder - und Eurozeichen formatieren
    const betragText = (betrag >= 0 ? '+' : '') + formatEuro(betrag >= 0 ? betrag : -betrag);

    // Neue Zeile erstellen
    const tr = document.createElement('tr');

    // Datum (deutsches Format TT.MM.JJJJ)
    const d = new Date(datum);
    const datumString = d.toLocaleDateString('de-DE');

    tr.innerHTML = `
      <td>${datumString}</td>
      <td>${zweck}</td>
      <td class="${betrag >= 0 ? 'betrag-positive' : 'betrag-negative'}">${betragText}</td>
      <td>${typ === 'gutschrift' ? 'Gutschrift' : 'Lastschrift'}</td>
    `;

    transaktionsTabelle.prepend(tr);

    // Formular zurücksetzen (Datum auf heute setzen)
    transactionForm.reset();
    const heute = new Date().toISOString().split('T')[0];
    document.getElementById('datum').value = heute;
  });

  // Datum im Formular standardmäßig auf heute setzen
  document.addEventListener('DOMContentLoaded', () => {
    const heute = new Date().toISOString().split('T')[0];
    document.getElementById('datum').value = heute;
  });
</script>

</body>
</html>

