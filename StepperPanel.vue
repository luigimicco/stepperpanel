<template>
  <div class="stepper-container">
    <div class="stepper-header-number">
      <div
        v-for="i in stepSlots.length"
        :class="['step-item', { active: i-1 === currentStep, completed: i <= currentStep }]"
        :key="'point_' + i"
      >
        ```
        <div class="step-number" @click="goToStep(i - 1)">{{ i }}</div>
        ```
        <div v-if="i < stepSlots.length" class="step-line"></div>
      </div>
    </div>
    <div class="stepper-header-title">
      <div
        v-for="i in stepSlots.length"
        :class="['step-title-item', { active: i-1 === currentStep, completed: i <= currentStep }]"
        :key="'title_' + i"
      >
        ```
        <div class="step-title"><slot :name="'label-' + (i-1)" /></div>
        ```
      </div>
    </div>

    <div class="stepper-content">
      <slot :name="'step-' + currentStep" />
    </div>

    <div v-if="$slots.navigation">
      <slot name="navigation" />
    </div>

  </div>
</template>
<script>
export default {
  name: 'StepperPanel',
  props: {
    modelValue: {
      type: Number,
      default: 0
    },
    linear: {
      type: Boolean,
      default: true
    }   , 
    allowClickNavigation: {
      type: Boolean,
      default: true
    }
  },
  emits: ['update:modelValue', 'step-change', 'step-completed'],
  computed: {
    currentStep: {
      get() {
        return Math.max(0, Math.min(this.modelValue, this.stepSlots.length - 1));
      },
      set(value) {
        this.$emit('update:modelValue', value);
      }
    },
    // Recupera tutti gli slot con nome "step" (possibilità Option API)
    stepSlots() {
      const slots = this.$slots;
      // Ordina i vNode degli slot "step" in base all'ordine di definizione
      return Object.keys(slots)
        .filter(name => name.startsWith('step-'))
        .sort()
        .map(name => slots[name][0]);
    }
  },
  methods: {
    nextStep() {
      if (this.currentStep < this.stepSlots.length - 1) {
        const newStep = this.currentStep + 1;
        this.changeStep(newStep); 
        this.$emit('step-completed', newStep - 1);
      }
    },
    previousStep() {
      if (this.currentStep > 0) {
        const newStep = this.currentStep - 1;
        this.changeStep(newStep);        
      }
    },
    goToStep(index) {
      if (!this.allowClickNavigation) return; 
      if (this.linear && index > (this.currentStep + 1)) return;
      if (index >= 0 && index < this.stepSlots.length) {
        this.changeStep(index);
      }
    },
    changeStep(newStep) {
      const oldStep = this.currentStep;
      this.currentStep = newStep;
      this.$emit('step-change', {
        from: oldStep,
        to: newStep
      });
    }

  }
};
</script>
<style scoped>
.stepper-container {
  width: 100%;
  margin: 0 auto;
}

.stepper-header-number {
  width: 100%;
  display: flex;
  justify-content: space-between;
    margin-top: 15px;
}

.stepper-header-title {
  width: 100%;
  display: flex;
  justify-content: space-between;
  margin-bottom: 30px;
}


.step-line {
    flex: 1;
    margin-top: 19px;
    margin-left: 10px;
    margin-right: 10px;
    height: 2px;
    background: #e5e7eb;
    transition: background-color 0.3s ease;  
}

.step-item.completed .step-line {
  background: #10b981;
}

.step-title-item {
  display: flex;
  justify-content: center;
  transition: all 0.3s ease;
  flex: 1;
}

.step-title-item:first-child {
  justify-content:flex-start;
  flex:0.5;
}

.step-title-item:last-child {
  justify-content:flex-end;
  flex:0.5;
  text-align: right;
}


.step-item {
  display: flex;
  transition: all 0.3s ease;
  flex: 1;
}

.step-item:last-child {
  flex: 0;
}

.step-item:hover:not(.disabled) .step-number {
  transform: scale(1.1);
}

.step-number {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 16px;
  margin-bottom: 8px;
  transition: all 0.3s ease;
  border: 2px solid #e0e7ff;
  background: white;
  color: #6b7280;
  cursor: pointer;
}

.step-item.active .step-number {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
  box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1);
}

.step-item.completed .step-number {
  background: #10b981;
  color: white;
  border-color: #10b981;
}

.step-item.disabled {
  cursor: not-allowed;
  opacity: 0.5;
}

.step-title {
  font-size: 14px;
  font-weight: 500;
  color: #6b7280;
  transition: color 0.3s ease;

}

.step-title-item.active .step-title {
  color: #3b82f6;
  font-weight: 600;
}

.step-title-item.completed .step-title {
  color: #10b981;
}

.stepper-content {
  border: 2px solid #e5e5e7;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  margin-top: 20px;
  margin-bottom: 20px;
  padding: 20px;
}

.stepper-navigation {
  display: flex;
  justify-content:end;
  align-items: center;
  padding: 20px 20px;
}

.step-counter {
  font-size: 14px;
  color: #6b7280;
  font-weight: 500;
}

/* Responsive */
@media (max-width: 768px) {
 
  .step-line {
      flex: 1;
      margin-top: 15px;
      margin-left: 5px;
      margin-right: 5px;
  }

  .step-number {
    width: 32px;
    height: 32px;
    font-size: 14px;
  }
  
  .step-title {
    font-size: 12px;
  }
   
  .step-counter {
    order: -1;
    width: 100%;
    text-align: center;
  }
}
</style>
