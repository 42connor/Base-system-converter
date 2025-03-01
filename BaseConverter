import React, { useState } from 'react';

const BaseConverter = () => {
  const [input, setInput] = useState('');
  const [fromBase, setFromBase] = useState(10);
  const [inputType, setInputType] = useState('number');
  const [result, setResult] = useState('');
  const [error, setError] = useState('');

  // Define common digit mapping for bases up to 36
  const digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
  // Mathematical operators we support in equations
  const operators = ['+', '-', '*', '/', '^', '(', ')'];

  /**
   * Convert a single number from source base to base 10
   */
  const convertNumber = (number, fromBase) => {
    // Handle negative numbers
    let isNegative = false;
    if (number.startsWith('-')) {
      isNegative = true;
      number = number.substring(1);
    }

    // Validate the base
    if (fromBase < 2 || fromBase > digits.length) {
      throw new Error(`Base must be between 2 and ${digits.length}`);
    }

    // Check if the number is valid for the given base
    const validDigits = digits.substring(0, fromBase);
    for (const char of number) {
      if (!validDigits.includes(char)) {
        throw new Error(`Invalid digit '${char}' for base ${fromBase}`);
      }
    }

    // Convert to decimal
    let decimal = 0;
    for (let i = 0; i < number.length; i++) {
      const digitValue = digits.indexOf(number[i]);
      decimal = decimal * fromBase + digitValue;
    }

    return isNegative ? -decimal : decimal;
  };

  /**
   * Tokenize an equation or expression into numbers and operators
   */
  const tokenizeEquation = (equation, fromBase) => {
    const tokens = [];
    let currentNumber = '';
    
    for (let i = 0; i < equation.length; i++) {
      const char = equation[i];
      
      // Skip spaces
      if (char === ' ') continue;
      
      // Process operators
      if (operators.includes(char)) {
        // If we have a number in progress, convert and add it
        if (currentNumber !== '') {
          tokens.push(convertNumber(currentNumber, fromBase));
          currentNumber = '';
        }
        tokens.push(char);
      } else {
        // Add to the current number
        currentNumber += char;
      }
    }
    
    // Handle any remaining number
    if (currentNumber !== '') {
      tokens.push(convertNumber(currentNumber, fromBase));
    }
    
    return tokens;
  };

  /**
   * Convert an equation from source base to base 10
   */
  const convertEquation = (equation, fromBase) => {
    const tokens = tokenizeEquation(equation, fromBase);
    return tokens.join(' ');
  };

  /**
   * Convert a string with encoded text using the specified base
   */
  const convertEncodedString = (encodedText, fromBase) => {
    // Split the encoded text by spaces, assuming each "word" is a character code
    const encodedChars = encodedText.trim().split(/\s+/);
    
    // Convert each character code and build the string
    const decodedChars = encodedChars.map(code => {
      const decimalValue = convertNumber(code, fromBase);
      try {
        return String.fromCodePoint(decimalValue);
      } catch (e) {
        return `[Invalid character code: ${decimalValue}]`;
      }
    });
    
    return decodedChars.join('');
  };

  /**
   * Main conversion function that detects the type of input and converts accordingly
   */
  const convert = () => {
    setError('');
    try {
      // Trim the input
      const trimmedInput = input.trim();
      
      if (!trimmedInput) {
        setError('Please enter an input to convert');
        return;
      }
      
      // Perform the appropriate conversion
      switch (inputType) {
        case 'number':
          setResult(`Decimal (Base 10): ${convertNumber(trimmedInput, fromBase)}`);
          break;
        
        case 'equation':
          setResult(`Equation in Decimal (Base 10): ${convertEquation(trimmedInput, fromBase)}`);
          break;
        
        case 'string':
          setResult(`Decoded String: ${convertEncodedString(trimmedInput, fromBase)}`);
          break;
        
        default:
          throw new Error(`Unknown input type: ${inputType}`);
      }
    } catch (err) {
      setError(err.message);
    }
  };

  // Generate base options for the select input
  const baseOptions = [];
  for (let i = 2; i <= 62; i++) {
    baseOptions.push(
      <option key={i} value={i}>
        Base {i} {i === 2 ? '(Binary)' : i === 8 ? '(Octal)' : i === 10 ? '(Decimal)' : i === 16 ? '(Hexadecimal)' : ''}
      </option>
    );
  }

  return (
    <div className="bg-gray-100 min-h-screen flex flex-col items-center p-6">
      <div className="bg-white p-6 rounded-lg shadow-lg w-full max-w-2xl">
        <h1 className="text-2xl font-bold mb-6 text-center text-blue-600">Base System Converter</h1>
        <p className="mb-4 text-gray-600 text-center">
          Convert numbers, equations, or encoded strings from any base to decimal (base 10)
        </p>
        
        <div className="mb-4">
          <label className="block text-gray-700 mb-2 font-medium">Input Type</label>
          <div className="flex space-x-2">
            <label className="flex-1">
              <input
                type="radio"
                name="inputType"
                value="number"
                checked={inputType === 'number'}
                onChange={() => setInputType('number')}
                className="mr-2"
              />
              Number
            </label>
            <label className="flex-1">
              <input
                type="radio"
                name="inputType"
                value="equation"
                checked={inputType === 'equation'}
                onChange={() => setInputType('equation')}
                className="mr-2"
              />
              Equation
            </label>
            <label className="flex-1">
              <input
                type="radio"
                name="inputType"
                value="string"
                checked={inputType === 'string'}
                onChange={() => setInputType('string')}
                className="mr-2"
              />
              Encoded String
            </label>
          </div>
        </div>
        
        <div className="mb-4">
          <label className="block text-gray-700 mb-2 font-medium">From Base</label>
          <select
            value={fromBase}
            onChange={(e) => setFromBase(parseInt(e.target.value))}
            className="w-full p-2 border border-gray-300 rounded"
          >
            {baseOptions}
          </select>
        </div>
        
        <div className="mb-4">
          <label className="block text-gray-700 mb-2 font-medium">
            {inputType === 'number' ? 'Enter Number' : 
             inputType === 'equation' ? 'Enter Equation' : 'Enter Encoded String'}
          </label>
          <textarea
            value={input}
            onChange={(e) => setInput(e.target.value)}
            className="w-full p-2 border border-gray-300 rounded h-24"
            placeholder={
              inputType === 'number' ? 'Example: 1010 (for binary)' : 
              inputType === 'equation' ? 'Example: 1010 + 101 (for binary)' : 
              'Example: 110 145 154 154 157 (ASCII codes in octal for "Hello")'
            }
          />
        </div>
        
        <button 
          onClick={convert}
          className="w-full bg-blue-600 text-white p-2 rounded hover:bg-blue-700 transition-colors"
        >
          Convert
        </button>
        
        {error && (
          <div className="mt-4 p-3 bg-red-100 border border-red-400 text-red-700 rounded">
            Error: {error}
          </div>
        )}
        
        {result && !error && (
          <div className="mt-4 p-3 bg-green-100 border border-green-400 text-green-700 rounded">
            {result}
          </div>
        )}
        
        <div className="mt-6 border-t border-gray-300 pt-4">
          <h2 className="text-lg font-semibold mb-2">Help</h2>
          <div className="text-sm text-gray-600">
            <p className="mb-2"><strong>Number:</strong> Convert a single number from the selected base to decimal.</p>
            <p className="mb-2"><strong>Equation:</strong> Convert a simple equation with operators (+, -, *, /, ^) where all numbers are in the selected base.</p>
            <p className="mb-2"><strong>Encoded String:</strong> Convert a sequence of space-separated numbers (representing character codes) to a text string.</p>
            <p className="mb-2">For bases above 10, use letters: A=10, B=11, ..., Z=35, a=36, b=37, ... for higher digits.</p>
          </div>
        </div>
      </div>
    </div>
  );
};

export default BaseConverter;
