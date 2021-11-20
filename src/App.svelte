<script>
  import TrackRoute from "./Track.svelte";
  import Home from "./Home.svelte";
  import { Router, Route } from "svelte-navigator";

  fetch("https://x1yb80pwsn4.herokuapp.com");
</script>

<Router>
  <Route path="track/*">
    <TrackRoute />
  </Route>
  <Route path="/*">
    <Home />
  </Route>
</Router>
