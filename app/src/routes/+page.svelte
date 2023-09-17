<script>
    import { onMount } from "svelte";
import Level from "../components/level.svelte";
import GD from "gd.js";

const gd = new GD({
    "dbURL": "https://chillgdps.ps.fhgdps.com",
    "corsURL": "https://corsproxy.io/?"
    //"corsURL": "https://mydemoncorsproxy.batteraquette58.repl.co/"
});
const demons = (async () => {
    let num = 70;
    while (true) {
        try {
            return await gd.levels.search({"demon": true}, num);
        } catch {
            num -= 10;
        }
    }
})();
async function fetchUser(account_id) {
    return await gd.users.getByAccountID(account_id);
};
function isRated(level) {
    return level.award.raw != 0;
}
let level_name, creator, id;
function addLevel() {
    demons.then((response) => {
        response = response.filter(level => {
            return isRated(level);
        });
        let chosen = response[Math.floor(Math.random()*response.length)];
        level_name = chosen.name;
        let creator_id = chosen.creator.accountID;
        fetchUser(creator_id).then(
            user => creator = user.username
        );
        id = chosen.id;
    });
};
onMount(() => addLevel());
</script>

<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container">
        <span class="navbar-brand">MyDemon</span>
        <button class="btn btn-primary d-flex" on:click={addLevel}>Generate</button>
    </div>
</nav>
<h3 style="text-align: center;"><strong>If it says undefined, wait a bit. If nothing loads in a few seconds, try refreshing the page.</strong></h3>
<Level level_name={level_name} creator={creator} id={id} />
<p style="text-align: center;">little applet made by batte :D</p>