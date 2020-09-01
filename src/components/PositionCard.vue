<template>
    <div class="card">
        <h3>{{ position.instrument.replace('_', '/') }}</h3>
        <div class="position-prop">LONG@{{ position.long.averagePrice }}</div>
        <div class="position-prop">SHRT@{{ position.short.averagePrice }}</div>
        <div class="position-prop">Financing: {{ position.financing }}</div>
        <div class="position-prop">Div Adj: {{ position.dividendAdjustment }}</div>
        <div class="position-prop">Margin: {{ position.marginUsed }}</div>
        <div class="position-prop" :class="{ 'red' : position.unrealizedPL < 0 }">{{ position.unrealizedPL }}</div>
    </div>
</template>

<script>
export default {
    name: "PositionCard",
    props: {
        position: Object
    }
}
</script>

<style lang="scss" scoped>
    .card {
        width       : 10vw;
        border      : 1px solid #2c3e50;;
        padding     : 1vw;
        margin      : 1vw;
        text-align  : left;

        .position-prop {
            line-height : 24px;
        }

        .red {
            color : red;
        }
    }
</style>