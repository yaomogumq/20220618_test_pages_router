<template>
  <div class="app">
    
    <sidebar/>
    <div>
    <h1>hi</h1>
    <button><a class="text-gray-800" :href="signin_url"> Click me to sign in </a> </button>
	 <pre>{{hubs}}hihi</pre>
    </div>
    
    
    
  </div>
  
  
</template>

<style lang="scss">
:root {
	--primary: #3759c9;
	--primary-alt: #22c55e;
	--grey: #64748b;
	--dark: #2c3f61;
	--greyer: #9ba0a7;
	--dark-alt: #bbd8bf;
	--light: #f1f5f9;
	--sidebar-width: 300px;
}
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Fira sans', sans-serif;
}
body {
	background: var(--light);
}
button {
	cursor: pointer;
	appearance: none;
	border: none;
	outline: none;
	background: none;
}
.app {
	display: flex;
	main {
		flex: 1 1 0;
		padding: 2rem;
		@media (max-width: 768px) {
			padding-left: 6rem;
		}
	}
}

</style>

<script setup>
import Sidebar from '~/components/Sidebar';
import ForgeSDK from 'forge-apis';

var HubsApi = new ForgeSDK.HubsApi(); //Hubs Client

var url = new URL(document.URL);
var authorizationCode = 'null';
var FORGE_CLIENT_ID = 'ovVGST7XyRDWeIyGUdYCmgtZF6IvVYZ4', FORGE_CLIENT_SECRET = 'T8ec853f398e', REDIRECT_URL = 'http://localhost:3001';
// Initialize the 3-legged OAuth2 client, set specific scopes and optionally set the `autoRefresh` parameter to true
// if you want the token to auto refresh
var scope='data:read';
var autoRefresh = true;
var oAuth2ThreeLegged = new ForgeSDK.AuthClientThreeLegged(FORGE_CLIENT_ID, FORGE_CLIENT_SECRET, REDIRECT_URL, [
    `${scope}`
], autoRefresh);
var credentials={
  access_token:''
}

let signin_url = `https://developer.api.autodesk.com/authentication/v1/authorize?response_type=token&client_id=${FORGE_CLIENT_ID}&redirect_uri=${REDIRECT_URL}&scope=${scope}`;
let hubs;
//console.log(signin_url);
 try{
  authorizationCode=url.hash;
  credentials.access_token = authorizationCode.replace(/(#access_token=)|&[\s\S]+/g,'');
  hubs= await HubsApi.getHubs({}, oAuth2ThreeLegged, credentials).then((hubs) => 

	 JSON.stringify(hubs.body.data)).then((body) => { return JSON.parse(body);})
    //hubs.body.data[1].attributes.name
 }
  
 catch(err){
 	authorizationCode='error';
 }


console.log(hubs);

</script>