<template>
    <div ref="tradingview" :id="container" class="tradingview-widget-container">
        <div class="tradingview-widget-container__widget"></div>
        <div class="tradingview-widget-copyright"><a href="https://br.tradingview.com/markets/currencies/economic-calendar/" rel="noopener" target="_blank"><span class="blue-text">Calendário Econômico</span></a> por TradingView</div>
    </div>
</template>

<script>
const SCRIPT_ID = 'tradingview-widget-script';
const CONTAINER_ID = 'tradingview-widget-container';

export default {
    name: 'EconomicCalendar',
    data() {
        return {
            container: CONTAINER_ID
        }
    },
    props: {
        options: {
            type: Object,
            default: () => ({
                "colorTheme": "dark",
                "isTransparent": true,
                "width": "100%",
                "height": "100%",
                "locale": "br",
                "importanceFilter": "-1,0,1"
            })
        }
    },
    methods: {
        canUseDOM() {
            return typeof window !== 'undefined' && window.document && window.document.createElement;
        },
        getScriptElement() {
            return document.getElementById(SCRIPT_ID);
        },
        updateOnloadListener(onload) {
            const script = this.getScriptElement();
            const oldOnload = script.onload;
            return script.onload = () => {
                oldOnload();
                onload();
            };
        },
        scriptExists() {
            return this.getScriptElement() !== null;
        },
        appendScript(onload) {
            if (!this.canUseDOM()) {
                onload();
                return;
            }

            if (this.scriptExists()) {
                if (typeof TradingView === 'undefined') {
                    this.updateOnloadListener(onload);
                    return;
                }
                onload();
                return;
            }
            const script = document.createElement('script');
            script.id = SCRIPT_ID;
            script.type = 'text/javascript';
            script.async = true;
            script.src = 'https://s3.tradingview.com/external-embedding/embed-widget-events.js';
            script.onload = onload;
            script.textContent = JSON.stringify(this.options)

            this.$refs.tradingview.appendChild(script);
        },

        initWidget() {
            console.log('[widget-economic-calendar] loaded')
        },
    },
    mounted() {
        setTimeout(() => {
            this.appendScript(this.initWidget)
        }, 300)
    },
}
</script>
