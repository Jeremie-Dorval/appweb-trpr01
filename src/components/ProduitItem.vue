<script setup lang="ts">
    import type { Produit } from "../scripts/produit"
    import { defineEmits } from 'vue'

    defineProps<{
        produit: Produit
    }>()

    const emit = defineEmits<{
        (event: 'delete', id: number): void
        (event: 'modifier', id: number): void
        (event: 'dupliquer', id:number): void
    }>()
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
                <p class="card-text text-muted">{{ produit.description }}</p>
            </div>
            <div class="row-md-4">
                <button class="col-md-4 btn btn-primary" @click="emit('dupliquer', produit.id)">+</button>
                <button class="col-md-4 btn btn-warning" @click="emit('modifier', produit.id)">Modifier</button>
                <button class="col-md-4 btn btn-danger" @click="emit('delete', produit.id)">Supprimer</button>
            </div>
        </div>
    </div>
</template>

<style scoped>

</style>