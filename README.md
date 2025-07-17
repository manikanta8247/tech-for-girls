# tech-for-girls
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Tech for Girls Registration</title>
<style>
  body {
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
    background-color: #f0f4f8;
    margin: 0; padding: 20px;
    display: flex;
    justify-content: center;
  }
  .container {
    max-width: 500px;
    background: #fff;
    padding: 30px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  }
  h2 {
    text-align: center;
    color: #333;
  }
  label {
    display: block;
    margin-top: 15px;
    font-weight: 600;
  }
  input[type="text"],
  input[type="number"],
  input[type="email"],
  select,
  input[type="file"] {
    width: 100%;
    padding: 8px 10px;
    margin-top: 5px;
    border-radius: 4px;
    border: 1px solid #ccc;
    box-sizing: border-box;
  }
  button {
    margin-top: 20px;
    padding: 12px;
    width: 100%;
    background-color: #007bff;
    color: #fff;
    font-size: 16px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  button:disabled {
    background-color: #aaa;
    cursor: not-allowed;
  }
  #shareBtn {
    background-color: #25D366;
  }
  #shareCount {
    margin-top: 10px;
    font-weight: bold;
  }
  #message {
    margin-top: 15px;
    text-align: center;
    font-size: 1em;
    color: green;
  }
</style>
</head>
<body>
<div class="container">
<h2>Register for Tech for Girls</h2>
<form id="regForm">
  <label for="name">Name:</label>
  <input type="text" id="name" required />

  <label for="phone">Phone Number:</label>
  <input type="number" id="phone" required />

  <label for="email">Email ID:</label>
  <input type="email" id="email" required />

  <label for="college">College/Department:</label>
  <select id="college" required>
    <option value="">Select your college/department</option>
    <option value="College A">College A</option>
    <option value="College B">College B</option>
    <option value="College C">College C</option>
  </select>

  <button type="button" id="shareBtn">Share on WhatsApp</button>
  <div id="shareCount">Click count: 0/5</div>

  <label for="screenshot">Upload Screenshot:</label>
  <input type="file" id="screenshot" accept="image/*" required />

  <button type="submit" id="submitBtn">Submit Registration</button>
</form>
<div id="message"></div>
</div>

<script>
const googleSheetURL = 'YOUR_GOOGLE_APPS_SCRIPT_URL'; // Replace with your Google Apps Script URL
const maxShares = 5;
let shareCount = 0;

// Initialize from localStorage
if(localStorage.getItem('submitted')){
  disableForm();
}

const shareBtn = document.getElementById('shareBtn');
const shareCountDisplay = document.getElementById('shareCount');
const messageDiv = document.getElementById('message');
const submitBtn = document.getElementById('submitBtn');
const form = document.getElementById('regForm');

shareBtn.addEventListener('click', () => {
  if(shareCount >= maxShares) return;
  const message = encodeURIComponent("Hey Buddy, Join Tech For Girls Community");
  const whatsappURL = `https://wa.me/?text=${message}`;
  window.open(whatsappURL, '_blank');
  shareCount++;
  shareCountDisplay.textContent = `Click count: ${shareCount}/${maxShares}`;
  if(shareCount === maxShares){
    messageDiv.textContent = "Sharing complete. Please continue.";
  }
});

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  if(shareCount < maxShares){
    alert('Please complete sharing 5 times before submitting.');
    return;
  }
  // Disable form after submission
  disableForm();
  messageDiv.textContent = '';

  // Collect form data
  const name = document.getElementById('name').value.trim();
  const phone = document.getElementById('phone').value.trim();
  const email = document.getElementById('email').value.trim();
  const college = document.getElementById('college').value;
  const fileInput = document.getElementById('screenshot');
  const file = fileInput.files[0];

  // Upload file to Firebase or use a placeholder for demo
  // For simplicity, we'll convert image to Base64
  const reader = new FileReader();
  reader.onload = async () => {
    const fileData = reader.result; // Base64 string
    // Prepare data payload
    const payload = {
      name,
      phone,
      email,
      college,
      screenshot: fileData
    };
    // Send data to Google Sheets via Apps Script
    try {
      const response = await fetch(googleSheetURL, {
        method: 'POST',
        mode: 'no-cors',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(payload)
      });
      // For no-cors, response handling is limited
      document.getElementById('message').textContent = " Your submission has been recorded. Thanks for being part of Tech for Girls!";
      localStorage.setItem('submitted', 'true');
    } catch (error) {
      alert('Error submitting data. Please try again.');
    }
  };
  reader.readAsDataURL(file);
});

function disableForm() {
  document.querySelectorAll('input, select, button').forEach(el => {
    el.disabled = true;
  });
}
</script>
</body>
</html>