---
title: Cake Volume Calculator
---
<h1>Cake Volume Calculator</h1>
<div id="inputSection">
  <h2>Cakes from the Original Recipe</h2>
  <button onclick="addCake('inputSection', inputCakeCounter)">
    Add Cake
  </button>
</div>
<div id="outputSection">
  <h2>Desired Cakes</h2>
  <button onclick="addCake('outputSection', outputCakeCounter)">
    Add Cake
  </button>
</div>
<button onclick="calculateVolume()">Calculate Proportion</button>
<p id="result"></p>
{{< script >}}
    let inputCakeCounter = 1;  // To keep track of input cake numbering
    let outputCakeCounter = 1; // To keep track of output cake numbering

    function extractCakes(containerId) {
      const container = document.getElementById(containerId);
      const cakeDivs = container.querySelectorAll(".cake");
      const cakes = [];

      cakeDivs.forEach((cakeDiv) => {
        const shapeDropdown = cakeDiv.querySelector(".shape-dropdown");
        const selectedShape = shapeDropdown.value;

        let radius, height, length, width;

        if (selectedShape === "cylinder") {
          radius = cakeDiv.querySelector(".cylinder-radius").value;
          height = cakeDiv.querySelector(".cylinder-height").value;
        } else {
          length = cakeDiv.querySelector(".prism-length").value;
          width = cakeDiv.querySelector(".prism-width").value;
          height = cakeDiv.querySelector(".prism-height").value;
        }

        const cake = {
          shape : selectedShape,
          radius : radius,
          height : height,
          length : length,
          width : width,
        };

        cakes.push(cake);
      });

      return cakes;
    }

    function toggleDimensionInputs(cakeElement) {
      const shapeDropdown = cakeElement.querySelector(".shape-dropdown");
      const selectedShape = shapeDropdown.value;
      const dimensionInputs = cakeElement.querySelectorAll(".dimension-input");

      dimensionInputs.forEach((input) => { input.style.display = "none"; });

      if (selectedShape === "cylinder") {
        cakeElement.querySelector(".cylinder-dimensions").style.display = "block";
      } else {
        cakeElement.querySelector(".prism-dimensions").style.display = "block";
      }
    }

    function calcCylinderVolume(r, h) { return Math.PI * Math.pow(r, 2) * h; }

    function calcPrismVolume(l, w, h) { return l * w * h; }

    function addCake(sectionId, counter) {
      const cakeContainer = document.getElementById(sectionId);
      const newCake = document.createElement("div");
      newCake.classList.add("cake");
      newCake.innerHTML = `
                <label>Cake ${counter}:</label>
                <select class="shape-dropdown" onchange="toggleDimensionInputs(this.parentElement)">
                    <option value="cylinder">Cylinder</option>
                    <option value="prism">Rectangular Prism</option>
                </select>
                <div class="input-container dimension-input cylinder-dimensions">
                    <input type="number" class="cylinder-radius" step="0.1" placeholder="Radius">
                    <input type="number" class="cylinder-height" step="0.1" placeholder="Height">
                </div>
                <div class="input-container dimension-input prism-dimensions" style="display:none">
                    <input type="number" class="prism-length" step="0.1" placeholder="Length">
                    <input type="number" class="prism-width" step="0.1" placeholder="Width">
                    <input type="number" class="prism-height" step="0.1" placeholder="Height">
                </div>
                <button class="delete-button" onclick="deleteCake(this)">Delete</button>
            `;
      cakeContainer.appendChild(newCake);

      // Increment the cake counter for the specific section
      if (sectionId === "inputSection") {
        inputCakeCounter++;
      } else if (sectionId === "outputSection") {
        outputCakeCounter++;
      }
    }

    function deleteCake(button) {
      // Remove the cake element when the delete button is clicked
      const cakeElement = button.parentElement;
      const section = cakeElement.parentElement;
      section.removeChild(cakeElement);
    }

    function calculateVolume() {
      // Get input cakes and output cakes
      const inputCakes = extractCakes("inputSection");
      const outputCakes = extractCakes("outputSection");

      let totalInputVolume = 0;
      let totalOutputVolume = 0;

      // Calculate the total volume of input cakes
      for (const cake of inputCakes) {
        if (cake.shape === "cylinder" && !isNaN(cake.radius) &&
            !isNaN(cake.height)) {
          totalInputVolume += calcCylinderVolume(cake.radius, cake.height);
        } else if (cake.shape === "prism" && !isNaN(cake.length) &&
                   !isNaN(cake.width) && !isNaN(cake.height)) {
          totalInputVolume += calcPrismVolume(cake.length, cake.width, cake.height);
        }
      }

      // Calculate the total volume of output cakes
      for (const cake of outputCakes) {
        if (cake.shape === "cylinder" && !isNaN(cake.radius) &&
            !isNaN(cake.height)) {
          totalOutputVolume += calcCylinderVolume(cake.radius, cake.height);
        } else if (cake.shape === "prism" && !isNaN(cake.length) &&
                   !isNaN(cake.width) && !isNaN(cake.height)) {
          totalOutputVolume +=
              calcPrismVolume(cake.length, cake.width, cake.height);
        }
      }

      // Calculate the proportion of input to output volumes
      const proportion = totalOutputVolume / totalInputVolume;

      // Display the result
      document.getElementById("result").innerHTML = `In order to make enough batter for the desired number of cakes, you should make ${proportion} times the original recipe amount.`;
    }
{{< /script >}}

<style>
  body {
    font-family: Arial, sans-serif;
    text-align: center;
    background-color: #f0f0f0;
  }

  h1 {
    color: #333;
  }

  #inputSection,
  #outputSection {
    background-color: #fff;
    padding: 20px;
    border-radius: 10px;
    margin: 20px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  }

  h2 {
    color: #333;
  }

  .cake {
    margin: 10px auto;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: space-evenly;
    max-width: 600px; /* Added max-width to limit the width */
  }

  .cake label {
    text-align: right;
    padding-right: 10px;
  }

  .cake .input-container {
    display: flex;
    justify-content: space-evenly;
  }

  .cake .delete-button {
    margin-top: 5px;
  }

  input {
    width: 80px;
    padding: 5px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  button {
    background-color: #333;
    color: #fff;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  button:hover {
    background-color: #555;
  }

  #result {
    margin: 20px 0;
    font-weight: bold;
    color: #333;
  }
</style>
