<template>
  <div
    :id="container_id"
    ref="tradingview"
    class="tradingview-widget-container"
  >
    <div class="tradingview-widget-container__widget"></div>
  </div>
</template>

<script>
const SCRIPT_ID = 'crypto_mkt_script'
const CONTAINER_ID = 'crypto_mkt'

export default {
  name: 'CryptoMarket',
  props: {
    options: {
      type: Object,
      default: function () {
        return {
          width: '100%',
          height: '100%',
          defaultColumn: 'overview',
          screener_type: 'crypto_mkt',
          displayCurrency: 'USD',
          colorTheme: 'light',
          locale: 'en',
        }
      },
    },
  },
  data() {
    return {
      container_id: CONTAINER_ID,
    }
  },

  mounted() {
    setTimeout(() => {
      this.appendScript(this.initWidget)
    }, 300)
  },

  methods: {
    canUseDOM() {
      return (
        typeof window !== 'undefined' &&
        window.document &&
        window.document.createElement
      )
    },

    getScriptElement() {
      return document.getElementById(SCRIPT_ID)
    },

    updateOnloadListener(onload) {
      const script = this.getScriptElement()
      const oldOnload = script.onload
      return (script.onload = () => {
        oldOnload()
        onload()
      })
    },

    scriptExists() {
      return this.getScriptElement() !== null
    },

    appendScript(onload) {
      if (!this.canUseDOM()) {
        onload()
        return
      }
      if (this.scriptExists()) {
        if (typeof TradingView === 'undefined') {
          this.updateOnloadListener(onload)
          return
        }
        onload()
        return
      }
      const script = document.createElement('script')
      script.id = SCRIPT_ID
      script.type = 'text/javascript'
      script.async = true
      script.onload = onload
      script.textContent = JSON.stringify(this.options)
      script.src =
        'https://s3.tradingview.com/external-embedding/embed-widget-screener.js'

      this.$refs.tradingview.appendChild(script)
    },

    initWidget() {
      if (typeof TradingView === 'undefined') {
        return
      }

      new window.TradingView.widget(
        Object.assign({ container_id: this.container_id })
      )
    },
  },
}
</script>
