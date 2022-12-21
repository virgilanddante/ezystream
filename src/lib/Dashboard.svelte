<script>
    import { writable } from 'svelte/store';
    import * as Shows from '../lib/shows.json';

    let showIDs = [];
    let episodeArray = [];

    let episodesList = writable([]);
    let currentEpisode = writable("");

    let showVid = false;

    for(let i=0; i<Shows.shows.length; i++) {
        showIDs.push(Shows.shows[i].id);
    }

    /* Function to change whether the cover art or the video player should be seen */
    function viewController() {
        if (!showVid) {
            for(let i=0; i<Shows.shows.length; i++) {
                if(this.id === Shows.shows[i].id) {
                    for(let j=0; j<Shows.shows[i].episodes.length; j++) {
                        episodeArray.push(Shows.shows[i].episodes[j].episode_name);
                    }
                }
            }
            episodesList.set(episodeArray);
            currentEpisode.set(episodeArray[0]);
            showVid = true;
        } else {
            let video = document.getElementById('player');
            video.pause();
            video.currentTime = 0;
            episodesList.set([]);
            episodeArray = [];
            showVid = false;
        }
    }

    /* Function to change the episode */
    function episodeController() {
        currentEpisode.set(this.id);
    }
</script>

<div class="container text-center" hidden={showVid}>
    <div class="row">
        {#each showIDs as show}
            <div class="col">
                <div class="card">
                    <img src="covers/{show}.jpg" id={show} alt="cover" on:click={viewController} />
                </div>
            </div>
        {/each}
    </div>
</div>
<div class="vid-player" hidden={!showVid}>
    <video id="player" width="80%" height="60%" controls src="video/{$currentEpisode}.mp4" style="display:block; margin: 0 auto;" />
    <div>
        <div id="side-bar" class="d-flex flex-column flex-shrink-0 p-3 text-bg-dark">
            <span class="fs-4">Episodes</span>
            <hr>
            <ul class="nav nav-pills flex-column mb-auto">
                {#each $episodesList as ep}
                <button style="background-color: #222222;"><li id={ep} style="color: white;" on:click={episodeController}>{ep}</li></button>
                {/each}
            </ul>
        </div>
        <button class="close-button" type="button" on:click={viewController}>X Close</button>
    </div>
</div>
