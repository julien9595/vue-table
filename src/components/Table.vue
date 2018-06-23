<template>
    <div>
        <div>
        
        </div>
        
        <table>
            <thead>
            <tr>
                <th v-for="key in columns"
                    v-on:click="sortBy(key)"
                    :class="{ active: sortKey === key }">
                    {{ key | format}}
                    <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'"></span>
                </th>
            </tr>
            </thead>
            
            <tbody>
            <tr v-for="entry in filteredData">
                <td v-for="key in columns">
                    {{entry[key] | formatDate}}
                </td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    import * as moment from 'moment';
    
    export default {
        name: 'Table',
        props: [
            'data',
            'columns',
            'searchValue'
        ],
        data() {
            const sortOrders = {};
            this.columns.forEach(key => sortOrders[key] = 1);
            this.sortOrders = sortOrders;
            
            return {
                sortKey: '',
                sortOrders: sortOrders,
                statusList: [],
                channelList: []
            }
        },
        created() {
            this.data.forEach(obj => {
                if (!this.statusList.includes(obj.status)) this.statusList.push(obj.status);
                if (!this.channelList.includes(obj.contact_channel)) this.channelList.push(obj.contact_channel);
            });
        },
        computed: {
            filteredData() {
                let data = this.data;
                const sortKey = this.sortKey;
                const searchValue = this.searchValue && this.searchValue.toLowerCase();
                const order = this.sortOrders[sortKey] || 1;
                
                if (searchValue) {
                    data = data.filter(row => {
                        return Object.keys(row)
                            .some(key => String(row[key]).toLowerCase().includes(searchValue));
                    });
                }
                
                if (sortKey) {
                    data = data.slice()
                        .sort((a, b) => {
                            a = a[sortKey];
                            b = b[sortKey];
                            return (a === b ? 0 : a > b ? 1 : -1) * order;
                        });
                }
                
                return data;
            }
        },
        methods: {
            sortBy(key) {
                this.sortKey = key;
                this.sortOrders[key] = this.sortOrders[key] * -1
            }
        },
        filters: {
            format(str) {
                let keyToInsert;
                switch (str) {
                    case 'contact_channel':
                        keyToInsert = 'Moyen de communication';
                        break;
                    case 'status':
                        keyToInsert = 'Status';
                        break;
                    case 'interaction_creation_date':
                        keyToInsert = 'Dernière interaction';
                        break;
                    case 'due_date':
                        keyToInsert = 'Echéance';
                        break;
                    case 'last_comment':
                        keyToInsert = 'Dernier commentaire';
                        break;
                    case 'assignedTO':
                        keyToInsert = 'Assigné à';
                        break;
                    case 'first_name':
                        keyToInsert = 'Prénom';
                        break;
                    case 'last_name':
                        keyToInsert = 'Nom de famille';
                        break;
                    case 'email':
                        keyToInsert = 'Email';
                        break;
                    case 'phone':
                        keyToInsert = 'Téléphone';
                        break;
                }
    
    
                return keyToInsert;
            },
            formatDate(value) {
                if (moment(value).isValid()) return moment(value).utc().format("DD/MM/YYYY HH:mm");
                else return value;
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    table {
        background-color: #fff;
    }
    
    th {
        background-color: #42b983;
        color: rgba(255,255,255,0.66);
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }
    
    td {
        background-color: #f9f9f9;
    }
    
    th, td {
        min-width: 120px;
        padding: 10px 20px;
    }
    
    th.active {
        color: #fff;
    }
    
    th.active .arrow {
        opacity: 1;
    }
    
    .arrow {
        display: inline-block;
        vertical-align: middle;
        width: 0;
        height: 0;
        margin-left: 5px;
        opacity: 0.66;
    }
    
    .arrow.asc {
        border-left: 4px solid transparent;
        border-right: 4px solid transparent;
        border-bottom: 4px solid #fff;
    }
    
    .arrow.dsc {
        border-left: 4px solid transparent;
        border-right: 4px solid transparent;
        border-top: 4px solid #fff;
    }
</style>
