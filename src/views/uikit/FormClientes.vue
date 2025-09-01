<script setup>
import Button from 'primevue/button';
import Calendar from 'primevue/calendar';
import InputText from 'primevue/inputtext';
import { ref } from 'vue';

// Variables reactivas
const name = ref('');
const lastname1 = ref('');
const lastname2 = ref('');
const fechaNacimiento = ref(null);
const rfc = ref('');

// Función para generar homoclave aleatoria
const generarHomoclave = () => {
    const caracteres = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    let homoclave = '';

    for (let i = 0; i < 3; i++) {
        homoclave += caracteres.charAt(Math.floor(Math.random() * caracteres.length));
    }

    return homoclave;
};

// Función para generar RFC
const generarRFC = () => {
    // Validar campos
    if (!name.value || !lastname1.value || !lastname2.value || !fechaNacimiento.value) {
        alert('Por favor, complete todos los campos');
        return;
    }

    try {
        // Obtener partes de la fecha
        const fecha = new Date(fechaNacimiento.value);
        const año = fecha.getFullYear().toString().slice(2);
        const mes = ('0' + (fecha.getMonth() + 1)).slice(-2);
        const dia = ('0' + fecha.getDate()).slice(-2);

        // Obtener iniciales y letras para el RFC
        const primeraLetraApellidoPaterno = lastname1.value.charAt(0).toUpperCase();
        const primeraVocalApellidoPaterno = encontrarPrimeraVocal(lastname1.value);
        const primeraLetraApellidoMaterno = lastname2.value.charAt(0).toUpperCase();
        const primeraLetraNombre = name.value.charAt(0).toUpperCase();

        // Generar homoclave aleatoria
        const homoclave = generarHomoclave();

        // Construir RFC
        rfc.value = primeraLetraApellidoPaterno + primeraVocalApellidoPaterno + primeraLetraApellidoMaterno + primeraLetraNombre + año + mes + dia + homoclave;
    } catch (error) {
        console.error('Error al generar RFC:', error);
        alert('Error al generar el RFC. Verifique los datos ingresados.');
    }
};

// Función para encontrar la primera vocal interna
const encontrarPrimeraVocal = (texto) => {
    if (!texto || texto.length < 2) return 'X';

    // Saltar la primera letra y buscar la primera vocal
    const textoRestante = texto.substring(1).toUpperCase();
    const vocales = ['A', 'E', 'I', 'O', 'U'];

    for (let letra of textoRestante) {
        if (vocales.includes(letra)) {
            return letra;
        }
    }

    return 'X'; // Si no encuentra vocal, usa X
};

// Función para limpiar campos
const limpiarCampos = () => {
    name.value = '';
    lastname1.value = '';
    lastname2.value = '';
    fechaNacimiento.value = null;
    rfc.value = '';
};
</script>

<template>
    <div class="fluid">
        <div class="flex flex-col md:flex-row gap-8">
            <div class="md:w-1/2">
                <div class="card flex flex-col gap-4">
                    <div class="font-semibold text-xl">GENERAR EL RFC DE UNA PERSONA</div>
                    <div class="flex flex-col gap-2">
                        <label for="name1">NOMBRE(s):</label>
                        <InputText id="name1" type="text" v-model="name" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="lastname1">APELLIDO PATERNO:</label>
                        <InputText id="lastname1" type="text" v-model="lastname1" />
                    </div>
                    <div class="flex flex-col gap-2">
                        <label for="lastname2">APELLIDO MATERNO:</label>
                        <InputText id="lastname2" type="text" v-model="lastname2" />
                    </div>

                    <div class="flex flex-col gap-2">FECHA DE NACIMIENTO:</div>
                    <Calendar :showIcon="true" :showButtonBar="true" v-model="fechaNacimiento" dateFormat="dd/mm/yy" class="w-full" />

                    <div class="flex flex-col gap-2">
                        <label for="rfc">RFC:</label>
                        <InputText id="rfc" type="text" v-model="rfc" readonly />
                    </div>
                    <div class="card flex flex-col gap-4">
                        <div class="flex flex-wrap gap-2">
                            <Button label="GENERAR" @click="generarRFC" class="p-button"></Button>
                            <Button label="LIMPIAR" @click="limpiarCampos" class="p-button-secondary"></Button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Panel de información -->
            <div class="md:w-1/2">
                <div class="card flex flex-col gap-4 p-4">
                    <div class="font-semibold text-xl">INFORMACIÓN SOBRE EL RFC</div>
                    <p>El RFC (Registro Federal de Contribuyentes) se genera con:</p>
                    <ul class="list-disc pl-5">
                        <li>Primera letra del apellido paterno</li>
                        <li>Primera vocal interna del apellido paterno</li>
                        <li>Primera letra del apellido materno</li>
                        <li>Primera letra del nombre</li>
                        <li>Fecha de nacimiento (año, mes, día)</li>
                        <li>Homoclave (en este ejemplo se usa "XA")</li>
                    </ul>
                    <div class="mt-4 p-3 border-round border-1 surface-border">
                        <div class="text-lg font-medium">Ejemplo:</div>
                        <div>Nombre: Juan</div>
                        <div>Apellidos: Pérez García</div>
                        <div>Fecha: 15/05/1990</div>
                        <div>RFC: <strong>PEGJ900515XA</strong></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.fluid {
    padding: 2rem;
    background-color: #f8f9fa;
    min-height: 100vh;
}

.card {
    background: white;
    border-radius: 12px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
    padding: 2rem;
}

:deep(.p-inputtext) {
    width: 100%;
}

:deep(.p-calendar) {
    width: 100%;
}
</style>
