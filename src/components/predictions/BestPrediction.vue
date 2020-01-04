<template>
    <div>{{ bestPrediction.className }}: {{ bestPrediction.probability.toFixed(2) }}</div>
</template>

<script>
export default {
    props: {
        predictions: {
            type: Array,
            required: true
        }
    },

    computed: {
        bestPrediction() {
            return this.predictions.reduce(
                (agg, prediction) => {
                    return prediction.probability > agg.probability ? prediction : agg;
                },
                { probability: 0 }
            );
        }
    }
};
</script>
