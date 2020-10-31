<script>
    import { Bar } from 'vue-chartjs'

    export default {
        name: "Graph",
        extends: Bar,
        data() {
            return {
                charData: {
                    labels: [-9, -8, -7, -6, -5, -4, -3, -2, 'Previous', 'Last'],
                    datasets: [{
                            label: 'open/close',
                            backgroundColor: 'rgba(93, 255, 124, 0.3)',
                            borderColor: 'rgb(37, 191, 56)',
                            borderWidth: 1,
                            barPercentage: 0.9,
                            data: [],
                        },
                        {
                            label: 'low/high prices',
                            backgroundColor: 'rgba(248, 121, 121, 0.3)',
                            borderColor: 'rgba(248, 121, 121, 1)',
                            borderWidth: 1,
                            barPercentage: 0.9,
                            data: [],
                        }]},
                options: {
                    scales: {
                        xAxes: [{
                            stacked: true
                        }],
                        yAxes: [{
                            stacked: false
                        }]
                    }
                },
            }
        },
        watch: {
            charData() {
                this.renderChart(this.charData, this.options);
            },
        }
    }
</script>

<style scoped>

</style>