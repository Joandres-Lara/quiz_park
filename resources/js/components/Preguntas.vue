<template>
<div class="container">
    <div class="row">
        <div class="col text-center">
            <h2 v-text="examen.nombre_examen"></h2>
            <p v-text="'categoria: '+categorias.nombre_categoria"></p>
        </div>
    </div>
    <div class="row">
        <div class="col-md-6">
            <label for="">Pregunta:</label>
            <textarea v-model="pregunta" class="form-control" rows="5" cols="80"></textarea>
            <label for="">Respuesta A:</label>
            <input v-model="respuesta_a" class="form-control" type="text">
            <label for="">Respuesta B:</label>
            <input v-model="respuesta_b" class="form-control" type="text">
            <label for="">Respuesta C:</label>
            <input v-model="respuesta_c" class="form-control" type="text">
            <label for="">Respuesta D:</label>
            <input v-model="respuesta_d" class="form-control" type="text">
            <label for="">Respuesta correcta:</label>
            <select v-model="respuesta_correcta" class="custom-select" name="">
                <option value="">Seleccione</option>
                <option v-if="respuesta_a>''" v-text="respuesta_a" :value="respuesta_a"></option>
                <option v-if="respuesta_b>''" v-text="respuesta_b" :value="respuesta_b"></option>
                <option v-if="respuesta_c>''" v-text="respuesta_c" :value="respuesta_c"></option>
                <option v-if="respuesta_d>''" v-text="respuesta_d" :value="respuesta_d"></option>
            </select>
            <button @click="crearPregunta()" class="btn btn-warning mt-2">Crear pregunta</button>

        </div>
        <div class="col-md-6">
            <div v-for="pregunta in arrayPreguntas" :key="pregunta.id" class="">
                <h4 v-text="pregunta.pregunta"></h4>
                <ul>
                    <li v-text="'A: ' + pregunta.a_opt"></li>
                    <li v-text="'B: ' + pregunta.b_opt"></li>
                    <li v-text="'C: ' + pregunta.c_opt"></li>
                    <li v-text="'D: ' + pregunta.d_opt"></li>
                </ul>
                <p v-text="'respuesta: ' + pregunta.respuesta"></p>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import Swal from 'sweetalert2';
export default {
    props: ['examen'],
    data() {
        return {
            categorias: {},
            pregunta: '',
            respuesta_a: '',
            respuesta_b: '',
            respuesta_c: '',
            respuesta_d: '',
            respuesta_correcta: '',
            arrayPreguntas: []
        }
    },
    methods: {
        obtenerCategoria(url, datos) {
            let me = this;
            axios.get(url).then(function(response) {
                var consulta = response.data.categorias;
                me.categorias = consulta;
            }).catch(function(error) {
                console.log(error)
            });
        },
        obtenerPreguntas() {
            let me = this;
            axios.get('listar_preguntas?id=' + this.examen.id).then(function(response) {
                var respuesta = response.data.preguntas;
                console.log(response);
                me.arrayPreguntas = respuesta;
            }).catch(function(error) {
                console.log(error)
            });
        },
        crearPregunta() {
            let me = this;
            axios.post('crear_pregunta', {
                'examen_id': this.examen.id,
                'pregunta': this.pregunta,
                'a_opt': this.respuesta_a,
                'b_opt': this.respuesta_b,
                'c_opt': this.respuesta_c,
                'd_opt': this.respuesta_d,
                'respuesta': this.respuesta_correcta
            }).then(function(response) {
                var registro = me.pregunta;
                me.alerta(registro);
                me.resetear();
                me.obtenerPreguntas();
            }).catch(function(error) {
                console.log(error)
            });
        },
        resetear() {
            this.pregunta = "";
            this.respuesta_a = "";
            this.respuesta_b = "";
            this.respuesta_c = "";
            this.respuesta_d = "";
            this.respuesta_correcta = "";
        },
        alerta(mensaje) {
            Swal.fire({
                position: 'top-end',
                icon: 'success',
                title: 'La pregunta: ' + mensaje + ' fue creada',
                showConfirmButton: false,
                timer: 1500
            });
        }
    },
    mounted() {
        console.log('Component preguntas mounted.');
        console.log(this.examen);
        this.obtenerCategoria('listar_categoria?id=' + this.examen.categoria_id);
        this.obtenerPreguntas();
    }
}
</script>
