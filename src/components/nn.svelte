<script lang="ts">
    class neuron {
        value: number;
        bias: number;
        weights: number[];

        constructor(nextLayer_neurons: number) {
            this.value = 0;
            this.bias = 0;
            this.weights = new Array(nextLayer_neurons).fill(0);
        }
        show() {
            console.log(this);
        }
    }

    class layer {
        neurons: neuron[];
        constructor(size: number, nextLayerSize: number) {
            this.neurons = new Array(size)
                .fill(0)
                .map(() => new neuron(nextLayerSize));
        }
        show() {
            console.log(this.neurons);
        }
    }

    let inputLayer = new layer(1, 2);
    let hiddenLayer = new layer(2, 1);
    let outputLayer = new layer(1, 0);

    const dataset = [
        [0, 0],
        [0.5, 1],
        [1, 0],
    ];
    inputLayer.neurons[0].weights[0] = -34.4;
    inputLayer.neurons[0].weights[1] = -2.52;

    hiddenLayer.neurons[0].bias = 2.14;
    hiddenLayer.neurons[1].bias = 1.29;
    hiddenLayer.neurons[0].weights[0] = -1.3;
    hiddenLayer.neurons[1].weights[0] = 2.28;

    outputLayer.neurons[0].bias = -0.58;

    function neuralNetwork(x: number) {
        console.clear();
        let result = 0;
        // Value Calculation for every neuron in hidden layer
        // First Neuron
        let netIntput =
            x * inputLayer.neurons[0].weights[0] + hiddenLayer.neurons[0].bias;

        hiddenLayer.neurons[0].value = Math.log(
            1 + Math.pow(Math.E, netIntput)
        );

        // Second Neuron
        netIntput =
            x * inputLayer.neurons[0].weights[1] + hiddenLayer.neurons[1].bias;
        hiddenLayer.neurons[1].value = Math.log(
            1 + Math.pow(Math.E, netIntput)
        );

        // Value Calculation for every neuron Output layer
        // First Neuron
        console.log(
            "Output Layer 0",
            (
                hiddenLayer.neurons[0].value * hiddenLayer.neurons[0].weights[0]
            ).toFixed(7)
        );

        result =
            hiddenLayer.neurons[0].value * hiddenLayer.neurons[0].weights[0] +
            hiddenLayer.neurons[1].value * hiddenLayer.neurons[1].weights[0] +
            outputLayer.neurons[0].bias;
        return result;
    }
    $: outputLayer.neurons[0].value = neuralNetwork(
        inputLayer.neurons[0].value
    );

    import Plotly from "plotly.js-dist";
    const xValues = [];
    const yValues = [];
    for (let x = -10; x <= 10; x += 0.1) {
        xValues.push(x);
        yValues.push(neuralNetwork(x));
    }

    // Plot the function
    const trace = {
        x: xValues,
        y: yValues,
        type: "scatter",
    };

    const layout = {
        title: "Neural Network Function",
        xaxis: {
            title: "Input",
            range: [-0.1, 1], // Set the range for the x-axis
        },
        yaxis: { 
            title: "Output", 
            range: [-2, 4]
        },
    };

    Plotly.newPlot("plot", [trace], layout);
</script>

<div class="w-full flex flex-col justify-center items-center ">
    <label class="form-control w-full max-w-xs">
        <div class="label">
            <span class="label-text">Dossage Value</span>
            <span class="label-text-alt">0 - 1</span>
        </div>
        <input
            type="range"
            min="0"
            max="1"
            step="0.01"
            class="range"
            bind:value={inputLayer.neurons[0].value}
        />
        <div class="label">
            <span class="label-text-alt"
                >Value = {inputLayer.neurons[0].value}</span
            >
        </div>
    </label>
    <div class="stats shadow">
        <div class="stat">
            <div class="stat-title">Effective?</div>
            <div class="stat-value">
                {Math.round(outputLayer.neurons[0].value) == 0 ? "No" : "Yes"}
            </div>
            <div class="stat-desc">
                {outputLayer.neurons[0].value.toFixed(5)}
            </div>
        </div>
    </div>
</div>
