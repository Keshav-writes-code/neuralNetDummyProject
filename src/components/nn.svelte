<script lang="ts">
    import Plotly from "plotly.js-dist";

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
            1 + Math.pow(Math.E, netIntput) + Math.pow(x,10)
        ) -x ;

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

    // ------------------------------------------------
    // ---------------- Plotting Stuff ----------------
    // ------------------------------------------------

    class TraceLines {
        x: number[];
        y: number[];
        name: string;
        type: string;
        constructor(name: string, type: string, plotter: Function) {
            this.x = [];
            this.y = [];
            this.name = name;
            this.type = type;
            for (let x = 0; x <= 1; x += 0.01) {
                this.x.push(x);
                this.y.push(plotter(x));
            }
        }
    }
    
    const traceDashedLine = {
        x: [-0.1, 1], // Use the same range as your x-axis
        y: [0.5, 0.5],
        mode: "lines",
        type: "scatter",
        line: {
            dash: "dash",
            width: 2,
            color: "green",
        },
        name: "Threshold",
    };
    $: {
        const neuralNet_Out = new TraceLines("NN Out", "scatter", neuralNetwork);
        const neuron_1_out = new TraceLines("N1 Out", "scatter", (x: number) => {
            let netIntput =
                x * inputLayer.neurons[0].weights[0] + hiddenLayer.neurons[0].bias;

            return (
                hiddenLayer.neurons[0].weights[0] *
                (Math.log(1 + Math.pow(Math.E, netIntput)))
            );
        });
        const neuron_2_out = new TraceLines("N2 Out", "scatter", (x: number) => {
            let netIntput =
                x * inputLayer.neurons[0].weights[1] + hiddenLayer.neurons[1].bias;
            return (
                hiddenLayer.neurons[1].weights[0] *
                    (Math.log(1 + Math.pow(Math.E, netIntput)) +
                outputLayer.neurons[0].bias - Math.pow(x,10))
            );
        });
        const traceDot = {
            x: [inputLayer.neurons[0].value],
            y: [outputLayer.neurons[0].value],
            mode: "markers",
            type: "scatter",
            marker: {
                size: 10,
                color: "red",
            },
            name: "Point",
        };

        const layout = {
            title: "Neural Network Function",
            xaxis: {
                title: "Input",
                // range: [-0.1,1]
            },
            yaxis: {
                title: "Output",
                // range: [-0.1,2.5    ]
            },
        };

        Plotly.newPlot(
            "plot",
            [
                neuralNet_Out,
                neuron_1_out,
                neuron_2_out,
                traceDot,
                traceDashedLine,
            ],
            layout
        );
    }
</script>

<div class="w-full flex flex-col justify-center items-center">
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
    <div class="">
        <input
            type="range"
            min="-40"
            max="40"
            step="0.01"
            class="range range-xs"
            bind:value={inputLayer.neurons[0].weights[0]}
        />
        <input
            type="range"
            min="-5"
            max="5"
            step="0.01"
            class="range range-xs"
            bind:value={inputLayer.neurons[0].weights[1]}
        />
        
        <input
            type="range"
            min="-5"
            max="5"
            step="0.01"
            class="range range-xs"
            bind:value={hiddenLayer.neurons[0].bias}
        />
        <input
            type="range"
            min="-2"
            max="2"
            step="0.01"
            class="range range-xs"
            bind:value={hiddenLayer.neurons[1].bias}
        />
        <div class="divider">More</div>
        <input
            type="range"
            min="-2"
            max="2"
            step="0.01"
            class="range range-xs"
            bind:value={hiddenLayer.neurons[0].weights[0]}
        />
        <input
            type="range"
            min="-3"
            max="3"
            step="0.01"
            class="range range-xs"
            bind:value={hiddenLayer.neurons[1].weights[0]}
        />
    </div>
</div>
