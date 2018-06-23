<template>
    <div id="app">
        <form id="searchInput">
            Rechercher <input name="search" v-model="searchValue">
        </form>
        
        <Table id="table"
            :data="data"
            :searchValue="searchValue"
            :columns="columns"></Table>
    </div>
</template>

<script>
    import Table from './components/Table'
    import jsonFile from './assets/MOCK_DATA.json';
    
    export default {
        name: 'App',
        components: {
            Table
        },
        data() {
            const fileData = jsonFile.map(obj => {
                const result = {
                    ...obj,
                    first_name: obj.customer.first_name,
                    last_name: obj.customer.last_name,
                    email: obj.customer.email,
                    phone: obj.customer.phone,
                };
                
                delete result.customer;
                return result;
            });
            
            return {
                searchValue: '',
                columns: [],
                data: fileData
            }
        },
        methods: {
            initializeColumns() {
                const firstObj = this.data[0];
                for (let key in firstObj) {
                    if (firstObj.hasOwnProperty(key)) {
                        if (key !== 'id' &&
                            key !== 'attachments' &&
                            key !== 'gender' &&
                            key !== 'country_code' &&
                            key !== 'birthday') {
                            this.columns.push(key);
                        }
                    }
                }
            }
        },
        created() {
            this.initializeColumns();
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        margin-top: 7vh;
    }
    
    #searchInput {
        text-align: center;
        margin-bottom: 7vh;
    }
    
    #table {
    
    }
</style>
