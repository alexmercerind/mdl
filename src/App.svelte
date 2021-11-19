<script>
  import AppBar from "./AppBar.svelte";
  import TrackRoute from "./TrackRoute.svelte";
  import { Router, Route, Link } from "svelte-navigator";
</script>

<Router>
  <Route path="track/*">
    <TrackRoute />
  </Route>
</Router>
