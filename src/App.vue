<template>
    <header class="header">
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
            <div class="container-fluid">
                <div class="collapse navbar-collapse ms-5" id="navbarNavAltMarkup">
                    <div class="navbar-nav">
                        <a class="nav-link active" aria-current="page" style="font-weight: bold; color: MediumVioletRed;">EXPLORADOR DE DRINKS</a>
                    </div>
                </div>
            </div>
        </nav>
    </header>
    <div class="container texte-center">
        <div class="row">
            <div class="row col-12">
                <div class="container texte-center">
                    <div class="row col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12">
                        <div class="row col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" style="margin-top: 20px;">
                            <!-- FILTER BY NAME -->
                            <div class='col-12 col-sm-4 col-md-4 col-lg-4 col-xl-4' style="margin-bottom: 20px;">
                                <span>Search Drink by Name:</span>
                                <input 
                                    type="text" 
                                    class="form-control"
                                    placeholder="Digite o nome do drink" 
                                    v-model="input.DRINK_NAME"
                                    @input="searchDrinkByName"
                                >
                            </div>
                            <div class='col-12 col-sm-2 col-md-2 col-lg-2 col-xl-2 text-center' style="margin-bottom: 20px; margin-top: 25px;">
                                <button @click="searchDrinkByName()" style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" type="button" class="btn btn-primary">
                                    <i class="fas fa-search" style="margin-right: 5px;"></i> Search
                                </button>
                            </div>
                            <!-- FILTER BY CATEGORY -->
                            <div class='col-12 col-sm-4 col-md-4 col-lg-4 col-xl-4' style="margin-bottom: 20px;">
                                <span>Filter by Category:</span>
                                <select class="form-select" aria-label="Category" v-model='input.CATEGORY' @change="getDrinksByCategory()">
                                    <option v-for='x in dataSourceCategory' :key="x.idDrink" :value="x"> {{x.strCategory}} </option>
                                </select>
                            </div>
                            <div class='col-12 col-sm-2 col-md-2 col-lg-2 col-xl-2 text-center' style="margin-bottom: 20px; margin-top: 25px;">
                                <button @click="limpar_campos()" style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" type="button" class="btn btn-primary">Clear fields</button>
                            </div>
                            <!-- FILTER BY LETTER -->
                            <div class='col-12 col-sm-10 col-md-10 col-lg-10 col-xl-10' style="margin-bottom: 20px;">
                                <span>Filter by Initial Letter:</span><br>
                                <button
                                    style="color: MediumVioletRed; border-color: MediumVioletRed !important;"
                                    v-for="letter in alphabet" 
                                    :key="letter"
                                    class="btn btn-outline-primary m-1 btnLetter"
                                    @click="getDrinksByLetter(letter)">
                                    {{ letter }}
                                </button>
                            </div>
                            <div class='col-12 col-sm-2 col-md-2 col-lg-2 col-xl-2 text-center' style="margin-bottom: 20px; margin-top: 25px;">
                                <button style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#Modal_Favorite">Favorite Drinks</button>
                            </div>
                        </div>
                        <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" style="margin-top: 20px;">
                            <h4 class="text-center" style="font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;">Drinks</h4>
                        </div>
                        <div v-for="x in api" :key="x.idDrink" style="margin-top: 5%; margin-bottom: 5%;" class="col-12 col-sm-6 col-md-6 col-lg-6 col-xl-6 d-flex justify-content-center">
                            <div class="col-sm-12 col-md-8">
                                <div id="card" style="background-color: white; height: 550px;" class="card">
                                    <div  class="card-body row">
                                        <div class="text-end" style="position: relative;">
                                            <i 
                                                :class="isFavorito(x) ? 'fas fa-star text-warning' : 'far fa-star'" 
                                                style="font-size: 24px; cursor: pointer;" 
                                                @click="toggleFavorito(x)"
                                            ></i>
                                        </div>
                                        <div style="font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;" class="text-center"><b>{{x.strDrink}}</b></div>
                                        <div style="margin-top: 5%; margin-bottom: 5%;" class="text-center">
                                            <img :src="x.strDrinkThumb" width="150">
                                        </div>
                                        <div style="margin-top: -20px; font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                            <p class="mt-4" v-if="input.CATEGORY == null"><b>Category:</b> {{x.strCategory}} </p>
                                            <p class="mt-4" v-else><b>Category:</b> {{input.CATEGORY.strCategory}} </p>
                                        </div>
                                        <div class="col-12 text-center" style="margin-bottom: 10px; margin-top: 20px;">
                                            <button @click="more(x)" style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#Modal_More">More</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- Modal More -->
                <div class="modal fade" id="Modal_More" tabindex="-1" aria-labelledby="Modal_MoreLabel" aria-hidden="true">
                    <div class="modal-dialog modal-xl">
                        <div class="modal-content">
                            <div style="background-color:#292929;" class="modal-header">
                                <h1 style="color:white; text-align: center !important;" class="modal-title fs-5" id="Modal_MoreLabel">Product information {{ modal.drink }}</h1>
                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div style="background-color: #141313" class="modal-body">
                                <div class="row">
                                    <div class="col-md-5"></div>
                                    <div></div>
                                    <div style="margin-top: 2%;  color:white; width: 500px;" class="col-12 mt-12">
                                        <div class="col-sm-12 col-md-12">
                                            <div style="margin-top: 5%; margin-bottom: 5%;" class="text-center">
                                                <img :src="modal.image" width="150">
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Drink number: </b>{{ modal.id }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Category: </b>{{ modal.category }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Alcoholic: </b>{{ modal.alcoholic }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Instructions: </b>{{ modal.instructions }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;" v-if="modal.ingredient1">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Ingredient 1: </b>{{ modal.ingredient1 }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;" v-if="modal.ingredient2">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Ingredient 2: </b>{{ modal.ingredient2 }}</p>
                                            </div>
                                            <div style="margin-top: -7px; font-style: italic; font-family: 'Courier New', Courier, monospace;" v-if="modal.ingredient3">
                                                <p class="mt-12"> <b style="color: MediumVioletRed; font-weight: bold;">Ingredient 3: </b>{{ modal.ingredient3 }}</p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div style="background-color:#292929;" class="modal-footer">
                                <button type="button" class="btn btn-secondary" style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" data-bs-dismiss="modal">Close</button>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- Modal favorite -->
                <div class="modal fade" id="Modal_Favorite" tabindex="-1" aria-labelledby="Modal_FavoriteLabel" aria-hidden="true">
                    <div class="modal-dialog modal-xl">
                        <div class="modal-content">
                            <div style="background-color:#292929;" class="modal-header">
                                <h1 style="color:white; text-align: center !important;" class="modal-title fs-5" id="Modal_MoreLabel">Favorite Drinks</h1>
                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div style="background-color: #141313" class="modal-body">
                                <div class="row">
                                    <div class="col-12 col-sm-12 col-md-12 col-lg-12 col-xl-12" style="margin-top: 20px;">
                                        <h4 class="text-center" style="font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;">Drinks</h4>
                                    </div>
                                    <div v-for="x in favoritos" :key="x.idDrink" style="margin-top: 5%; margin-bottom: 5%;" class="col-12 col-sm-6 col-md-6 col-lg-6 col-xl-6 d-flex justify-content-center">
                                        <div class="col-sm-12 col-md-8">
                                            <div id="card" style="background-color: white; height: 550px;" class="card">
                                                <div  class="card-body row">
                                                    <div style="font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;" class="text-center"><b>{{x.strDrink}}</b></div>
                                                    <div style="margin-top: 5%; margin-bottom: 5%;" class="text-center">
                                                        <img :src="x.strDrinkThumb" width="150">
                                                    </div>
                                                    <div style="margin-top: -20px; font-weight: bold; font-style: italic; font-family: 'Courier New', Courier, monospace;">
                                                        <p class="mt-4" v-if="input.CATEGORY == null"><b>Category:</b> {{x.strCategory}} </p>
                                                        <p class="mt-4" v-else><b>Category:</b> {{input.CATEGORY.strCategory}} </p>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div style="background-color:#292929;" class="modal-footer">
                                <button type="button" class="btn btn-secondary" style="background-color: MediumVioletRed; border-color: MediumVioletRed !important;" data-bs-dismiss="modal">Close</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  export default{
    data: function () {
        return {
            api: [],
            modal: {
                id: null,
                drink: null,
                category: null,
                alcoholic: null,
                image: null,
                instructions: null,
                ingredient1: null,
                ingredient2: null,
                ingredient3: null
            },
            favorite: {
                id: null,
                drink: null,
                category: null,
                alcoholic: null,
                image: null,
                instructions: null,
                ingredient1: null,
                ingredient2: null,
                ingredient3: null
            },
            dataSourceOrdem: [],
            dataSourceCategory: [],
            input: {
                ORDEM: null,
                CATEGORY: null,
                DRINK_NAME: null
            },
            alphabet: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split(''),
            selectedLetter: '',
            drinksList: [],
            favoritos: []
        }
    },
    computed: {
    },
    methods: {
        async getRandomCocktails() {
            try {
                const requests = [];
                for (let i = 0; i < 30; i++) {
                requests.push(fetch('https://www.thecocktaildb.com/api/json/v1/1/random.php').then(res => res.json()));
                }
                const results = await Promise.all(requests);
                const cocktails = results.map(result => result.drinks[0]);
                this.api = cocktails;
            } catch (error) {
                console.error('Erro ao buscar drinks:', error);
            }
        },
        async getCategories() {
            try {
                const response = await fetch('https://www.thecocktaildb.com/api/json/v1/1/list.php?c=list');
                const data = await response.json();
                this.dataSourceCategory = data.drinks;
            } catch (error) {
                console.error('Erro ao pegar as categorias:', error);
            }
        },
        async getDrinksByCategory() {
            try {
                const category = this.input.CATEGORY.strCategory;
                const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/filter.php?c=${category}`);
                const data = await response.json();
                this.api = data.drinks;
            } catch (error) {
                console.error('Erro ao buscar drinks por categoria:', error);
            }
        },
        async searchDrinkByName() {
            const name = this.input.DRINK_NAME.trim();
            if (name) {
                this.drinksList = await this.getDrinksByName(name);
            } else {
                this.getRandomCocktails();
                return;
            }
        },
        async getDrinksByName(name) {
            try {
                const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/search.php?s=${name}`);
                const data = await response.json();
                this.api = data.drinks ? data.drinks : [];
            } catch (error) {
                console.error('Erro ao buscar drinks por nome:', error);
            return [];
            }
        },
        async getDrinksByLetter(letter) {
            try {
                const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/search.php?f=${letter}`);
                const data = await response.json();
                this.api = data.drinks;

                if (data.drinks) {
                this.drinksList = data.drinks;
                } else {
                this.drinksList = [];
                console.log('Nenhum drink encontrado com essa letra');
                }
            } catch (error) {
                console.error('Erro ao buscar drinks por letra:', error);
            }
        },
        toggleFavorito(drink) {
            const index = this.favoritos.findIndex(fav => fav.idDrink === drink.idDrink);
            if (index === -1) {
            this.favoritos.push(drink);
            } else {
            this.favoritos.splice(index, 1);
            }
        },
        isFavorito(drink) {
            return this.favoritos.some(fav => fav.idDrink === drink.idDrink);
        },
        more(x){
            this.modal.id = x.idDrink;
            this.modal.drink = x.strDrink;
            this.modal.category = x.strCategory;
            this.modal.alcoholic = x.strAlcoholic;
            this.modal.image = x.strDrinkThumb;
            this.modal.instructions = x.strInstructions;
            this.modal.ingredient1 = x.strIngredient1;
            this.modal.ingredient2 = x.strIngredient2;
            this.modal.ingredient3 = x.strIngredient3;
        },
        limpar_campos(){
            this.input.CATEGORY = null;
            this.input.ORDEM = null;
            this.input.DRINK_NAME = null;
            this.favoritos = [];
            this.getRandomCocktails();
        }
    },
    mounted: function () {
        this.getRandomCocktails();
        this.getCategories();
    }
}
</script>

<style scoped>
.btnLetter:hover{
  background-color: white;
}
</style>