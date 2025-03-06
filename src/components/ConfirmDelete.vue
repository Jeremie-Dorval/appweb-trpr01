<script setup lang="ts">
    import type { Produit } from "../scripts/produit"
    import { defineProps, defineEmits } from 'vue'

    const props = defineProps<{
        produit: Produit | null
    }>()

    const emit = defineEmits<{
        (event: 'confirm', id: number): void
        (event: 'cancel'): void
    }>()

    const confirmDelete = () => {
        if (props.produit) {
            emit('confirm', props.produit.id)
        }
    }

    const cancelDelete = () => {
        emit('cancel')
    }
</script>

<template>
    <div class="modal fade show" style="display: block;" tabindex="-1" aria-labelledby="deleteModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="deleteModalLabel">Confirmer la suppression</h5>
                    <button type="button" class="btn-close" @click="cancelDelete" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    Êtes-vous sûr de vouloir supprimer le produit "{{ props.produit?.name }}" ?
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="cancelDelete">Annuler</button>
                    <button type="button" class="btn btn-danger" @click="confirmDelete">Supprimer</button>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>

</style>