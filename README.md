const displayElement = document.getElementById('display');
let currentValue = '0';
let waitingForOperand = false;

function updateDisplay() {
  displayElement.textContent = currentValue;
}

function inputDigit(digit) {
  if (waitingForOperand) {
    currentValue = digit;
    waitingForOperand = false;
    return;
  }
  if (currentValue === '0' && digit !== '.') {
    currentValue = digit;
  } else if (currentValue === '-0' && digit !== '.') {
    currentValue = '-' + digit;
  } else {
    currentValue += digit;
  }
}

function inputDecimal() {
  if (waitingForOperand) {
    currentValue = '0.';
    waitingForOperand = false;
    return;
  }
  if (!currentValue.includes('.')) {
    currentValue += '.';
  }
}

function clearCalculator() {
  currentValue = '0';
  waitingForOperand = false;
}

function deleteLastChar() {
  if (currentValue.length === 1 || currentValue === '-0') {
    currentValue = '0';
    return;
  }
  currentValue = currentValue.slice(0, -1);
}

function toggleSign() {
  if (currentValue.startsWith('-')) {
    currentValue = currentValue.substring(1);
  } else {
    currentValue = '-' + currentValue;
  }
}

function calculateExpression(expression) {
  try {
    const sanitized = expression.replace(/[^0-9.+\-*/()%]/g, '');
    const result = Function(`"use strict"; return (${sanitized})`)();
    return String(result);
  } catch (error) {
    return 'Error';
  }
}

function calculate() {
  currentValue = calculateExpression(currentValue);
  waitingForOperand = true;
}

const buttons = document.querySelectorAll('.button');
buttons.forEach(button => {
  button.addEventListener('click', () => {
    const value = button.dataset.value;
    const action = button.dataset.action;

    if (action === 'clear') {
      clearCalculator();
    } else if (action === 'delete') {
      deleteLastChar();
    } else if (action === 'toggle-sign') {
      toggleSign();
    } else if (action === 'calculate') {
      calculate();
    } else if (value === '.') {
      inputDecimal();
    } else if (value) {
      inputDigit(value);
    }

    updateDisplay();
  });
});

updateDisplay();
