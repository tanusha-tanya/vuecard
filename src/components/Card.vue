<template>
    <div class="card">
        <template v-if="followersData">
            <div class="card-header">
                <h1 class="card-h1">{{followersData.displaySettings.subType}}</h1>
            </div>
            <div class="card-body">
                <div class="card-top">
                    <div class="card-title">
                        <img src="@/assets/arrows.svg" alt="arrows" class="card-top-icon"/>
                        {{followersData.displaySettings.type}}
                    </div>
                </div>
                <div class="card-middle">
                    <div class="card-side__left">
                        <h2 class="card-h2">{{followersData.displaySettings.type}} count</h2>
                        <div class="card-select">is
                            <div class="card-selectbox">
                                <a href="#" @click.prevent="selectIsOpen = !selectIsOpen" class="card-selectbox-opener">
                                    {{option}} 
                                    <img src="@/assets/down.svg" alt="down"/>
                                </a>    
                                <transition  name="bounce">
                                    <div class="card-selectbox-options"  v-if="selectIsOpen">
                                        <a href="#" class="card-selectbox-link" @click.prevent="option='Greater'; selectIsOpen = false">Greater</a>
                                        <a href="#" class="card-selectbox-link" @click.prevent="option='Less'; selectIsOpen = false">Less</a>
                                    </div>
                                </transition>                      
                            </div>
                        than</div>
                    </div>
                    <div class="card-side__right">
                        <template v-for="(element, index) in followersData.elements" >
                            <label class="card-item-label" :for="`value-${index+1}`" :key="index">
                                <a href="#" @click.prevent="deleteElement($event, index)" v-if="followersData.elements.length !== 1" class="card-cross">
                                    <img src="@/assets/cross.svg" alt="delete" width="20" height="20"/>
                                </a>
                                <input type="text" 
                                    class="card-item"                                
                                    :id="`value-${index+1}`"
                                    @change="changeValue($event, index)"
                                    :value="element.condition.value"
                                    />
                            </label>
                        </template>
                        <button class="card-addvalue" @click.prevent="addvalue">
                        + Add value
                        </button>
                    </div>
                </div>
                <div class="card-bottom">
                    <div class="card-side__left">
                        Followers count is
                    </div>
                    <div class="card-side__right card-count">
                        {{counter}} or {{option}}
                    </div>
                </div>
            </div>
        </template>
        <template v-else>
            <img src="@/assets/preloader.gif" alt="preloader" width="50" height="50" class="card-preloader"/>
        </template>
    </div>
</template>
<script>
    import axios from 'axios'
    export default{
        name: 'Card',
        data() {
            return {
                followersData: null,
                selectIsOpen: false,
                option: 'Greater'
            };
        },
        computed:{
            counter(){
                let arr = []
                this.followersData.elements.forEach(el => arr.push(el.condition.value))
                return Math.max(...arr)
            }
        },
        mounted() {
            axios
            .get('/data/followers.json')
            .then(response => (this.followersData = response.data));
        },
        methods:{
            addvalue(){
                let arr = this.followersData.elements
                let newElement = JSON.parse(JSON.stringify(arr[arr.length-1]));
                newElement.onMatch = null  
                this.followersData.elements.push(newElement)              
                if(this.followersData.elements.length > 2){
                    let onfail = arr[arr.length-2].onFail
                    newElement.onFail = onfail
                    this.followersData.elements[this.followersData.elements.length-2].onFail = {"action":"fallthrough"}
                }               
            },
            changeValue(event, index){
                if(event.target.value !== "" && !isNaN(Number(event.target.value))){
                    this.followersData.elements[index].condition.value = event.target.value
                }
                else{
                    event.target.value = this.followersData.elements[index].condition.value 
                }
            },
            deleteElement(event, index){
                this.followersData.elements.splice(index, 1)
                this.followersData.elements[this.followersData.elements.length-1].onMatch = null 
                if(this.followersData.elements.length > 2){
                    this.followersData.elements[this.followersData.elements.length-2].onFail = {"action":"fallthrough"}
                }  
            }
        }
    }
</script>
<style>
    *{
        margin:0;
        padding:0
    }
    body{
        margin: 0 auto;
    }
    .card{
        max-width: 246px;
        background: #FFFFFF;
        box-shadow: 0px 6px 32px rgba(152, 169, 188, 0.24), 0px 4px 8px rgba(119, 140, 162, 0.08);
        border-radius: 6px;
        position: relative;
        margin: 166px auto;
        font-family: Rubik, Roboto, sans-Serif;
        font-size: 12px;
        line-height: 14px;
        color: #778CA2;
    }
    .card-preloader{
        position: relative;
        margin: 50px auto;
        display: block;
    }
    .card-header{
        background: #FF7629;
        padding: 12px;
        border-top-left-radius: 6px;
        border-top-right-radius: 6px;
        position: relative;
    }
    .card-header:before{
        content: '';
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 100%;
        background: #FFFFFF;
        border: 2px solid #FF7629;
        box-sizing: border-box;
        box-shadow: 0px 2px 16px rgba(153, 155, 168, 0.12);
        position: absolute;
        left: -5px;
        top: 13px;
    }
    .card-h1{
        font-family: Rubik, Roboto, sans-Serif;
        font-style: normal;
        font-weight: 500;
        font-size: 12px;
        line-height: 14px;
        color: #FFFFFF;
        text-align: left
    }
    .card-top{
        padding: 12px;
        border-bottom: 1px solid #F2F4F6;
    }
    .card-title{      
        font-size: 10px;
        line-height: 12px;
    }
    .card-top-icon{
        display: inline-block;
        margin-right: 5px;
        vertical-align: middle;
    }
    .card-middle{
        display: flex;
    }
    .card-side__left{
        padding: 12px;
        width: 154px;
        box-sizing: border-box;
    }
    .card-h2{
        font-size: 12px;
        line-height: 14px;
    }
    .card-select{
        display: flex;
    }
    .card-selectbox{
        position: relative;
    }
    .card-selectbox-opener{
        color: #252631;
        text-decoration: none;
        margin: 0 5px;
    }
    .card-selectbox-options{
        background: #FFFFFF;
        
        box-shadow: 0px 6px 32px rgba(152, 169, 188, 0.24), 0px 4px 8px rgba(119, 140, 162, 0.08);
        border-radius: 6px;
        position: absolute;
        left: 3px;
    }
    .card-selectbox-link{
        color: #252631;
        text-decoration: none;
        display: block;
        padding: 3px;
    }
    .card-selectbox-link:hover, .card-selectbox-link:active, .card-selectbox-link:focus{
        background: rgba(152, 169, 188, 0.24);
        
    }
    .card-side__right{
        width: 92px;
        box-sizing: border-box;
        border-left: 1px dashed #E8ECEF;
    }
    .card-item{
        padding: 12px;
        font-size: 12px;
        line-height: 14px;
        color: #252631;
        border: 0;
        border-bottom: 1px dashed #E8ECEF;
        outline: none;
        position:relative;
        box-sizing: border-box;
        width: 100%;
    }
    .card-item-label{
        position:relative;
        width: 100%;
        display: flex;
        align-items: center;
    }
    .card-item-label:after{
        content: '';
        border: 2px solid #FF7629;
        box-sizing: border-box;
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 100%;
        box-shadow: 0px 2px 16px rgba(153, 155, 168, 0.12);
        position: absolute;
        right: -5px;
    }
    .card-item-label:hover .card-cross{
        opacity: 1;
    }
    .card-cross{
        position: absolute;
        left: -10px;
        z-index: 300;
        opacity: 0;
        transition: opacity .3s;
    }
    .card-addvalue{
        background: none;
        outline: none;
        border: 0;
        padding: 12px;
        display: block;
        font-size: 12px;
        line-height: 14px;
        color: #6DD230;
        cursor: pointer;
    }
    .card-bottom{
        border-top: 1px dashed #E8ECEF;
        display: flex;
    }
    .card-count{
        color: #FF4550;
        padding: 12px;
    }
    .bounce-enter-active {
      animation: bounce-in .3s;
    }
    .bounce-leave-active {
      animation: bounce-in .3s reverse;
    }
  @keyframes bounce-in {
    0% {
      transform: scale(0);
    }
    50% {
      transform: scale(1.1);
    }
    100% {
      transform: scale(1);
    }
  }
</style>