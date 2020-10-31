<script>
    import { Line } from 'vue-chartjs'

    export default {
        name: "Current",
        extends: Line,
        data() {
            return {
                charData: {
                    labels: ['', '', '', '', ''],
                    datasets: [{
                            label: 'Current price',
                            backgroundColor: 'rgba(40, 191, 191, 0.65)',
                            borderColor: 'rgb(30, 142, 142)',
                            fill: false,
                            data: [],
                        }]},
                options: {
                    responsive: true,
                    maintainAspectRatio: true,
                    animation: false,
                    aspectRatio: 10,
                },
            }
        },
        mounted() {
            this.renderChart(this.charData, this.options);
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