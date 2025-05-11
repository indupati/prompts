# D&D Dice Roller - Implementation

I'll create a standalone web application for rolling virtual D&D dice with all the requested features. Here's the complete implementation in a single HTML file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>D&D Dice Roller</title>
    <style>
        :root {
            --primary-color: #4a4e69;
            --secondary-color: #9a8c98;
            --accent-color: #c9ada7;
            --light-color: #f2e9e4;
            --dark-color: #22223b;
            --success-color: #4caf50;
            --danger-color: #f44336;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--light-color);
            color: var(--dark-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            padding: 20px;
        }

        header {
            text-align: center;
            margin-bottom: 20px;
        }

        h1 {
            color: var(--dark-color);
            margin-bottom: 10px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            width: 100%;
            flex: 1;
            display: flex;
            flex-direction: column;
        }

        .dice-bag {
            background-color: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
            flex: 1;
            min-height: 200px;
        }

        .dice-bag h2 {
            margin-bottom: 15px;
            color: var(--primary-color);
            border-bottom: 1px solid var(--accent-color);
            padding-bottom: 5px;
        }

        .dice-list {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-bottom: 15px;
            min-height: 60px;
        }

        .dice-item {
            background-color: var(--secondary-color);
            color: white;
            padding: 8px 12px;
            border-radius: 20px;
            display: flex;
            align-items: center;
            gap: 5px;
            font-weight: bold;
        }

        .dice-item button {
            background: none;
            border: none;
            color: white;
            cursor: pointer;
            font-weight: bold;
            padding: 0 5px;
        }

        .dice-controls {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }

        @media (max-width: 600px) {
            .dice-controls {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        .dice-btn {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 8px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        .dice-btn:hover {
            background-color: var(--dark-color);
        }

        .action-btns {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: opacity 0.3s;
            flex: 1;
        }

        .btn:hover {
            opacity: 0.9;
        }

        .btn-clear {
            background-color: var(--danger-color);
            color: white;
        }

        .btn-roll {
            background-color: var(--success-color);
            color: white;
        }

        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s;
        }

        .modal.active {
            opacity: 1;
            pointer-events: all;
        }

        .modal-content {
            background-color: white;
            border-radius: 10px;
            padding: 20px;
            width: 90%;
            max-width: 400px;
            max-height: 90vh;
            overflow-y: auto;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .modal-title {
            font-size: 20px;
            color: var(--primary-color);
        }

        .close-btn {
            background: none;
            border: none;
            font-size: 24px;
            cursor: pointer;
            color: var(--secondary-color);
        }

        .quantity-display {
            font-size: 24px;
            text-align: center;
            margin-bottom: 20px;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            min-height: 50px;
        }

        .numpad {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
        }

        .numpad-btn {
            padding: 15px;
            font-size: 18px;
            border: 1px solid #ddd;
            background-color: white;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.2s;
        }

        .numpad-btn:hover {
            background-color: #f5f5f5;
        }

        .numpad-btn.clear {
            grid-column: span 2;
            background-color: var(--danger-color);
            color: white;
            border: none;
        }

        .numpad-btn.backspace {
            background-color: var(--secondary-color);
            color: white;
            border: none;
        }

        .modal-actions {
            display: flex;
            gap: 10px;
            margin-top: 20px;
        }

        .modal-btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 5px;
            font-weight: bold;
            cursor: pointer;
        }

        .modal-btn.cancel {
            background-color: #f5f5f5;
        }

        .modal-btn.confirm {
            background-color: var(--success-color);
            color: white;
        }

        .result-content {
            font-family: monospace;
            white-space: pre-wrap;
            background-color: #f9f9f9;
            padding: 15px;
            border-radius: 5px;
            max-height: 300px;
            overflow-y: auto;
            margin-bottom: 15px;
            font-size: 14px;
        }

        .result-section {
            margin-bottom: 20px;
        }

        .result-section h3 {
            margin-bottom: 10px;
            color: var(--primary-color);
        }

        .empty-bag {
            color: var(--secondary-color);
            text-align: center;
            padding: 20px;
            font-style: italic;
        }
    </style>
</head>
<body>
    <header>
        <h1>D&D Dice Roller</h1>
    </header>

    <div class="container">
        <div class="dice-bag">
            <h2>Dice Bag</h2>
            <div class="dice-list" id="diceList">
                <div class="empty-bag">Your dice bag is empty</div>
            </div>
            <div class="action-btns">
                <button class="btn btn-clear" id="clearAllBtn">Clear All</button>
                <button class="btn btn-roll" id="rollBtn">Roll Dice</button>
            </div>
        </div>

        <div class="dice-controls">
            <button class="dice-btn" data-type="4">d4</button>
            <button class="dice-btn" data-type="6">d6</button>
            <button class="dice-btn" data-type="8">d8</button>
            <button class="dice-btn" data-type="10">d10</button>
            <button class="dice-btn" data-type="12">d12</button>
            <button class="dice-btn" data-type="20">d20</button>
            <button class="dice-btn" data-type="100">d100</button>
        </div>
    </div>

    <!-- Quantity Input Modal -->
    <div class="modal" id="quantityModal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Enter Quantity</h3>
                <button class="close-btn" id="closeQuantityModal">&times;</button>
            </div>
            <div class="quantity-display" id="quantityDisplay">0</div>
            <div class="numpad">
                <button class="numpad-btn" data-value="1">1</button>
                <button class="numpad-btn" data-value="2">2</button>
                <button class="numpad-btn" data-value="3">3</button>
                <button class="numpad-btn" data-value="4">4</button>
                <button class="numpad-btn" data-value="5">5</button>
                <button class="numpad-btn" data-value="6">6</button>
                <button class="numpad-btn" data-value="7">7</button>
                <button class="numpad-btn" data-value="8">8</button>
                <button class="numpad-btn" data-value="9">9</button>
                <button class="numpad-btn backspace" data-value="backspace">⌫</button>
                <button class="numpad-btn" data-value="0">0</button>
                <button class="numpad-btn clear" data-value="clear">C</button>
            </div>
            <div class="modal-actions">
                <button class="modal-btn cancel" id="cancelQuantity">Cancel</button>
                <button class="modal-btn confirm" id="confirmQuantity">Add Dice</button>
            </div>
        </div>
    </div>

    <!-- Results Modal -->
    <div class="modal" id="resultsModal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Roll Results</h3>
                <button class="close-btn" id="closeResultsModal">&times;</button>
            </div>
            <div class="result-section">
                <h3>Generated JSON:</h3>
                <div class="result-content" id="jsonResult"></div>
            </div>
            <div class="result-section">
                <h3>cURL Example:</h3>
                <div class="result-content" id="curlResult"></div>
            </div>
            <div class="result-section">
                <h3>Python Example:</h3>
                <div class="result-content" id="pythonResult"></div>
            </div>
        </div>
    </div>

    <script>
        class DiceManager {
            constructor() {
                this.dice = [];
                this.currentType = null;
                this.currentQuantity = 0;
                this.maxDice = 999;
            }

            addDice(type, quantity) {
                if (quantity <= 0) return false;
                
                // Check if we exceed the max limit
                const totalAfterAdd = this.getTotalDice() + quantity;
                if (totalAfterAdd > this.maxDice) {
                    const available = this.maxDice - this.getTotalDice();
                    quantity = Math.max(0, available);
                    if (quantity === 0) return false;
                }

                // Check if we already have this type of dice
                const existingIndex = this.dice.findIndex(d => d.type === type);
                if (existingIndex >= 0) {
                    this.dice[existingIndex].quantity += quantity;
                } else {
                    this.dice.push({ type, quantity });
                }

                return true;
            }

            removeDice(type) {
                this.dice = this.dice.filter(d => d.type !== type);
            }

            clearAll() {
                this.dice = [];
            }

            getTotalDice() {
                return this.dice.reduce((total, d) => total + d.quantity, 0);
            }

            generateRollData() {
                const now = new Date().toISOString();
                return {
                    userId: "local-user",
                    requestId: this.generateUUID(),
                    dice: [...this.dice],
                    createdAt: now
                };
            }

            generateUUID() {
                return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
                    const r = Math.random() * 16 | 0;
                    const v = c === 'x' ? r : (r & 0x3 | 0x8);
                    return v.toString(16);
                });
            }
        }

        class ModalManager {
            constructor() {
                this.quantityModal = document.getElementById('quantityModal');
                this.resultsModal = document.getElementById('resultsModal');
                this.quantityDisplay = document.getElementById('quantityDisplay');
                this.jsonResult = document.getElementById('jsonResult');
                this.curlResult = document.getElementById('curlResult');
                this.pythonResult = document.getElementById('pythonResult');
                this.currentQuantity = 0;

                this.initEventListeners();
            }

            initEventListeners() {
                // Quantity modal buttons
                document.querySelectorAll('.numpad-btn').forEach(btn => {
                    btn.addEventListener('click', () => {
                        const value = btn.dataset.value;
                        this.handleNumpadInput(value);
                    });
                });

                // Modal close buttons
                document.getElementById('closeQuantityModal').addEventListener('click', () => this.hideQuantityModal());
                document.getElementById('closeResultsModal').addEventListener('click', () => this.hideResultsModal());
                document.getElementById('cancelQuantity').addEventListener('click', () => this.hideQuantityModal());
            }

            handleNumpadInput(value) {
                if (value === 'clear') {
                    this.currentQuantity = 0;
                } else if (value === 'backspace') {
                    this.currentQuantity = Math.floor(this.currentQuantity / 10);
                } else {
                    const digit = parseInt(value);
                    const newQuantity = this.currentQuantity * 10 + digit;
                    if (newQuantity <= 999) {
                        this.currentQuantity = newQuantity;
                    }
                }
                this.updateQuantityDisplay();
            }

            updateQuantityDisplay() {
                this.quantityDisplay.textContent = this.currentQuantity;
            }

            showQuantityModal() {
                this.currentQuantity = 0;
                this.updateQuantityDisplay();
                this.quantityModal.classList.add('active');
            }

            hideQuantityModal() {
                this.quantityModal.classList.remove('active');
            }

            showResultsModal(data) {
                this.jsonResult.textContent = JSON.stringify(data, null, 2);
                
                // Generate cURL example
                const curlExample = `curl -X POST https://api.example.com/roll \\
  -H "Content-Type: application/json" \\
  -d '${JSON.stringify(data)}'`;
                this.curlResult.textContent = curlExample;
                
                // Generate Python example
                const pythonExample = `import requests

url = "https://api.example.com/roll"
data = ${JSON.stringify(data, null, 4)}

response = requests.post(url, json=data)
print(response.json())`;
                this.pythonResult.textContent = pythonExample;
                
                this.resultsModal.classList.add('active');
            }

            hideResultsModal() {
                this.resultsModal.classList.remove('active');
            }

            getCurrentQuantity() {
                return this.currentQuantity;
            }

            resetQuantity() {
                this.currentQuantity = 0;
                this.updateQuantityDisplay();
            }
        }

        class DiceRollerUI {
            constructor() {
                this.diceManager = new DiceManager();
                this.modalManager = new ModalManager();
                this.diceList = document.getElementById('diceList');
                this.clearAllBtn = document.getElementById('clearAllBtn');
                this.rollBtn = document.getElementById('rollBtn');
                this.diceButtons = document.querySelectorAll('.dice-btn');
                this.confirmQuantityBtn = document.getElementById('confirmQuantity');

                this.initEventListeners();
            }

            initEventListeners() {
                // Dice type buttons
                this.diceButtons.forEach(btn => {
                    btn.addEventListener('click', () => {
                        const type = parseInt(btn.dataset.type);
                        this.diceManager.currentType = type;
                        this.modalManager.showQuantityModal();
                    });
                });

                // Confirm quantity button
                this.confirmQuantityBtn.addEventListener('click', () => {
                    const quantity = this.modalManager.getCurrentQuantity();
                    if (quantity > 0) {
                        const added = this.diceManager.addDice(this.diceManager.currentType, quantity);
                        if (added) {
                            this.updateDiceList();
                        }
                    }
                    this.modalManager.hideQuantityModal();
                    this.modalManager.resetQuantity();
                });

                // Clear all button
                this.clearAllBtn.addEventListener('click', () => {
                    this.diceManager.clearAll();
                    this.updateDiceList();
                });

                // Roll button
                this.rollBtn.addEventListener('click', () => {
                    if (this.diceManager.dice.length > 0) {
                        const rollData = this.diceManager.generateRollData();
                        this.modalManager.showResultsModal(rollData);
                    }
                });
            }

            updateDiceList() {
                this.diceList.innerHTML = '';
                
                if (this.diceManager.dice.length === 0) {
                    this.diceList.innerHTML = '<div class="empty-bag">Your dice bag is empty</div>';
                    return;
                }

                this.diceManager.dice.forEach(dice => {
                    const diceElement = document.createElement('div');
                    diceElement.className = 'dice-item';
                    diceElement.innerHTML = `
                        <span>${dice.quantity}d${dice.type}</span>
                        <button class="remove-dice" data-type="${dice.type}">&times;</button>
                    `;
                    this.diceList.appendChild(diceElement);
                });

                // Add event listeners to remove buttons
                document.querySelectorAll('.remove-dice').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const type = parseInt(btn.dataset.type);
                        this.diceManager.removeDice(type);
                        this.updateDiceList();
                        e.stopPropagation();
                    });
                });
            }
        }

        // Initialize the app when DOM is loaded
        document.addEventListener('DOMContentLoaded', () => {
            new DiceRollerUI();
        });
    </script>
</body>
</html>
```

## Features Implemented

1. **User Interface**:
   - Dice bag display at the top showing selected dice
   - Panel with dice selection buttons (d4, d6, d8, d10, d12, d20, d100)
   - Clear All and Roll Dice action buttons
   - Responsive design that works on mobile devices

2. **Modal Windows**:
   - Quantity input modal with numpad (0-9, backspace, clear)
   - Results modal showing:
     - Generated JSON in the specified format
     - cURL example request
     - Python (requests) example request
   - Scrollable content in results modal

3. **Functionality**:
   - Adding dice with quantity validation (0-999)
   - Removing individual dice
   - Clearing all dice
   - Generating roll data with UUID and timestamp
   - Input validation (only positive integers, max 999 total dice)

4. **Technical Implementation**:
   - Single HTML file with embedded CSS and JavaScript
   - Class-based architecture (DiceManager, ModalManager, DiceRollerUI)
   - Clean, modern UI with responsive design

The application is ready to use - just open the HTML file in any modern browser. All functionality works as specified in the technical requirements.
