<script setup lang="ts">
    import type { Produit } from "../scripts/produit"
    import { ref, defineEmits } from 'vue'
    import ConfirmDelete from "./ConfirmDelete.vue"

    const showDetails = ref(false)
    const showModal = ref(false)
    const produitToDelete = ref<Produit | null>(null)

    defineProps<{
        produit: Produit
    }>()

    const emit = defineEmits<{
        (event: 'delete', id: number): void
        (event: 'modifier', id: number): void
        (event: 'dupliquer', id:number): void
    }>()

    const toggleDetails = () => {
        showDetails.value = !showDetails.value
    }

    const confirmDelete = (produit: Produit) => {
        produitToDelete.value = produit
        showModal.value = true
    }

    const deleteProduct = (id: number) => {
        emit('delete', id)
        showModal.value = false
    }

    const cancelDelete = () => {
        showModal.value = false
        produitToDelete.value = null
    }
</script>

<template>
    <div class="mb-4 itme-center">
        <div class="card shadow-sm">
            <div class="card-body">
                <h5 class="card-title text-primary">{{ produit.name }}</h5>
                <p class="card-text">
                    <strong>Prix :</strong> {{ produit.prix }}$  
                </p>
                <p v-if="produit.quantiter <= 5" class="card-text text-danger">
                    <strong>Quantité :</strong> {{ produit.quantiter }}
                </p>
                <p v-else-if="produit.quantiter <= 15" class="card-text text-warning">
                    <strong>Quantité :</strong> {{ produit.quantiter }}
                </p>
                <p v-else class="card-text text-success">
                    <strong>Quantité :</strong> {{ produit.quantiter }}
                </p>
                <span v-if="produit.quantiter === 0" class="badge bg-danger">Rupture de stock</span>
                <div v-if="showDetails" class="mt-3">
                    <p><strong>Description complète :</strong> {{ produit.description }}</p>
                </div>
            </div>
            <div class="row-md-4">
                <button class="col-md-2 btn btn-primary" @click="emit('dupliquer', produit.id)">+</button>
                <button class="col-md-2 btn btn-primary" @click="toggleDetails">Détail</button>
                <button class="col-md-2 btn btn-warning" @click="emit('modifier', produit.id)">Modifier</button>
                <button class="col-md-3 btn btn-danger" @click="confirmDelete(produit)">Supprimer</button>
            </div>
        </div>
    </div>

    <ConfirmDelete v-if="showModal" :produit="produitToDelete" @cancel="cancelDelete" @confirm="deleteProduct"/>
</template>

<style scoped>

</style>