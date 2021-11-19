<script>
    import { onMount } from "svelte";
    import { fly, fade, scale } from "svelte/transition";
    import Loader from "./Loader.svelte";

    let label = (duration) => {
        return `${Math.round(duration / 60)}:${duration % 60 < 10 ? "0" : ""}${
            duration % 60
        }`;
    };

    let id = (link) => {
        if (link.includes("youtu.be")) {
            if (link[link.length - 1] == "/") {
                return link.split("/")[link.split.length - 2];
            }
            return link.split("/")[link.split.length - 1];
        } else if (link.includes("youtube.com")) {
            if (link.includes("&")) {
                return link.substring(
                    link.indexOf("v=") + 2,
                    link.indexOf("&")
                );
            } else
                return link
                    .substring(link.indexOf("v=") + 2, link.length)
                    .replace("/", "");
        }
        return link;
    };

    let track;
    onMount(async () => {
        let response = await fetch(
            `http://127.0.0.1:8000/track_info?track_id=${id(location.href)}`
        );
        track = await response.json();
    });
</script>

<div class="scaffold">
    {#if track == undefined}
        <Loader />
    {:else}
        <div
            in:fly={{ delay: 100, duration: 500, y: 100 }}
            out:fade={{ duration: 500 }}
            class="track"
        >
            <img
                class="album-art"
                src={track["album_art_high"]}
                alt="album_art_high"
            />
            <div class="metadata">
                <div class="title">{track["track_name"]}</div>
                <br />
                <div>{track["album_artist_name"]}</div>
                <div>{track["track_artist_names"].join(", ")}</div>
                <div>{track["album_name"]}</div>
                <div>{label(track["track_duration"])}</div>
                <div>{track["year"]}</div>
                <div>{track["track_number"]}/{track["album_length"]}</div>
            </div>
        </div>
    {/if}
</div>

<style>
    .scaffold {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100vh;
        width: 100vw;
        margin: 0;
        padding: 0;
        overflow-y: hidden;
    }

    .track {
        border: 1px solid var(--border-color);
        border-radius: 4px;
        height: 420px;
        max-width: 840px;
        width: 100%;
        margin: 24px;
        box-sizing: border-box;
        display: flex;
        flex-direction: row;
        background-color: var(--card-color);
    }

    .album-art {
        border-radius: 4px;
    }

    .metadata {
        margin: 18px 24px 18px 24px;
        width: max-content;
    }
</style>
