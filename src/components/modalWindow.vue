<template>
    <div class="modal-mask"> 
        <div class="modal-wrapper" @click.self="$emit('emit-modal')"> 
            <div class="modal-container" >
                <div class="modal-header">
                    <span>Modal </span>
                    <input class="modal-header_close" type="button" value="X" @click="$emit('emit-modal')">
                </div>
                <div>
                    <ul>
                        <li v-for="(item,name,index) in selectElement" :key="index">
                            <span>{{name}} - {{item}}</span>
                        </li>
                    </ul>
                </div>
                <div>
                    
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name:'modalWindow',
    props: {
        selectElement: {
            type: Object,
            default: null,
            required: true,

        }
    },
    mounted() {
        document.addEventListener("keydown",this.escapeKeyListener);
    },
    destroyed() {
        document.removeEventListener('keydown',this.escapeKeyListener);
    },
    methods:{
        escapeKeyListener:function(event){
            if (event.keyCode == 27) {
                this.$emit('emit-modal');
            }
        }
    }
    
}
</script>

<style scoped>
.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: table;
    transition: opacity 0.3s ease;
}
.modal-wrapper {
    display: table-cell;
    vertical-align: middle;
}

.modal-container {
    width: 300px;
    height: 300px;
    margin: 0 auto;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    transition: all 0.3s ease;
    font-family: Helvetica, Arial, sans-serif;
    position: relative;
}

.modal-header_close {
    position: absolute;
    top: 0;
    right: 0;
}
</style>