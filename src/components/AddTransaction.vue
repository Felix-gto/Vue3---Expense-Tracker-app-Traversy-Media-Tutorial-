<template>
  <h3>Add new transaction</h3>

  <form id="form" @submit.prevent="onSubmit">

    <!-- Add Text -->
    <div class="form-control">
      <label for="text">Text</label>

      <input type="text" 
        id="text" 
        v-model="text" 
        placeholder="Enter text..." 
      />
    </div>

     <!-- Add Number (amount) -->
    <div class="form-control">
      <label for="amount"
        >Amount <br />
        (negative - expense, positive - income)
      </label>
      
      <!-- Input type = "text" not number so we can add decimals... We will parse it later with amount: parseFloat(amount.value)-->
      <input
        type="text"
        id="amount"
        v-model="amount"
        placeholder="Enter amount..."
      />
    </div>

    <button class="btn">Add transaction</button>
  </form>
</template>

<script setup>
  import { ref } from "vue";
  // Use Vue-Toastification
  import { useToast } from 'vue-toastification';

  const text = ref('');
  const amount = ref('');

  // Create custom event when we Add new transaction
  const emit = defineEmits(['transactionSubmitted']);

  // Initialize Vue Toastification   ->   useToast
  const toast = useToast();

  const onSubmit = () => {
    if (!text.value || !amount.value) {
      toast.error('Both fields must be filled');
      return;
    }

    const transactionData = {
      text: text.value,
      amount: parseFloat(amount.value)
    }
    
    // Emit Custom event - Listen for it in App.vue
    emit('transactionSubmitted', transactionData);

    text.value = '';
    amount.value = '';

  };
</script>
