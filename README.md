<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Creative QR Code Generator</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
  body {
    margin: 0;
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #4a90e2, #9013fe);
    min-height: 100vh;
    color: #fff;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
  }
  h1 {
    font-weight: 600;
    margin-bottom: 0.2rem;
    text-shadow: 0 0 8px rgba(0,0,0,0.6);
  }
  h2 {
    font-weight: 400;
    margin-top: 0;
    margin-bottom: 2rem;
    opacity: 0.9;
    text-align: center;
    max-width: 400px;
  }
  form {
    background: rgba(255 255 255 / 0.15);
    padding: 2rem 2.5rem;
    border-radius: 16px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.2);
    width: 340px;
    text-align: center;
  }
  label {
    display: block;
    font-weight: 600;
    margin-bottom: 0.3rem;
    text-align: left;
    color: #eee;
  }
  input[type="text"], input[type="number"], select {
    width: 100%;
    padding: 0.6rem 0.8rem;
    margin-bottom: 1.2rem;
    border-radius: 10px;
    border: none;
    font-size: 1rem;
  }
  input[type="text"]:focus, input[type="number"]:focus, select:focus {
    outline: none;
    box-shadow: 0 0 6px #fde047;
  }
  button {
    background: #fde047;
    border: none;
    color: #222;
    font-weight: 700;
    font-size: 1.1rem;
    padding: 0.75rem 1.8rem;
    border-radius: 50px;
    cursor: pointer;
    transition: background 0.3s ease;
  }
  button:hover {
    background: #fabc15;
  }
  #qrCodeDisplay {
    margin-top: 2rem;
    background: #fff;
    padding: 1rem;
    border-radius: 18px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.15);
    display: none;
    max-width: 320px;
    width: 100%;
    text-align: center;
  }
  #qrCodeDisplay canvas {
    margin: auto;
    display: block;
  }
  .design-controls {
    margin-top: 1rem;
    text-align: left;
  }
  .design-controls label {
    font-weight: 600;
    color: #eee;
    margin-bottom: 0.2rem;
  }
  .design-controls input[type="color"], .design-controls select {
    width: 100%;
    padding: 0.4rem 0.6rem;
    border-radius: 10px;
    border: none;
    margin-bottom: 1.2rem;
  }
  footer {
    margin-top: auto;
    font-size: 0.85rem;
    color: rgba(255,255,255,0.6);
  }
  @media (max-width: 400px) {
    form {
      width: 90vw;
      padding: 1.5rem 1.5rem;
    }
  }
</style>
</head>
<body>
<h1>Creative QR Code Generator</h1>
<h2>Generate QR codes for URLs and Numbers with custom design</h2>
<form id="qrForm">
  <label for="qrType">Select QR code type:</label>
  <select id="qrType" name="qrType" required>
    <option value="url" selected>URL</option>
    <option value="number">Number</option>
  </select>

  <label id="inputLabel" for="inputData">Enter the URL:</label>
  <input type="text" id="inputData" name="inputData" placeholder="https://example.com" required />

  <div class="design-controls">
    <label for="colorDark">QR Dark Color:</label>
    <input type="color" id="colorDark" name="colorDark" value="#333333" />
    <label for="colorLight">QR Light Color:</label>
    <input type="color" id="colorLight" name="colorLight" value="#ffffff" />
    <label for="qrSize">QR Code Size (px):</label>
    <select id="qrSize" name="qrSize">
      <option value="128">128px</option>
      <option value="192" selected>192px</option>
      <option value="256">256px</option>
      <option value="320">320px</option>
    </select>
  </div>

  <button type="submit">Generate QR Code</button>
</form>

<div id="qrCodeDisplay"></div>

<footer>Powered by QRCode.js | Stylish & easy QR code generator</footer>

<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
<script>
  const qrForm = document.getElementById('qrForm');
  const qrTypeSelect = document.getElementById('qrType');
  const inputLabel = document.getElementById('inputLabel');
  const inputData = document.getElementById('inputData');
  const qrCodeDisplay = document.getElementById('qrCodeDisplay');
  const colorDarkInput = document.getElementById('colorDark');
  const colorLightInput = document.getElementById('colorLight');
  const qrSizeSelect = document.getElementById('qrSize');
  let qr;

  function clearQRCode() {
    qrCodeDisplay.innerHTML = '';
    if (qr) {
      qr.clear();
      qr.makeCode('');
    }
  }

  qrTypeSelect.addEventListener('change', () => {
    if (qrTypeSelect.value === 'url') {
      inputLabel.textContent = 'Enter the URL:';
      inputData.type = 'text';
      inputData.placeholder = 'https://example.com';
      inputData.pattern = '';
    } else if (qrTypeSelect.value === 'number') {
      inputLabel.textContent = 'Enter the Number:';
      inputData.type = 'number';
      inputData.placeholder = '1234567890';
      inputData.pattern = '\\d+';
    }
    inputData.value = '';
    clearQRCode();
    qrCodeDisplay.style.display = 'none';
  });

  qrForm.addEventListener('submit', e => {
    e.preventDefault();
    const value = inputData.value.trim();
    if (!value) {
      alert('Please enter a valid input');
      return;
    }
    if (qrTypeSelect.value === 'url') {
      try {
        new URL(value);
      } catch {
        alert('Please enter a valid URL.');
        return;
      }
    } else if (qrTypeSelect.value === 'number') {
      if (!/^\d+$/.test(value)) {
        alert('Please enter a valid number.');
        return;
      }
    }

    qrCodeDisplay.style.display = 'block';
    clearQRCode();

    try {
      qr = new QRCode(qrCodeDisplay, {
        text: value,
        width: parseInt(qrSizeSelect.value, 10),
        height: parseInt(qrSizeSelect.value, 10),
        colorDark: colorDarkInput.value,
        colorLight: colorLightInput.value,
        correctLevel: QRCode.CorrectLevel.H
      });
    } catch (err) {
      alert('Failed to generate QR code.');
      console.error(err);
    }
  });
</script>
</body>
</html>

