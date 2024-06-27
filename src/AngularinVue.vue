<template>
  <div class="bg-dark">
    <angular-preview ref="customElement">
      <slot></slot>
    </angular-preview>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, defineProps } from 'vue';

const props = defineProps({
  controllerCode: {
    type: String,
    required: true,
  },
});

const customElement = ref(null);

const updateControllerCode = (code) => {
  if (customElement.value) {
    customElement.value.controllerCode = code;
  }
};

watch(
  () => props.controllerCode,
  (newCode) => {
    updateControllerCode(newCode);
  }
);

onMounted(() => {
  if (!customElements.get('angular-preview')) {
    class AngularPrevew extends HTMLElement {
      constructor() {
        super();
        this.attachShadow({ mode: 'open' });
        this.shadowRoot.innerHTML = `
            <div class="container">
              <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
              <slot></slot>
            </div>
          `;
      }

      connectedCallback() {
        this.loadAngular();
      }

      loadAngular() {
        const angularScript = document.createElement('script');
        angularScript.src =
          'https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js';
        angularScript.onload = () => this.loadUIBootstrap();
        this.shadowRoot.appendChild(angularScript);
      }

      loadUIBootstrap() {
        const uiBootstrapScript = document.createElement('script');
        uiBootstrapScript.src =
          'https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.min.js';
        uiBootstrapScript.onload = () => this.initAngularApp();
        this.shadowRoot.appendChild(uiBootstrapScript);
      }

      initAngularApp() {
        const script = document.createElement('script');
        script.textContent = this.controllerCode;
        this.shadowRoot.appendChild(script);
      }

      set controllerCode(code) {
        this._controllerCode = code;
        if (this.shadowRoot) {
          const script = this.shadowRoot.querySelector('script:last-child');
          if (script) {
            script.textContent = code;
          }
        }
      }

      get controllerCode() {
        return this._controllerCode;
      }
    }

    customElements.define('angular-preview', AngularPrevew);
  }

  updateControllerCode(props.controllerCode);
});
</script>
