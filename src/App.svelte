<script lang="ts">
	import { onMount } from 'svelte';
  import Jquery from 'jquery'



  type Zone = {
    name: string;
    development_mode: number;
    id: string;
    devModeStatus: boolean;
  };


  let zones: Zone[] = [];

  onMount(async () => {
    const response = await fetch("http://localhost:3003");
    const zones_res: Zone[] = await response.json();
    
    zones = zones_res

  })
  
  function test(){
    console.log("test func")
  }

  async function getZones() {
    const response = await fetch("http://localhost:3003");
    const zones_res: Zone[] = await response.json();
    zones = zones_res
    console.log(zones)
  }


  async function toggleDevelopmentModeOn(id: string) {
    fetch(`http://localhost:3003/devmode/on/${id}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({"value": "on"})
    }).then(() => getZones())
    console.log("dev mode on")

  
  }

  async function toggleDevelopmentModeOff(id: string) {
    fetch(`http://localhost:3003/devmode/off/${id}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({value: 'off'})
    }).then(() => getZones())
    console.log("dev mode off")
  }
  
  

function getDevStatus(zone: Zone): boolean {
  if (zone.development_mode > 0) {
    return zone.devModeStatus = true
  } else {
    return zone.devModeStatus = false
  }
}


</script>


<main>

  <table class="ui celled table">
    <thead>
      <tr><th>Site Name</th>
      <th>Development Mode</th>
      <th>Toggle Development Mode</th>
    </tr></thead>
    <tbody>
      {#each zones as zone}
      <tr>
        <td>{zone.name}</td>
        <td>{getDevStatus(zone) ? "on" : "off"}</td>
        <td class="collapsing">
          <div class="ui fitted slider checkbox">
            <input type="checkbox"
            bind:checked={zone.devModeStatus}
            on:change={() => getDevStatus(zone) ? toggleDevelopmentModeOff(zone.id) : toggleDevelopmentModeOn(zone.id)}>
              <label></label>
          </div>
        </td>
      </tr>
      {/each}
    </tbody>
  </table>

</main>

