<script>
	import Router from "svelte-spa-router";
	import routes from "./routes";
</script>

<!-- This part is always displayed -->

<div class= "navigation container">
	<nav class="navbar nav-fill w-100 navbar-expand-lg navbar-light bg-light">
		<div class="container-fluid">
			<div class="collapse navbar-collapse" id="navbarNavDropdown">
				<ul class="navbar-nav">
					<li class="nav-item">
						<a class="nav-link" href="#/">Home</a>
					</li>				
					<li class="nav-item">
						<a class="nav-link" href="#/vote">Vote Classification</a>
					</li>			
					<li class="nav-item">
						<a class="nav-link" href="#/webcam">Webcam</a>
					</li>		
				</ul>
			</div>
		</div>
	</nav>

</div>

<div class="container mt-3">
	<!-- Here we load the page depending on the current URL -->
	<Router {routes} />
</div>