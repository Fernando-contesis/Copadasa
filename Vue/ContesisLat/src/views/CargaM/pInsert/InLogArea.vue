<template>
    <div class="modal-backdrop"></div>
    <div class="ReportPage">
        <h4>Agregar Areas de Almacen</h4>
        <hr>
        <form class="row g-3 needs-validation" novalidate>
            <div class="col-md-2">
                <label for="validationCustom01" class="form-label">Almacen</label>
                <input type="text" v-model="almacen" class="form-control" id="validationCustom01" required>
                <div class="valid-feedback">
                    Looks good!
                </div>
            </div>
            <div class="col-md-2">
                <label for="validationCustom01" class="form-label">Area</label>
                <input type="form-select" v-model="area" id="validationCustom01"  required>
                <div class="valid-feedback">
                    Looks good!
                </div>
            </div> 
            <div class="col-md-4">
                <label for="validationCustom02" class="form-label">Descripcion</label>
                <input type="text" v-model="descripcion" class="form-control" id="validationCustom02"  required>
                <div class="valid-feedback">
                    Looks good!
                </div>
            </div>
            <div class="col-md-2">
                <label for="validationCustom04" class="form-label">Estado</label>
                <select class="form-select" v-model="status" id="validationCustom04" required >
                    <option>A</option>
                    <option>I</option>
                </select>
                <div class="invalid-feedback">
                    Please select a valid state.
                </div>
            </div>
            <div class="col-12">
                <button class="btn btn-secondary me-2" type="button" style="background-color: #24292F;" @click="handleClick">Cancelar</button>
                <button class="btn btn-primary" type="button" style="background-color: #001982;" @click="handleSubmit">Enviar</button>
            </div>
        </form>

    </div> 
</template>

<script lang="ts" setup>
import { defineProps,defineEmits,Ref,ref } from 'vue'
import { useDateTimeStore } from '@/store/dateTimeStore';
import { userGlobalStore } from '@/store/userGlobal';
import { UrlGlobal } from '@/store/dominioGlobal';

const dUrl = UrlGlobal()
const dateTimeStore = useDateTimeStore();
const userStore = userGlobalStore();


//variables reactivas para los campos del formulario
const almacen = ref<string>('')
const area = ref<string>('')
const descripcion = ref<string>('')
//const anaquel = ref<string>('')
//onst cara = ref<string>('')
const status = ref<string>('')

//props y emits----------------------------------------------------------------
const props = defineProps({
    btnIn: Boolean,
    var1: Number,
})
const emits = defineEmits(['insertProps'])
        const handleClick = () =>{
            emits("insertProps",!props.btnIn)
        }
//----------------------------------------------------------------

// envio del insert a la tabla en la base de datos a traves de la api en django
const handleSubmit = async () =>{
    dateTimeStore.refreshDateTime();
    console.log(dateTimeStore.formattedDate)
    const data = {
        model:"logarea",
        data:{
            almacen:almacen.value,
            area:area.value,
            descripcion:descripcion.value,
            creado_por:userStore.globalUser,
            fecha_creado:dateTimeStore.formattedDate,
            hora_creado:dateTimeStore.formattedTime,
            modificado_por:userStore.globalUser,
            status:status.value,
            fecha_status:dateTimeStore.formattedDate,
            hora_status:dateTimeStore.formattedTime
            
        }
    }
    try {
        const response = await fetch(dUrl.urlGlobal +'/api2/insert/',{
            method: 'POST',
            headers:{
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data),
        })
        if(response.ok){
            const responseData = await response.json()
            console.log("Insercion exitosa:",responseData)
            handleClick()
        }else{
            console.error("Error al insertar en la base de datos:",response.statusText)
        }
    }catch (error){
        console.error("error de red:",error)
    }
}
//----------------------------------------------------------------------------------------------
</script>

<style scoped>
svg {
    fill: black;
}

.modal-backdrop {
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    /* Fondo oscuro semitransparente */
    z-index: 1050;
    /* Coloca el fondo oscuro por encima de otros elementos */
    @media screen and (max-width:600px){
        width: 500px;     
    }
}

.ReportPage {
    min-height: 62%;
    width: 75%;
    background: whitesmoke;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 5px;
    display: flex;
    justify-content: left;
    align-items: left;
    display: flex;
    flex-direction: column;
    gap: 15px;
    font-family: Trebuchet MS;
    color: black;
    padding: 15px;
    z-index: 1060; 
    overflow: hidden;
    @media screen and (max-width: 600px){
        overflow: scroll;
        height: 55%;
        width: 95%; 
    }
}

.ReportPage hr {
    border: none;
    border-top: 1px solid #ccc;
    margin: 1px 0;
    padding-left: 100%;
}
</style>