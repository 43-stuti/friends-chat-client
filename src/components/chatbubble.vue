<template>
    <div>
        <div class="container">
            <div class="bubbleC"> 
                <div v-bind:style="bubbleStyle" class="bubble">
                        <div v-bind:style="colourStyle">
                            <h6 v-if ="!this.message.self">{{message.user}}</h6>
                        </div>
                        <div>
                            {{message.message}}
                        </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            colourmapping: {
                'Chandler': '#d12725',
                'Joey': '#00b8eb',
                'Monica': '#d12725',
                'Phoebe': '#fbbe15',
                'Rachel':'#00b8eb',
                'Ross': '#fbbe15'
            },
        }
    },
    name:'chatbubble',
    props:['message'],
    computed: {
        bubbleStyle() {
            if(this.message.self)   {
                return {
                    'border': '1px solid',
                    'border-color':'#f0e911',
                    'border-radius': '5px',
                    'colour':'#ffffff'
                };
            } else {
                return {
                    'border': '1px solid',
                    'border-color':'#595959',
                    'border-radius': '5px',
                    'colour':'#ffffff'
                };
            }
        },
        colourStyle() {
            return {
                'color':this.colourmapping[this.message.character]
            }
        }
    },
    components: {
    }
}
</script>
<style scoped>
    .container {
        margin-bottom:20px;
    }
    .bubbleC {
    }
    .bubble {
        display: inline-block;
        padding-left: 20px;
        padding-right: 20px;
        padding-top: 5px;
        padding-bottom: 5px;
    }
</style>