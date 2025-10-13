# StepperPanel (Vue 3)

StepperPanel è un componente Stepper per Vue 3 basato su slot nominati, con gestione lineare/free, click navigation opzionale, e eventi di cambio e completamento step.

## Demo rapida

```vue
<template>
  <div>
    <StepperPanel
      ref="sp"
      v-model="step"
      :linear="true"
      :allowClickNavigation="true"
      @step-change="onStepChange"
      @step-completed="onStepCompleted"
    >
      <!-- Etichette dei passi -->
      <template #label-0>Account</template>
      <template #label-1>Profilo</template>
      <template #label-2>Conferma</template>

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
        <div class="step-counter">Passo {{ step + 1 }} / 3</div>
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
      this.$refs.sp.nextStep()
      // oppure: this.step = Math.min(this.step + 1, 2)
    },
    prev() {
      this.$refs.sp.previousStep()
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

**Suggerimento:** Gli slot nominati sono ordinati alfabeticamente (step-0, step-1, …). Assicurarsi di usare una numerazione coerente 0-based per titoli e contenuti.

## Installazione

### Copia singolo file
Copiare `StepperPanel.vue` nella cartella dei componenti e importarlo nel progetto.

### Requisiti
- Vue 3.x

## Utilizzo

```vue
<StepperPanel v-model="step" :linear="true" :allowClickNavigation="true">
  <template #label-0>Primo</template>
  <template #label-1>Secondo</template>
  <template #label-2>Terzo</template>

  <template #step-0>Contenuto passo 1</template>
  <template #step-1>Contenuto passo 2</template>
  <template #step-2>Contenuto passo 3</template>

  <template #navigation>
    <!-- opzionale: contenuto custom di navigazione -->
  </template>
</StepperPanel>
```

## API

### Props

| Prop | Tipo | Default | Descrizione |
|---|---|---|---|
| `modelValue` | Number | 0 | Indice corrente dello step, 0-based; usare con v-model per il two-way binding |
| `linear` | Boolean | true | Se true, limita i salti avanti a un solo step (no skip multipli); i passi futuri non sono cliccabili oltre current+1 |
| `allowClickNavigation` | Boolean | true | Se true, abilita il click sui numeri degli step; rispettati comunque i vincoli di linear |

### Eventi

| Evento | Parametri | Descrizione |
|---|---|---|
| `update:modelValue` | `number` | Emesso al cambio di step per supportare v-model |
| `step-change` | `{ from: number, to: number }` | Emesso ogni volta che lo step cambia, utile per tracking / validazioni |
| `step-completed` | `number` | Emesso quando si avanza con nextStep, indica lo step appena completato |

### Metodi istanza

| Metodo | Descrizione |
|---|---|
| `nextStep()` | Avanza allo step successivo, emettendo step-completed per lo step precedente |
| `previousStep()` | Torna allo step precedente |
| `goToStep(index)` | Prova a navigare allo step indicato rispettando allowClickNavigation e linear |

**Nota:** per invocare i metodi dal padre, assegnare un ref al componente e chiamare `this.$refs.mioRef.nextStep()`. Con Vue 3 Options API, i ref su componenti figli sono accessibili dopo il mount.

### Slot

| Slot | Descrizione |
|---|---|
| `label-N` | Testo/markup del titolo del passo N (0-based) |
| `step-N` | Contenuto del passo N (0-based) |
| `navigation` | Contenitore opzionale per pulsanti o indicatori aggiuntivi (es. contatore passi) |

**Linee guida slot:**
- Usare naming coerente con prefisso `label-` e `step-` e indice 0-based per mantenere l'allineamento e l'ordinamento alfabetico
- Gli slot sono named scoped slots; possono esporre props se necessario estendere in futuro

## Stili

Il componente include stili scoped per indicatori, linee e contenitore, ed è responsive su schermi < 768px.

**Suggerimenti:**
- Sovrascrivere le classi in un foglio esterno se si desidera integrare con sistemi di design o utility CSS

## Pattern e Best Practice

- **Named scoped slots** per distribuire contenuti e titoli in modo dichiarativo
- **"Props down, events up"**: controlling state via v-model e ascolto eventi per side-effect e validazioni
- Evitare dipendenze esterne; la logica di ordinamento degli slot step-N è semplice e prevedibile

## Confronto con librerie

Questo componente fornisce un approccio leggero e framework-agnostic in termini di markup, rispetto a stepper più strutturati come in PrimeVue o CoreUI, che espongono componenti Step/StepPanel/StepItem con API specifiche.

## Esempio completo con validazione

```vue
<template>
  <div>
    <StepperPanel
      ref="stepper"
      v-model="currentStep"
      :linear="true"
      :allowClickNavigation="true"
      @step-change="handleStepChange"
      @step-completed="handleStepCompleted"
    >
      <template #label-0>Dati personali</template>
      <template #label-1>Preferenze</template>
      <template #label-2>Conferma</template>

      <template #step-0>
        <div class="step-content">
          <h3>Inserisci i tuoi dati</h3>
          <form @submit.prevent>
            <input v-model="userData.name" placeholder="Nome" required />
            <input v-model="userData.email" type="email" placeholder="Email" required />
          </form>
          <div class="step-actions">
            <button @click="nextWithValidation" :disabled="!isStep0Valid">Avanti</button>
          </div>
        </div>
      </template>

      <template #step-1>
        <div class="step-content">
          <h3>Seleziona le tue preferenze</h3>
          <label>
            <input v-model="userData.newsletter" type="checkbox" />
            Ricevi newsletter
          </label>
          <div class="step-actions">
            <button @click="$refs.stepper.previousStep()">Indietro</button>
            <button @click="$refs.stepper.nextStep()">Avanti</button>
          </div>
        </div>
      </template>

      <template #step-2>
        <div class="step-content">
          <h3>Riepilogo</h3>
          <p><strong>Nome:</strong> {{ userData.name }}</p>
          <p><strong>Email:</strong> {{ userData.email }}</p>
          <p><strong>Newsletter:</strong> {{ userData.newsletter ? 'Sì' : 'No' }}</p>
          <div class="step-actions">
            <button @click="$refs.stepper.previousStep()">Indietro</button>
            <button @click="submit">Conferma</button>
          </div>
        </div>
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
      currentStep: 0,
      userData: {
        name: '',
        email: '',
        newsletter: false
      }
    }
  },
  computed: {
    isStep0Valid() {
      return this.userData.name && this.userData.email
    }
  },
  methods: {
    nextWithValidation() {
      if (this.isStep0Valid) {
        this.$refs.stepper.nextStep()
      }
    },
    handleStepChange({ from, to }) {
      console.log(`Passaggio da step ${from} a step ${to}`)
    },
    handleStepCompleted(stepIndex) {
      console.log(`Step ${stepIndex} completato`)
    },
    submit() {
      console.log('Dati inviati:', this.userData)
      alert('Registrazione completata!')
    }
  }
}
</script>

<style>
.step-content {
  padding: 20px 0;
}

.step-actions {
  margin-top: 20px;
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.step-actions button {
  padding: 8px 16px;
  border: 1px solid #ccc;
  background: white;
  border-radius: 4px;
  cursor: pointer;
}

.step-actions button:hover {
  background: #f5f5f5;
}

.step-actions button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
```

## Licenza

MIT

## Contributi

I contributi sono benvenuti! Aprire una issue o una pull request per miglioramenti o correzioni.

