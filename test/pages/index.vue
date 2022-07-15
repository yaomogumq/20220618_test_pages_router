<template>
	
  <div class="app">
    
    <sidebar/>
    <div class="content">
    <h1>hi</h1>
    <button><a class="text-gray-800" :href="signin_url"> Click me to sign in </a> </button>
	<div>
	<!-- <button @click="gethub">GET HUB</button> -->
	<JsonTreeView :data="state.json" :maxDepth="3" @selected="onSelected" />	
	</div>
	
	<!-- <pre>{{hubs}}</pre> -->
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
.content {
	flex: 1 1 0;
}
.item {
	padding-left: 6rem;
	color: var(--primary-alt);

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

<script setup lang="ts">
import Sidebar from '~/components/sidebar';
import ForgeSDK from 'forge-apis';
import { JsonTreeView } from "json-tree-view-vue3";
import { defineComponent, reactive, ref } from "vue";


//define forge api tools
var HubsApi = new ForgeSDK.HubsApi(); //Hubs Client
var ProjectsApi = new ForgeSDK.ProjectsApi();
var FoldersApi = new ForgeSDK.FoldersApi();
var ItemsApi = new ForgeSDK.ItemsApi();
var url = document.URL;

var FORGE_CLIENT_ID = 'ovVGST7XyRDWeIyGUdYCmgtZF6IvVYZ4', FORGE_CLIENT_SECRET = 'T8ec853f398e3406', REDIRECT_URL = 'http://localhost:3001';
// Initialize the 3-legged OAuth2 client, set specific scopes and optionally set the `autoRefresh` parameter to true
// if you want the token to auto refresh
var scope='data:read';
var autoRefresh = true;
var oAuth2ThreeLegged = new ForgeSDK.AuthClientThreeLegged(FORGE_CLIENT_ID, FORGE_CLIENT_SECRET, REDIRECT_URL, [
    `${scope}`
], autoRefresh);
let signin_url = `https://developer.api.autodesk.com/authentication/v1/authorize?response_type=code&client_id=${FORGE_CLIENT_ID}&redirect_uri=${REDIRECT_URL}&scope=${scope}`;
const credentials = ref({});
const hubs = ref({});


const authorizationCode = url.split('?code=')[1];

credentials.value =  await oAuth2ThreeLegged.getToken(authorizationCode).then((credentials) =>{
   return credentials; // The `credentials` object contains an `access_token` and an optional `refresh_token` that you can use to call the endpoints.
}, function(err){
    console.error(err);
});

 hubs.value =  await HubsApi.getHubs({}, oAuth2ThreeLegged, credentials.value).then((hubs) =>{
	let newtree={};
	for (const i in hubs.body.data){
	 newtree[i]={hub:{id:hubs.body.data[i].id
	 ,name:hubs.body.data[i].attributes.name}};
	}			
    return newtree;
}, function(err){
     console.error(err);
});


var createNestedObject = function( base, names, value ) {
    // If a value is given, remove the last name and keep it for later:
    var lastName = arguments.length === 3 ? names.pop() : false;

    // Walk the hierarchy, creating new objects where needed.
    // If the lastName was removed, then the last object is not set yet:
    for( var i = 0; i < names.length; i++ ) {
        base = base[ names[i] ] = base[ names[i] ] || {};
    }

    // If a value was given, set it to the last name:
    if( lastName ) base = base[ lastName ] = value;

    // Return the last object in the hierarchy:
    return base;
};



var state = reactive({json:JSON.stringify(hubs.value)});


async function onSelected (event: unknown) {

	
	let temp = JSON.parse(state.json);
	
	switch(true){

		case event.path.includes("hub") && event.path.includes("id") && !event.path.includes("projects"):

		try{
				var Projects = await ProjectsApi.getHubProjects(event.value, {}, oAuth2ThreeLegged, credentials.value).then((Projects)=>
				
				{let newtree={};
				for (const i in Projects.body.data){
				newtree[i]={Project:{id:Projects.body.data[i].id
				,name:Projects.body.data[i].attributes.name}};
				}			
				return newtree;});
				
			}
			catch(err){
				console.log(err);
			}

			createNestedObject(temp, [event.path.match(/\d+/g)?.[0],'hub','projects'], {"projects": Projects});
			
			break;
		
		case event.path.includes("project") && event.path.includes("id"):
		
		
		try{
				var Folders = await ProjectsApi.getProjectTopFolders(hubs.value[event.path[2]].hub.id, event.value, oAuth2ThreeLegged, credentials.value).then((Folders)=>
				{return Folders});
				
			}
			catch(err){
				console.log(err);
			}
			
			
			createNestedObject(temp, [event.path[2],'hub','projects','projects',event.path.match(/\d+/g)?.[1],"Folders"], {"Folders": Folders}); ;
			break;
	

	  }


	

	state.json= JSON.stringify(temp) ;
	
}
// const  gethub= (url) => {
// 	try{
// 	authorizationCode=new URL(url).hash;
// 	credentials.access_token = authorizationCode.replace(/(#access_token=)|&[\s\S]+/g,'');
// 	alert(credentials.access_token)
// 	}
// 	catch(err){
// 		authorizationCode='error';
// 	}
	
// }


	// var hubs= async()=>await HubsApi.getHubs({}, oAuth2ThreeLegged, credentials).then((hubs) => 
	// 	JSON.stringify(hubs.body.data[1])).then((body) => { return (body);})

 





</script>



