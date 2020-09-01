<template>
    <div id="app">
        <nav class="status">
            <label>
                Update (s)
                <input type="number"
                       min="1"
                       v-model="updatePeriod"
                       onkeydown="return false">
            </label>
            <div class="account-detail">
                Equity: <span>{{ this.account.NAV }}</span>
            </div>
            <div class="account-detail">
                Balance: <span>{{ this.account.balance }}</span>
            </div>
            <div class="account-detail">
                Floating P/L: <span :class="{ 'red' : this.account.unrealizedPL < 0 }">{{ this.account.unrealizedPL }}</span>
            </div>
            <div class="account-detail">
                Exposure: <span>{{ this.account.positionValue }}</span>
            </div>
            <div class="account-detail">
                Margin used: <span>{{ this.account.marginUsed }}</span>
            </div>
            <div class="account-detail">
                Margin avail: <span>{{ this.account.marginAvailable }}</span>
            </div>
            <div class="account-detail">
                Margin close: <span :class="{ 'red' : this.account.marginCloseoutPercent * 100 > 50 }">{{ (this.account.marginCloseoutPercent * 100).toFixed(2) }}</span>
            </div>
            <div class="account-detail">
                Leverage: <span>{{ this.leverage }}</span>
            </div>
            <div class="account-detail">
                Total P/L: <span :class="{ 'red' : this.account.pl < 0 }">{{ this.account.pl }}</span>
            </div>
            <div class="account-detail">
                Financing: <span>{{ this.account.financing }}</span>
            </div>
            <div class="account-detail">
                Div Adj: <span>{{ this.account.dividendAdjustment }}</span>
            </div>
        </nav>
        <position-card v-for="position in positions"
                       :key="position.instrument"
                       :position="position" />
    </div>
</template>

<script>
import PositionCard from "@/components/PositionCard";
import axios from 'axios';

export default {
    name: 'App',
    components: {
        PositionCard
    },
    data() {
        return {
            positions: null,
            updatePeriod: 5,
            account: null,
        }
    },
    computed: {
        leverage: function () {
            return (this.account.positionValue / this.account.NAV).toFixed(2);
        }
    },
    mounted() {
        this.update();
    },
    methods: {
        update() {
            axios.get(
                `https://api-fxtrade.oanda.com/v3/accounts/${process.env.VUE_APP_ACCOUNT_ID}/summary`,
                {
                    headers: {Authorization: `Bearer ${process.env.VUE_APP_BEARER_TOKEN}`}
                }
            ).then(response => {
                this.account = response.data.account;
            }).catch(e => {
                console.log(e);
            });

            axios.get(
                `https://api-fxtrade.oanda.com/v3/accounts/${process.env.VUE_APP_ACCOUNT_ID}/openPositions`,
                {
                    headers: {Authorization: `Bearer ${process.env.VUE_APP_BEARER_TOKEN}`}
                }
            ).then(response => {
                this.positions = response.data.positions;
            }).catch(e => {
                console.log(e);
            });

            setTimeout(this.update, this.updatePeriod * 1000);
        }
    }
}
</script>

<style lang="scss">
    body {
        margin: 0;
    }

    #app {
        font-family             : Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing  : antialiased;
        -moz-osx-font-smoothing : grayscale;
        text-align              : center;
        color                   : #2c3e50;
        display                 : flex;
        flex-wrap               : wrap;

        .status {
            flex-basis: 100%;
            background: #2c3e50;
            color: ghostwhite;
            display: flex;
            padding: 14px;
            align-items: baseline;

            input {
                width: 40px;
            }

            .account-detail {
                margin: 10px;
            }
        }

        .red {
            color : red;
        }
    }
</style>
