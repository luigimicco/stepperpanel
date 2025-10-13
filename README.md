# StepperPanel
A stepper Vue3 component

Questo repository contiene un componente Vue 3 per creare uno stepper a slot nominati con supporto a navigazione lineare, click sui passi, eventi di cambio passo e completamento, organizzato con “props down, events up” e uso di named scoped slots.

### Caratteristiche

- Stepper con indicatori numerici, linee di avanzamento e titoli sincronizzati, costruito con slot nominati step-N e label-N ordinati per nome.
- Navigazione controllata: lineare o libera, con possibilità di click sui numeri per saltare di passo rispettando i vincoli impostati.
- API pulita: props tipizzate, eventi per two-way binding di modelValue e notifiche di step-change/step-completed.
- Pattern di customizzazione tramite named scoped slots, conforme alla guida ufficiale sugli slot.


### Installazione

- Copiare il file StepperPanel.vue nel progetto e importarlo nel componente padre, seguendo le convenzioni di Vue 3 per componenti e slots.


## README.md

```markdown
# StepperPanel (Vue 3)

StepperPanel è un componente Stepper per Vue 3 basato su slot nominati, con gestione lineare/free, click navigation opzionale, e eventi di cambio e completamento step.

## Demo rapida

```

<template>
  <div>
    <StepperPanel
      v-model="step"
      :linear="true"
      :allowClickNavigation="true"
      @step-change="onStepChange"
      @step-completed="onStepCompleted"
    >
      <!-- Etichette dei passi -->
      ```
      <template #label-0>Account</template>
      ```
      ```
      <template #label-1>Profilo</template>
      ```
      ```
      <template #label-2>Conferma</template>
      ```

      <!-- Contenuti dei passi -->
      <template #step-0>
        <h3>Crea account</h3>
        <p>Contenuti del passo 1.</p>
        <div class="stepper-navigation">
          <button @click="next">Avanti</button>
        </div>
      </template>

      <template #step-1>
        <h3>Completa profilo</h3>
        <p>Contenuti del passo 2.</p>
        <div class="stepper-navigation">
          <button @click="prev">Indietro</button>
          <button @click="next">Avanti</button>
        </div>
      </template>

      <template #step-2>
        <h3>Riepilogo</h3>
        <p>Contenuti del passo 3.</p>
        <div class="stepper-navigation">
          <button @click="prev">Indietro</button>
          <button @click="finish">Conferma</button>
        </div>
      </template>

      <!-- Slot opzionale per custom navigation wrapper -->
      <template #navigation>
        ```
        <div class="step-counter">Passo {{ step + 1 }} / 3</div>
        ```
      </template>
    </StepperPanel>
  </div>
</template>
<script>
import StepperPanel from './StepperPanel.vue'

export default {
  components: { StepperPanel },
  data() {
    return {
      step: 0,
    }
  },
  methods: {
    next() {
      this.$refs.sp?.nextStep?.()
      // oppure: this.step = Math.min(this.step + 1, 2)
    },
    prev() {
      this.$refs.sp?.previousStep?.()
      // oppure: this.step = Math.max(this.step - 1, 0)
    },
    finish() {
      alert('Completato!')
    },
    onStepChange(payload) {
      console.log('Step change', payload)
    },
    onStepCompleted(index) {
      console.log('Step completed', index)
    },
  },
}
</script>
```

Suggerimento: Gli slot nominati sono ordinati alfabeticamente (step-0, step-1, …). Assicurarsi di usare una numerazione coerente 0-based per titoli e contenuti.

## Installazione

### Copia singolo file
- Copiare `StepperPanel.vue` nella cartella dei componenti e importarlo nel progetto.

### Requisiti
- Vue 3.x.

## Utilizzo

```

<StepperPanel v-model="step" :linear="true" :allowClickNavigation="true">
  ```
  <template #label-0>Primo</template>
  ```
  ```
  <template #label-1>Secondo</template>
  ```
  ```
  <template #label-2>Terzo</template>
  ```

  ```
  <template #step-0>Contenuto passo 1</template>
  ```
  ```
  <template #step-1>Contenuto passo 2</template>
  ```
  ```
  <template #step-2>Contenuto passo 3</template>
  ```

  <template #navigation>
    <!-- opzionale: contenuto custom di navigazione -->
  </template>
</StepperPanel>
```

## API

### Props

| Prop | Tipo | Default | Descrizione |
|---|---|---|---|
| modelValue | Number | 0 | Indice corrente dello step, 0-based; usare con v-model per il two-way binding. |
| linear | Boolean | true | Se true, limita i salti avanti a un solo step (no skip multipli); i passi futuri non sono cliccabili oltre current+1. |
| allowClickNavigation | Boolean | true | Se true, abilita il click sui numeri degli step; rispettati comunque i vincoli di linear. |

### Eventi

- update:modelValue(number): emesso al cambio di step per supportare v-model.
- step-change({ from, to }): emesso ogni volta che lo step cambia, utile per tracking / validazioni.
- step-completed(index): emesso quando si avanza con nextStep, indica lo step appena completato.

### Metodi istanza

- nextStep(): avanza allo step successivo, emettendo step-completed per lo step precedente.
- previousStep(): torna allo step precedente.
- goToStep(index): prova a navigare allo step indicato rispettando allowClickNavigation e linear.

Nota: per invocare i metodi dal padre, assegnare un ref al componente e chiamare `this.$refs.mioRef.nextStep()`. Con Vue 3 Options API, i ref su componenti figli sono accessibili dopo il mount.

### Slot

- label-N: testo/markup del titolo del passo N (0-based).
- step-N: contenuto del passo N (0-based).
- navigation: contenitore opzionale per pulsanti o indicatori aggiuntivi (es. contatore passi).

Linee guida slot:
- Usare naming coerente con prefisso `label-` e `step-` e indice 0-based per mantenere l’allineamento e l’ordinamento alfabetico.
- Gli slot sono named scoped slots; possono esporre props se necessario estendere in futuro.

## Stili

Il componente include stili scoped per indicatori, linee e contenitore, ed è responsive su schermi < 768px.

Suggerimenti:
- Sovrascrivere le classi in un foglio esterno se si desidera integrare con sistemi di design o utility CSS.

## Pattern e Best Practice

- Named scoped slots per distribuire contenuti e titoli in modo dichiarativo.
- “Props down, events up”: controlling state via v-model e ascolto eventi per side-effect e validazioni.
- Evitare dipendenze esterne; la logica di ordinamento degli slot step-N è semplice e prevedibile.

## Confronto con librerie

Questo componente fornisce un approccio leggero e framework-agnostic in termini di markup, rispetto a stepper più strutturati come in PrimeVue o CoreUI, che espongono componenti Step/StepPanel/StepItem con API specifiche.

## Licenza

MIT
```


### Note di implementazione

- L’uso di v-model si basa sull’evento update:modelValue e getter/setter di computed per sincronizzare lo stato del passo.
- La gestione degli slot con prefisso e ordinamento alfanumerico è coerente con le regole dei named slots e semplifica la definizione step-0/label-0 ecc.
