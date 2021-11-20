<script>
  import TrackRoute from "./Track.svelte";
  import Home from "./Home.svelte";
  import { Router, Route } from "svelte-navigator";

  fetch("https://x1yb80pwsn4.herokuapp.com");
  addEventListener("load", function () {
    var viewport = document.querySelector("meta[name=viewport]");
    viewport.setAttribute(
      "content",
      viewport.content + ", height=" + window.innerHeight
    );
  });
</script>

<Router>
  <Route path="track/*">
    <TrackRoute />
  </Route>
  <Route path="/*">
    <Home />
  </Route>
</Router>
