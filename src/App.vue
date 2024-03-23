<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <!-- :income="income" = string   /   :income="+income" = numbers -->
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <!-- Using "transactions" array of objects in the TransactionList component where we defined "transactions" as a prop-->
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />

    <!-- Listen for custom Event -->
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

// Import Toast Package
import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from "vue";

// Initialize Toast
const toast = useToast();

// If we want things to be reactive - bring in the ref() function   ->   Include array in ref()
const transactions = ref([]);

// Local Storage   ->   Fetch from Local Storage -> onMounted
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem
  ('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// Get Total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    // acc = accumulator
    return acc + transaction.amount;
    // Pass 0 = that's where we want the accumulator to start
  }, 0);
});

// Get income
const income = computed(() =>{
  return transactions.value
  // Filter for income only = amounts > 0
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
  // 2 decimals
  .toFixed(2);
});

// Get expenses
const expenses = computed(() =>{
  return transactions.value
  // Filter for expenses only = amounts < 0
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
  // 2 decimals
  .toFixed(2);
});

// Add Transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })

  saveTransactionsToLocalStorage();

  toast.success('Transaction added');
}

// Generate unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
}

// Delete Transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => 
    transaction.id !== id);

    saveTransactionsToLocalStorage();

    toast.success('Transaction deleted');
};


// Local Storage -> Save to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>