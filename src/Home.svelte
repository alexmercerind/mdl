<script>
    import { onMount } from "svelte";
    import { fly, fade } from "svelte/transition";
    import { Link, navigate } from "svelte-navigator";
    import Loader from "./Loader.svelte";
    import { MDCTextField } from "@material/textfield";
    let label = (duration) => {
        return `${Math.round(duration / 60)}:${duration % 60 < 10 ? "0" : ""}${
            duration % 60
        }`;
    };

    let result;
    onMount(async () => {
        var search = new MDCTextField(document.querySelector("#search-field"));
        search.input.addEventListener("focusin", async (event) => {
            document.getElementById("search-icon").style.color =
                "var(--accent-color)";
        });
        search.input.addEventListener("focusout", async (event) => {
            document.getElementById("search-icon").style.color = "#aaaaaa";
        });
        search.input.addEventListener("keydown", async (event) => {
            if (event.code === "Enter") {
                result = undefined;
                if (search.value === "") {
                    result = "Lookup for the music of your choice";
                    console.log(typeof result);
                } else {
                    let response = await fetch(
                        `https://x1yb80pwsn4.herokuapp.com/search?keyword=${search.value}&mode=track`
                    );
                    result = await response.json();
                    result = result["result"];
                    navigate(`/${search.value}`, { replace: true });
                    search.focus();
                }
            }
        });
        if (location.pathname == "/") {
            result = "Lookup for the music of your choice";
            return;
        }
        let keyword = location.pathname.split("/").at(-1);
        search.value = decodeURIComponent(keyword);
        let response = await fetch(
            `https://x1yb80pwsn4.herokuapp.com/search?keyword=${decodeURIComponent(
                keyword
            )}&mode=track`
        );
        result = await response.json();
        result = result["result"];
        search.focus();
    });
</script>

<div class="search-container">
    <i
        id="search-icon"
        style="font-size:32px; color: #aaaaaa; margin-right: 12px;"
        class="material-icons">search</i
    >
    <label id="search-field" class="mdc-text-field mdc-text-field--outlined">
        <span class="mdc-notched-outline">
            <span class="mdc-notched-outline__leading" />
            <span class="mdc-notched-outline__notch">
                <span
                    style="font-family: var(--font-family); font-size: 15px"
                    class="mdc-floating-label"
                    id="my-label-id">Lookup Music</span
                >
            </span>
            <span class="mdc-notched-outline__trailing" />
        </span>
        <input
            style="font-family: var(--font-family); font-size: 15px"
            type="text"
            class="mdc-text-field__input"
            aria-labelledby="my-label-id"
        />
    </label>
</div>
<div class="scrollview">
    {#if result === undefined}
        <div class="loader-container">
            <Loader />
        </div>
    {:else if typeof result === "string"}
        <i
            id="search-icon"
            style="font-size:280px; z-index: -1000; color: #cccccc; margin-right: 12px; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);"
            class="material-icons">music_note</i
        >
        <div class="centered-label">
            <div>{result}</div>
        </div>
    {:else}
        <div class="scaffold">
            {#each result as track}
                <Link
                    style="text-decoration: none;
            margin-top: 12px;
            margin-bottom: 12px;
            margin-left: 24px;
            margin-right: 24px;"
                    to={`/track/${track["track_id"]}+${track["album_id"]}`}
                >
                    <div
                        in:fly={{ delay: 100, duration: 500, y: 100 }}
                        out:fade={{ duration: 500 }}
                        class="track-container mdc-ripple-surface"
                    >
                        <img
                            class="album-art"
                            src={track["album_art_low"]}
                            alt="album_art"
                        />
                        <div>
                            <div>
                                {track["track_name"]}
                            </div>
                            <div class="subtitle">
                                {track["album_name"]}
                            </div>
                            <div class="subtitle">
                                {label(track["track_duration"])}
                            </div>
                        </div>
                    </div>
                </Link>
            {/each}
        </div>
    {/if}
</div>

<style>
    .mdc-text-field {
        width: 100%;
        margin-right: 12px;
    }

    .centered-label {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100%;
    }

    .search-container {
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        padding-top: 18px;
        padding-bottom: 16px;
        padding-left: 18px;
        padding-right: 10px;
        box-sizing: border-box;
        position: absolute;
        z-index: 1000;
        background-color: var(--card-color);
        box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
    }

    .scaffold {
        display: flex;
        flex-direction: column;
        position: absolute;
        width: 100%;
        margin-bottom: 12px;
        padding-top: 12px;
        padding-bottom: 12px;
        box-sizing: border-box;
        overflow-y: scroll;
        height: 100vh;
        padding-top: 104px;
    }

    .scrollview {
        overflow: hidden;
        height: 100%;
        width: 100%;
        flex: 1;
    }

    .loader-container {
        position: absolute;
        display: flex;
        flex-direction: column;
        align-items: center;
        left: calc(50% - 16px);
        top: calc(50% - 16px);
    }

    .track-container {
        box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
        background-color: var(--card-color);
        height: 96px;
        display: flex;
        flex-direction: row;
        align-items: center;
        border-radius: 4px;
        box-sizing: border-box;
    }

    .track-container:hover {
        box-shadow: 0 10px 20px rgba(0, 0, 0, 0.25),
            0 6px 8px rgba(0, 0, 0, 0.22);
    }

    .album-art {
        margin-right: 12px;
        width: 96px;
        height: 96px;
        border-radius: 4px;
    }
</style>
