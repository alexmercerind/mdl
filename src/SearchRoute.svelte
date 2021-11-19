<script>
    import { onMount } from "svelte";
    import { fly, fade } from "svelte/transition";
    import { Link } from "svelte-navigator";
    import Loader from "./Loader.svelte";

    let label = (duration) => {
        return `${Math.round(duration / 60)}:${duration % 60 < 10 ? "0" : ""}${
            duration % 60
        }`;
    };

    let result;
    onMount(async () => {
        let keyword = location.href.split("/").at(-1);
        let response = await fetch(
            `http://localhost:8000/search?keyword=${decodeURIComponent(
                keyword
            )}&mode=track`
        );
        result = await response.json();
        result = result["result"];
        console.log(result);
    });
</script>

{#if result == undefined}
    <div class="loader-container">
        <Loader />
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
                to={`/track/${track["track_id"]}`}
            >
                <div
                    in:fly={{ delay: 100, duration: 500, y: 100 }}
                    out:fade={{ duration: 500 }}
                    class="track-container"
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

<style>
    .scaffold {
        display: flex;
        flex-direction: column;
        position: absolute;
        width: 100%;
        margin-bottom: 12px;
        padding-top: 12px;
        padding-bottom: 12px;
        box-sizing: border-box;
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
