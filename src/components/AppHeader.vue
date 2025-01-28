<script>
	export default {
		data() {
			return { 
			
			}
		}
	}
</script>

<template>
	<header class="d-flex align-items-center">
		<nav class="navbar navbar-expand col-12">
			<div class="container">
						
						
				<div class="collapse navbar-collapse" id="navbarText">

					<img class="img-logo col-auto me-auto " src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQw7HGZeDZhcenqWe4MQTJ2N8BFRZQ4VOibKw&s" alt="douglas">

					<h1 class='text-center fw-bold col me-auto title'>
						DOUGLAS
					</h1>
					<button class="btn bg-black text-white fw-bold">
						<a class="nav-link" href="http://127.0.0.1:8000/login">
                        	Accedi
                    	</a>
					</button>
						
				</div>
				
			</div>
		</nav>
	</header>
</template>

<style lang="scss" scoped>

	header{

		height: 15vh;
    	background-color: darkgray;

		h1{
			color: #C3EAE7;
			font-size: 70px;
		}

		.img-logo{

			height: 10vh;
		}
	}

	
</style>
