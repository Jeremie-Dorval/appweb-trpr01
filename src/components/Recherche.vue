<script setup lang="ts">
    import { ref, defineProps, defineEmits, watch } from 'vue'
    import type { Produit } from '../scripts/produit'

    const recherche = ref('')
    const props = defineProps<{ produits: Produit[] }>()
    const emit = defineEmits<{ (event: 'recherche', resultat: Produit[]): void }>()

    watch(recherche, (nouvelleValeur) => {
        const resultat = props.produits.filter(produit =>
            produit.name.toLowerCase().includes(nouvelleValeur.toLowerCase())
        )
        emit('recherche', resultat)
    })
</script>

<template>
    <div class="input-group">
        <input v-model="recherche" type="text" id="recherche" class="form-control" placeholder="Rechercher un produit par son nom">
    </div>
</template>

<style scoped>

</style>