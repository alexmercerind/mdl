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
        return link.split("/")[link.split("/").length - 1];
    };

    let track;
    onMount(async () => {
        console.log(id(location.href));
        let response = await fetch(
            `http://127.0.0.1:8000/track_info?track_id=${id(location.href)}`
        );
        track = await response.json();
    });
</script>

<div class="scaffold">
    {#if track == undefined}
        <div class="loader-container">
            <Loader />
        </div>
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
                <div>
                    <div class="title">{track["track_name"]}</div>
                    <br />
                    <div>
                        {label(track["track_duration"])} • {track[
                            "track_number"
                        ]}/{track["album_length"]}
                    </div>
                    <div>{track["album_artist_name"]}</div>
                    <div>{track["track_artist_names"].join(", ")}</div>
                    <div>{track["album_name"]}</div>
                    <div>{track["year"]}</div>
                </div>
                <div class="button-bar">
                    <a
                        class="track-button"
                        target="__blank"
                        href="http://127.0.0.1:8000/download/{track[
                            'track_id'
                        ]}.ogg">Download</a
                    >
                    <div style="width: 12px;" />
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <a
                        class="track-button"
                        on:click={() => {
                            navigator.clipboard.writeText(
                                JSON.stringify(track)
                            );
                        }}>Copy Details</a
                    >
                </div>
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

    .loader-container {
        position: absolute;
        display: flex;
        flex-direction: column;
        align-items: center;
        left: calc(50% - 16px);
        top: calc(50% - 16px);
    }

    .track {
        box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
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

    .track:hover {
        box-shadow: 0 14px 28px rgba(0, 0, 0, 0.25),
            0 10px 10px rgba(0, 0, 0, 0.22);
    }

    .album-art {
        border-radius: 4px;
        height: 420px;
        width: 420px;
        object-fit: cover;
    }

    .metadata {
        margin: 18px 24px 18px 24px;
        width: max-content;
        display: flex;
        justify-content: space-between;
        flex-direction: column;
    }

    .button-bar {
        display: flex;
        flex-wrap: wrap;
        flex-direction: row;
    }

    .track-button {
        padding-left: 12px;
        padding-right: 12px;
        padding-top: 8px;
        padding-bottom: 8px;
        font-size: 15px;
        border-radius: 4px;
        text-decoration: none;
        background-color: var(--accent-color);
        box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
        color: #fff;
        cursor: pointer;
        transition-duration: 200ms;
        margin-top: 24px;
    }

    .track-button:hover {
        background-color: #fff;
        color: #000;
    }

    @media only screen and (max-width: 640px) {
        .scaffold {
            overflow-y: scroll;
            display: block;
        }

        .track {
            margin: 24px;
            width: calc(100% - 48px);
            height: auto;
            flex-direction: column;
        }

        .album-art {
            width: 100%;
            height: calc(100vw - 48px);
        }
    }
</style>
