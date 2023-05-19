<template>
	<div class="fixed-con">
		<div class="input-con">
			<input type="text" placeholder="Product name" v-model="product">
			<input type="text" placeholder="Description" v-model="productDescription">
			<input type="text" placeholder="Target market" v-model="productMarket">
			<button @click="btn">Generate marketing sample</button>
		</div>
	</div>

	<div class="text-output">
		<div id="output">
			<div class="text">
				<div v-if="isLoading">
					<i class="fa-sharp fa-solid fa-rotate fa-spin"></i>
				</div>
				<h1 v-if="output">
					Put in the name of your product and a short description of your product, also include your market target and let me do the magic for you.
				</h1>
				<p v-else>{{ text }}</p>
			</div>
		</div>
	</div>

</template>

<script>
import {ref, onMounted} from "vue"
import { Configuration, OpenAIApi } from 'openai'
export default{
	name: "InputData",

	setup(){
		let product = ref("")
		let productDescription = ref("")
		let productMarket = ref("")
		let text = ref("")
		let isLoading = ref(false)
		let output = ref(true)



		onMounted(() =>{
			document.body.style.backgroundColor = 'rgb(36, 36, 36)';
		})


		const configuration = new Configuration({
		apiKey: process.env.VUE_APP_API_KEY
		});
		
		const openai = new OpenAIApi(configuration);

		async function productAi(product, description, market){
			const response = await openai.createCompletion({
			max_tokens: 150,
			"model": "text-davinci-003",
			prompt: `The input here  shows the name of a product(brand), description and the target audience(customers), ensure they are much and rich in text.

			###

			product: Gluco-In.
			description: The touch is soft, a pleasant smell and a great taste.
			market: people who are above 60years.
			showcase: It is so durable, affordable and enjoyable, you want to make a feel a peak of it, then order yours now to feel the peak you are craving.


			###
			product: ${product}
			description: ${description}
			market: ${market}
			showcase:
			`

			});
			console.log(response)
			console.log(response.data.choices[0].text)
		

			if(response.status == 200){
				isLoading.value = false
			}
			text.value = response.data.choices[0].text

		}


		function btn(){
			if(!product.value || !productDescription.value || !productMarket.value) {
					console.log(product, "productName2")
					return
				} else {
					isLoading.value = true
					output.value = false
				}
			productAi(product.value, productDescription.value, productMarket.value)
			// console.log(product.value, "productName3")
		}


	

		return{
			product,
			productDescription,
			productMarket,
			text,
			btn,
			isLoading,
			output
		}
	}
}
</script>

<style scoped>
.fixed-con{
	position: fixed;
	right: 0vw;
	width: 25vw;
	/* border: 1px solid red; */
	height: 50vh;
	background-color: antiquewhite;
	box-shadow: 2px 2px 4px 2px red;
	border-radius: 2px;
}

.input-con{
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
}

input{
	padding: 2vh 1vw;
	margin-bottom: 3vh;
	width: 20vw;
	margin-top: 4px;
}

button{
	padding: 2vh 2vw;
	cursor: pointer;
	border-radius: 2px;
	border: none;
}


.text-output{
	display: grid;
	grid-template-columns: 80% 20%;
}


#output{
	width: 60vw;
	background-color: aquamarine;
	height: 100vh;
	margin-left: 3.5vw;
	word-wrap: break-word;
	padding: 3vh 3vw;
}

.text{
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
	margin: 35vh 0;
}

i{
	font-size: 24px;
}

p{
	font-family: Georgia, 'Times New Roman', Times, serif;
	font-size: 20px;
}
</style>