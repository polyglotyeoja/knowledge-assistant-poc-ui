<template>
    <div class="query-form">
        <h2>Ask a Developmentally-Informed Question</h2>

        <input v-model="query"
        type="text"
        placeholder="Enter your question"
        class = "query-input"
        />
        <!-- <label>Age (e.g., )</label>
        <input v-model="age" class="age-input"
        type="text"
        placeholder="Enter a child's age"
         /> -->
        <button @click="submitQuery" :disabled="loading">Submit</button>

        <div v-if="loading">Loading...</div>
        <div v-if="results">
            <h3>Results:</h3>
            <!-- <div v-for="(doc, i) in results" :key="i" class="result"> -->
                <div>
                <p><strong>Answer: </strong> {{ results }}</p>
            </div>
        </div>
    </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'
import axios, { AxiosError } from 'axios'

interface Document {
    title: string
    pmid: string
    abstract: string
}

interface QueryRequest { 
    query: string
    // age: number
}

interface QueryResponse {
    embedding: []
}

const query = ref<string>('')
const age = ref<number | string>('')
const results = ref()
const loading = ref<boolean>(false)

async function submitQuery(): Promise<void> {
    loading.value = true
    results.value = []

    try{
        const requestData: QueryRequest = {
            query: query.value
        }
        const headers = {
            'Content-Type': 'application/json'
        }
        const response = await axios.post<QueryResponse>('http://localhost:8000/ask_question', requestData, { headers }).then(result => results.value = result.data["embedding"]);
    } catch(err){
        console.error('Error:', err)

        if (err instanceof AxiosError) {
            alert(`Request failed: ${ err.message }`)
        }
        else { alert('Something went wrong')}
    }
        finally {
            loading.value = false
        }
    }
</script>

<style scoped>
.query-form {
    padding: 2rem;
    max-width: 600px;
    margin: 0 auto;
}
.query-input, .age-input {
    width: 100%;
    margin-bottom: 1rem;
}
.result {
    border-bottom: 1px solid #ddd;
    padding: 1rem 0;
}
</style>