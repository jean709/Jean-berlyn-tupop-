<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Jean Berlyn Top-Up Dyaman</title>
  <style>
    body { font-family: Arial; padding: 20px; background: #f2f9f2; }
    h1, h2 { color: #335533; }
    .card { background: #fff; padding: 20px; border-radius: 10px; margin-bottom: 20px; box-shadow: 0 0 10px #ccc; max-width: 600px; margin-left: auto; margin-right: auto;}
    button {
      background: #25D366; color: white; padding: 10px 15px;
      border: none; border-radius: 5px; font-size: 16px;
      cursor: pointer;
    }
    button:disabled {
      background: #ccc;
      cursor: not-allowed;
    }
    label {
      font-weight: bold;
    }
    input[type="text"], input[type="number"], select {
      width: 100%; padding: 8px; margin-top: 5px;
      border: 1px solid #ccc; border-radius: 5px;
      box-sizing: border-box;
    }
  </style>
</head>
<body>
  <div class="card" style="max-width: 500px; margin: 20px auto;">
    <h2>💎 Achte Dyaman / Monnen Pou Jwèt Ou</h2>

    <label for="basePrice">Antre pri premye pake (110 Dyaman) an HTG:</label>
    <input type="number" id="basePrice" placeholder="Egzanp: 160" min="1" value="160" />

    <br><br>

    <label for="game">Chwazi Jwèt la:</label><br>
    <select id="game" style="width: 100%; padding: 8px; margin-top: 5px;">
      <option value="">-- Chwazi yon jwèt --</option>
      <option value="freefire">Free Fire</option>
      <option value="fortnite">Fortnite</option>
      <option value="pubg">PUBG Mobile</option>
      <option value="codm">Call of Duty Mobile</option>
      <option value="mobilelegends">Mobile Legends</option>
    </select>

    <br><br>

    <label for="package">Chwazi Pake a:</label><br>
    <select id="package" style="width: 100%; padding: 8px; margin-top: 5px;" disabled>
      <option value="">-- Premyeman chwazi jwèt la --</option>
    </select>

    <br><br>

    <label for="playerid">Antre ID jwè ou:</label><br>
    <input type="text" id="playerid" placeholder="Egzanp: 123456789" style="width: 100%; padding: 8px; margin-top: 5px;">

    <br><br>

    <p><strong>Pri Total:</strong> <span id="price">--</span> HTG</p>

    <a href="#" id="orderBtn" target="_blank" style="text-decoration: none;">
      <button style="width: 100%; background-color: #25D366; color: white; padding: 12px; border: none; border-radius: 6px; font-size: 16px;" disabled>
        Voye Lòd ou WhatsApp
      </button>
    </a>
  </div>

  <script>
    const basePriceInput = document.getElementById('basePrice');
    const gameSelect = document.getElementById('game');
    const packageSelect = document.getElementById('package');
    const priceSpan = document.getElementById('price');
    const playerIdInput = document.getElementById('playerid');
    const orderBtn = document.getElementById('orderBtn');
    const orderButtonInner = orderBtn.querySelector('button');

    // Pakè dyaman pou chak jwèt (kantite sèlman)
    const packagesData = {
      freefire: [
        { name: "110 Dyaman", quantity: 110 },
        { name: "310 Dyaman", quantity: 310 },
        { name: "520 Dyaman", quantity: 520 },
        { name: "1060 Dyaman", quantity: 1060 }
      ],
      fortnite: [
        { name: "1000 V-Bucks", quantity: 1000 },
        { name: "2800 V-Bucks", quantity: 2800 },
        { name: "5000 V-Bucks", quantity: 5000 }
      ],
      pubg: [
        { name: "60 UC", quantity: 60 },
        { name: "300 UC", quantity: 300 },
        { name: "600 UC", quantity: 600 }
      ],
      codm: [
        { name: "100 CP", quantity: 100 },
        { name: "560 CP", quantity: 560 },
        { name: "1200 CP", quantity: 1200 }
      ],
      mobilelegends: [
        { name: "50 Diamonds", quantity: 50 },
        { name: "250 Diamonds", quantity: 250 },
        { name: "500 Diamonds", quantity: 500 }
      ]
    };

    // Fonksyon pou kalkile pri pake ki baze sou pri premye pake ak kantite dyaman
    function calculatePrice(quantity) {
      const baseQuantity = 110; // Premye pake se 110 dyaman
      const basePrice = parseFloat(basePriceInput.value);
      if (isNaN(basePrice) || basePrice <= 0) return null;
      // Pri lineyè
      return (quantity * basePrice) / baseQuantity;
    }

    // Mete opsyon pake nan lis la ak pri kalkile
    function updatePackageOptions() {
      const selectedGame = gameSelect.value;
      packageSelect.innerHTML = '';

      if (!selectedGame) {
        packageSelect.disabled = true;
        packageSelect.innerHTML = '<option value="">-- Premyeman chwazi jwèt la --</option>';
        priceSpan.textContent = '--';
        orderBtn.href = '#';
        orderButtonInner.disabled = true;
        return;
      }

      packageSelect.disabled = false;
      packageSelect.innerHTML = '<option value="">-- Chwazi pake --</option>';

      packagesData[selectedGame].forEach((pkg, idx) => {
        const price = calculatePrice(pkg.quantity);
        const option = document.createElement('option');
        if (price === null) {
          option.textContent = `${pkg.name} - Pri: -- HTG`;
        } else {
          option.textContent = `${pkg.name} - Pri: ${price.toFixed(2)} HTG`;
        }
        option.value = idx;
        packageSelect.appendChild(option);
      });

      priceSpan.textContent = '--';
      orderBtn.href = '#';
      orderButtonInner.disabled = true;
    }

    // Lè pri baz chanje, mete ajou opsyon pake yo si jwèt deja chwazi
    basePriceInput.addEventListener('input', () => {
      if (gameSelect.value) {
        updatePackageOptions();
        updateOrderButton();
      }
    });

    gameSelect.addEventListener('change', () => {
      updatePackageOptions();
      updateOrderButton();
    });

    packageSelect.addEventListener('change', () => {
      updateOrderButton();
    });

    playerIdInput.addEventListener('input', () => {
      updateOrderButton();
    });

    function updateOrderButton() {
      const selectedGame = gameSelect.value;
      const selectedPackageIndex = packageSelect.value;
      const playerId = playerIdInput.value.trim();

      if (selectedGame && selectedPackageIndex !== '' && playerId.length > 0) {
        const pkg = packagesData[selectedGame][selectedPackageIndex];
        const price = calculatePrice(pkg.quantity);
        if (price === null) {
          orderBtn.href = '#';
          orderButtonInner.disabled = true;
          priceSpan.textContent = '--';
          return;
        }
        priceSpan.textContent = price.toFixed(2);

        const text = encodeURIComponent(
          `Mwen vle achte ${pkg.name} pou ${capitalize(selectedGame)}. ID mwen se: ${playerId}. Pri total: ${price.toFixed(2)} HTG`
        );
        orderBtn.href = `https://wa.me/50935798281?text=${text}`;
        orderButtonInner.disabled = false;
      } else {
        priceSpan.textContent = '--';
        orderBtn.href = '#';
        orderButtonInner.disabled = true;
      }
    }

    function capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    }
  </script>
</body>
</html>
