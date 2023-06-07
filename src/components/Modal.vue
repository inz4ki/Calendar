<script>
import axios from 'axios';

export default {
    name: 'Modal',
    props: {
        visible: Boolean,
        variant: String,
        pk_id_etapa: Number
    },
    data() {
        return {
            OpenClose: this.visible,
        }
    },
    methods: {
        OpenCloseFun() {
            this.OpenClose = !this.OpenClose;
        },
    },
    watch: {
        visible: function (newVal, oldVal) {
            this.OpenClose = newVal;
            console.log('new ' + newVal + '==' + oldVal);
        }
    },
}
</script>
<template>
    <div v-if="OpenClose" class="modal fade show" style="display:block" tabindex="-1" aria-labelledby="exampleModalLabel"
        aria-modal="true" role="dialog">
        <div class="modal-dialog">
            <div class="modal-content fundo">
                <div class="modal-head">
                    <div class="modal-body">
                    </div>
                    <div class="modal-footer">
                        <button type="button" @click="OpenCloseFun()" :class="'btn btn-danger'">Fechar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <button type="button" @click="OpenCloseFun()" :class="'btn btn-' + variant">Incluir</button>
</template>
<style scoped>
label {
    margin-top: 10px;
}

.botao {
    margin-top: 10px;
}

.fundo {
    background-color: rgb(224, 224, 224);
    border: 2px solid black;
}
</style>