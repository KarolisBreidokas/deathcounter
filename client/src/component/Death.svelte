<script lang="ts">
  import image from "@/asset/bonefire.gif";
  import { socket } from "@/lib/ws";
  import { onDestroy } from "svelte";
  import { navigate, useParams } from "svelte-navigator";

  const sct = socket();
  const params = useParams();
  let deaths = 0;

  const joinfn = ()=>{
    console.log("connected :)");
    sct.emit("join", { channel: $params.channel }, ({ data, error }) => {
    if (error) {
      navigate("/?error", { replace: true });
    } else {
      deaths = data?.deaths | 0;
    }
  });
  }
  sct.on("connect", joinfn)
  sct.on("death_change", (data) => {
    deaths = data?.deaths;
  });

  onDestroy(() => {
    sct.close()
  });
</script>

<div class="deaths">
  <img src={image} alt="bonefire" />
  <span class="text">
    {deaths}&nbsp;death{Math.abs(deaths) === 1 ? "" : "s"}
  </span>
</div>

<style lang="css">
  .deaths {
    display: grid;
    justify-items: center;
    width: fit-content;
    gap: 1rem;
    font-weight: 900;
    font-size: var(--fs-300);
    padding: 1rem;
    background-color: var(--clr-dark-000);
    border-radius: 0.5rem;
  }
</style>
