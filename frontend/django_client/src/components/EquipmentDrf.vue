<template>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <div class="equipment_container">
        <div class="add_equipment">
            <h2>Добавить оборудование</h2>
            <form v-on:submit.prevent="submitForm" class="col-4 mx-auto">
                <div class="form-group">
                    <label for="type">Тип оборудования</label>
                    <!--input type="text" class="form-control" id="type" v-model="fk_type"-->
                    <select v-model="fk_type" class="form-control" v-on:change="onChangeSelect($event)">
                        <option v-for="(item, indx) in type.results" :key="indx" :value="item.id">{{ item.type_equipment }} (id = {{ item.id }})</option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="serial_number">Серийный номер</label>
                    <input type="text" class="form-control" id="serial_number" v-model="serial_number">
                </div>
                <div class="form-group">
                    <label for="text">Примечание</label>
                    <textarea class="form-control" id="text" v-model="text"></textarea>
                </div>
                <div class="form-group">
                    <button class="btn btn-outline-success w-100 my-3" type="submit">Добавить</button>
                </div>
            </form>
        </div>
        <div class="equipment_content">
            <h1 class="mt-4">Список оборудования</h1>
            <ul class="equipment_list">
                <li v-for="item in data.results" :key="item.id">
                    <h3>Тип оборудования: {{ item.fk_type }}</h3>
                    <!--div class="form-group">
                        <label for="fk_type">Тип оборудования</label>
                        <input type="text" class="form-control" id="fk_type" :value="item.fk_type" @input="item.fk_type = $event.target.value">
                    </div-->
                    <!--p>Серийный номер: {{ item.serial_number }}</p-->
                    <div class="form-group col-4 mx-auto">
                        <label for="serial_number">Серийный номер</label>
                        <input type="text" class="form-control" id="serial_number" :value="item.serial_number" @input="item.serial_number = $event.target.value">
                    </div>
                    <!--p>Примечание: {{ item.text }}</p-->
                    <div class="form-group col-4 mx-auto">
                        <label for="text">Примечание</label>
                        <textarea class="form-control" id="text" :value="item.text" @input="item.text = $event.target.value" ></textarea>
                    </div>
                    <button class="btn btn-success mx-2 my-3" @click="toggleEquipment(item)">Редактировать</button>
                    <button class="btn btn-danger mx-2 my-3" @click="deleteEquipment(item)">Удалить</button>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';
    //import { ref } from 'vue';
    export default {
        data() {
            return {
                data: [],
                type: [],
                fk_type: '',
                serial_number: '',
                text: '',
                reg: ''
            }
        },
        methods: {
            onChangeSelect: function(e) {
                var fk_id = e.target.value;
                console.log('id = ', fk_id);
                if (fk_id == '1') {
                    var regex = new RegExp(/[0-9A-Z]{2}[A-Z]{5}[A-Z0-9]{1}[A-Z]{2}/);
                }else if (fk_id == '2') {
                    regex = new RegExp(/[0-9]{1}[A-Z0-9]{2}[A-Z]{2}[0-9A-Z]{1}[-,_,@]{1}[0-9A-Z]{1}[a-z]{2}/);
                }else if (fk_id == '3') {
                    regex = new RegExp(/[0-9]{1}[A-Z]{4}[0-9A-Z]{1}[-,_,@]{1}[0-9A-Z]{3}/);
                }
                console.log('regex = ',  regex);
                this.reg = regex;
            },
            async getData() {
                try {
                    const response = await axios.get('http://127.0.0.1:9000/equipment/');
                    this.data = response.data;
                } catch (error) {
                    // log the error
                    console.log(error);
                }
            },
            async getType() {
                try {
                    const response = await axios.get('http://127.0.0.1:9000/equipment-type/');
                    this.type = response.data;
                } catch (error) {
                    // log the error
                    console.log(error);
                }
            },
            async submitForm(){
                var ser = document.getElementById('serial_number').value;
                if (this.reg.test(ser) == true) {
                    try {
                        // Send a POST request to the API
                        await axios.post('http://localhost:9000/equipment/', {
                            fk_type: this.fk_type,
                            serial_number: this.serial_number,
                            text: this.text,
                            completed: false
                        });
                        console.log('this.fk_type = ', this.fk_type);
                        //this.data.push(response.data);
                        this.getData();
                        this.fk_type = '';
                        this.serial_number = '';
                        this.text = '';
                    } catch (error) {
                        // Log the error
                        console.log(error);
                    }
                }else {
                    document.getElementById('serial_number').after('Ошибка введения серийного номера для данного типа оборудования');
                    return false;
                }

            },
            async deleteEquipment(item){
                let confirmation = confirm("Вы действительно хотите удалить оборудование?");
                if(confirmation){
                    try{
                        // Send a request to delete the item
                        await axios.delete(`http://localhost:9000/equipment/${item.id}`);
                        // Refresh the equipment list
                        this.getData();
                    }catch(error){
                        console.log(error)
                    }
                }
            },
            async toggleEquipment(item){
                try{

                    // Send a request to API to update the equipment
                    const response = await axios.put(`http://localhost:9000/equipment/${item.id}/`, {
                        //completed: false,
                        fk_type: item.fk_type,
                        serial_number: item.serial_number,
                        text: item.text
                    });
                    console.log('response.data = ', response.data);
                }catch(error){
                    // Log any error
                    console.log(error);
                }
            }
        },
        created() {
            this.getData();
            this.getType();
        }
    }
</script>