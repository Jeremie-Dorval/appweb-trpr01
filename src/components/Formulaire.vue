<script setup lang="ts">
    import { ref, watch } from "vue"
    import type { Produit } from "../scripts/produit.ts"
    import ProduitItem from "./ProduitItem.vue"
    import Recherche from "./Recherche.vue"

    const produits = ref<Produit[]>([
        {
            id: 1, 
            name:'Sol Ring', 
            prix: 4.49, 
            quantiter: 10, 
            description: 'Carte qui coute 1 mana générique a jouer, type artefact et si cette carte est engager ajouter deux mana générique a votre mana.'
        },
        {
            id: 2,
            name: 'Command Tower',
            prix: 0.49,
            quantiter: 15,
            description: "Carte qui ne coute rien a jouer, type land et si cette carte est engager ajouter un mana de n'importe qu'elle couleur de votre commander."
        }
    ])

    const selectedId = ref<number | null>(null)
    const editName = ref<string>('')
    const editPrix = ref<number>(0)
    const editQuantiter = ref<number>(0)
    const editDescription = ref<string>('')

    let nextId = 3
    const newName = ref<string>('')
    const newPrix = ref<number>(0)
    const newQuantiter = ref<number>(0)
    const newDescription = ref<string>('')

    const isValidated = ref(false)
    //const recherche = ref<string>('')
    const resultat = ref<Produit[]>([])

    const addProduit = () => {
        isValidated.value = true

        if (!newName.value || newPrix.value < 1 || newQuantiter.value < 1 || !newDescription.value) {
            return
        }

        produits.value.push({
            id:nextId++,
            name:newName.value,
            prix:newPrix.value,
            quantiter:newQuantiter.value,
            description:newDescription.value
        })

        alert("Produit ajouté avec succès !")

        newName.value = ''
        newPrix.value = 0
        newQuantiter.value = 0
        newDescription.value = ''
        isValidated.value = false
    }

    const deleteProduit = (id: number) => {
        produits.value = produits.value.filter((produit: { id: number }) => produit.id !== id)
    }

    const startEditProduit = (id: number) => {
        const produit = produits.value.find((produit) => produit.id == id)
        if(produit) {
            selectedId.value = id
            editName.value = produit.name
            editPrix.value = produit.prix
            editQuantiter.value = produit.quantiter
            editDescription.value = produit.description
        }
    }

    const editProduit = () => {
        if(!editName.value.trim()) return;

        const produit = produits.value.find((produit) => produit.id === selectedId.value)

        if(produit) {
            produit.name = editName.value
            produit.prix = editPrix.value
            produit.quantiter = editQuantiter.value
            produit.description = editDescription.value
            editName.value = ""
            editPrix.value = 0
            editQuantiter.value = 0
            editDescription.value = ""
        }
    }

    const dupliquer = (id: number) => {
        const produit = produits.value.find((produit) => produit.id === id)

        if(produit) {
            newName.value = produit.name,
            newPrix.value = produit.prix,
            newQuantiter.value = produit.quantiter,
            newDescription.value = produit.description
        }
    }

    watch(produits, (nouveauProduit) => {
        nouveauProduit.forEach(produit => {
            if(produit.quantiter === 0) {
                alert("Le produit" + produit.name + "est en ruptire de stock!")
            }
        })
    }, {deep: true})

    const exporterCSV = () => {
        if (produits.value.length === 0) {
            alert("Aucun produit à exporter !")
            return
        }

        let csvContent = "data:text/csv;charset=utf-8,"
        csvContent += "Nom,Prix,Quantité\n"

        produits.value.forEach(produit => {
            csvContent += `${produit.name},${produit.prix},${produit.quantiter}\n`
        })

        const encodedUri = encodeURI(csvContent)
        const link = document.createElement("a")
        link.setAttribute("href", encodedUri)
        link.setAttribute("download", "produits.csv")
        document.body.appendChild(link)

        link.click()
        document.body.removeChild(link)
    }
</script>

<template>
    <div class="container mt-4">
        <div class="row">
            <Recherche :produits="produits" @recherche="resultat = $event"/>
            <div class="col">
                <h3 class="mb-4 text-center">Ajouter une nouvelle carte</h3>
                <form class="row g-3 needs-validation" :class="{'was-validated': isValidated}" novalidate>
                    <div class="col-md-6">
                        <label for="nom" class="form-label">Nom du produit :</label>
                            <input v-model="newName" type="text" id="nom" class="form-control" placeholder="Nom de la carte" required :class="{'is-invalid': isValidated && !newName}">
                            <div class="invalid-feedback">Veuillez entrer un nom.</div>
                    </div>

                    <div class="col-md-6">
                        <label for="prix" class="form-label">Prix du produit :</label>
                        <input v-model="newPrix" type="text" id="prix" class="form-control" required :class="{'is-invalid': isValidated && !newPrix}">
                        <div class="invalid-feedback">Veuillez entrer un prix valide.</div>
                    </div>

                    <div class="col-md-6">
                        <label for="quantiter" class="form-label">Quantité :</label>
                        <input v-model="newQuantiter" type="number" id="quantiter" class="form-control" min="1" required :class="{'is-invalid': isValidated && newQuantiter < 1}">
                        <div class="invalid-feedback">La quantité doit être au moins 1.</div>
                    </div>

                    <div class="col-md-6">
                        <label for="description" class="form-label">Description :</label>
                        <input v-model="newDescription" type="text" id="description" class="form-control" placeholder="Description de la carte" required :class="{'is-invalid': isValidated && !newDescription}">
                        <div class="invalid-feedback">Veuillez entrer une description.</div>
                    </div>

                    <div class="col-12 text-center">
                        <button type="button" @click="addProduit" class="btn btn-success w-50">
                            Ajouter le produit
                        </button>
                    </div>
                </form>
            </div>
            <div class="col">
                <h3 class="mb-4 text-center">Modification d'un carte</h3>
                <form class="row g-3">
                    <div class="col-md-6">
                        <label for="nom" class="form-label">Nom du produit :</label>
                        <input v-model="editName" type="text" id="nom" class="form-control" placeholder="Nom de la carte">
                    </div>

                    <div class="col-md-6">
                        <label for="prix" class="form-label">Prix du produit :</label>
                        <input v-model="editPrix" type="text" id="prix" class="form-control" placeholder="Ex: 499.99">
                    </div>

                    <div class="col-md-6">
                        <label for="quantiter" class="form-label">Quantité :</label>
                        <input v-model="editQuantiter" type="number" id="quantiter" class="form-control" min="1">
                    </div>

                    <div class="col-md-6">
                        <label for="description" class="form-label">Description :</label>
                        <input v-model="editDescription" type="text" id="description" class="form-control" placeholder="Description de la carte">
                    </div>

                    <div class="col-12 text-center">
                        <button type="button" @click="editProduit" class="btn btn-success w-50">
                            Modifier le produit
                        </button>
                    </div>
                </form>
            </div>
        </div>
        <div v-if="resultat.length === 0" class="mt-4">
            <h3>Liste des produits :</h3>
            <ProduitItem v-for="produit in produits" :key="produit.id" :produit="produit" @delete="deleteProduit" @modifier="startEditProduit" @dupliquer="dupliquer"/>
        </div>
        <div v-else class="mt-4">
            <h3>Liste des produits :</h3>
            <ProduitItem v-for="produit in resultat" :key="produit.id" :produit="produit" @delete="deleteProduit" @modifier="startEditProduit" @dupliquer="dupliquer"/>
        </div>

        <button @click="exporterCSV" class="btn btn-primary">
            Exporter en CSV
        </button>
    </div>
</template>

<style scoped>

</style>