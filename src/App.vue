
<template>
<Header />
<div class="container">
<Balance :total="+total" />
<IncomeExpense :income="+income" :expenses="+expenses"/>
<TransactionList :transactions="transactions"
@transactionDeleted="handleTransactionDeleted"/>
<AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
</div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import {useToast} from 'vue-toastification'; 

import {ref, computed, onMounted} from 'vue';

const transactions = ref([
  
    // {id: 1, text: 'Flower', amount: -19.99},    
    // {id: 2, text: 'Salary', amount: 15000},  
    // {id: 3, text: 'Book', amount: -9.99},  
    // {id: 4, text: 'Camera', amount: -200},  
]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

if(savedTransactions) {
  transactions.value = savedTransactions;
}
});
const toast = useToast();

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
return acc + transaction.amount;
  }, 0)
});

const income = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction) => {
return acc + transaction.amount;
  }, 0)
  .toFixed(2);
});

const expenses = computed(() => {
  return transactions.value
  .filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction) => {
return acc + transaction.amount;
  }, 0)
  .toFixed(2);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  savedTransactionsToLocalStorage();

  toast.success('Transaction added');
}

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => 
  transaction.id !== id);

  savedTransactionsToLocalStorage();

  toast.success('Transaction was deleted');
};

const savedTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>