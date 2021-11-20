<script>
    import { onMount } from "svelte";
    import { fly, fade } from "svelte/transition";
    import Loader from "./Loader.svelte";

    let label = (duration) => {
        return `${Math.round(duration / 60)}:${duration % 60 < 10 ? "0" : ""}${
            duration % 60
        }`;
    };

    let id = (link) => {
        if (link.includes("youtu.be")) {
            if (link.at(-1) === "/") {
                return link.split("/").at(-2);
            }
            return link.split("/").at(-1);
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
        return link.split("/").at(-1);
    };

    let track;
    onMount(async () => {
        if (location.href.includes("+")) {
            let response = await fetch(
                `https://x1yb80pwsn4.herokuapp.com/track_info?track_id=${location.href
                    .split("/")
                    .at(-1)
                    .split("+")
                    .at(0)}&album_id=${location.href
                    .split("/")
                    .at(-1)
                    .split("+")
                    .at(-1)}`
            );
            track = await response.json();
        } else {
            let response = await fetch(
                `https://x1yb80pwsn4.herokuapp.com/track_info?track_id=${id(
                    location.href
                )}`
            );
            track = await response.json();
        }
    });
</script>

<div class="scaffold">
    {#if track === undefined}
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
                <div class="data">
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
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <a
                        class="track-button"
                        on:click={() => {
                            window.open(
                                `https://x1yb80pwsn4.herokuapp.com/download/${track["track_file_name"]}.ogg`,
                                "__blank"
                            );
                        }}>Download</a
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
                    <div style="width: 12px;" />
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <a
                        class="track-button"
                        on:click={() =>
                            navigator.share({
                                title: track["track_name"],
                                url: location.href,
                            })}>Share</a
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
        box-shadow: 0 10px 20px rgba(0, 0, 0, 0.25),
            0 6px 8px rgba(0, 0, 0, 0.22);
    }

    .album-art {
        border-radius: 4px;
        height: 420px;
        width: 420px;
        object-fit: cover;
    }

    .metadata {
        margin: 18px 24px 18px 24px;
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        flex-direction: column;
    }

    .data {
        height: 100%;
        width: 100%;
        position: relative;
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

    .track-button:active {
        transform: scale(0.95);
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

        .metadata {
            width: calc(100% - 48px);
        }

        .album-art {
            width: 100%;
            height: calc(100vw - 48px);
        }
    }
</style>
