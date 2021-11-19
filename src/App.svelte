<script>
  import TrackRoute from "./TrackRoute.svelte";
  import AlbumRoute from "./AlbumRoute.svelte";
  import SearchRoute from "./SearchRoute.svelte";
  import { Router, Route, Link } from "svelte-navigator";
</script>

<Router>
  <Route path="track/*">
    <TrackRoute />
  </Route>
  <Route path="album/*">
    <AlbumRoute />
  </Route>
  <Route path="search/*">
    <SearchRoute />
  </Route>
</Router>
