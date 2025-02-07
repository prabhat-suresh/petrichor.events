<script lang="ts">
	import type { event } from "$lib/types";
	import { onMount } from "svelte";

    export let data: {[key: string]: event[]};
    let events = data['events'];

    let bg: HTMLDivElement
    let currentEvent: event = events[0];
    onMount(() => {
        bg.style.backgroundImage = `url("${currentEvent.image}")`;
    })

</script>

<div class="bg" bind:this={bg}></div>
<div class="parent">
    <div class="sidebar">
        {#each events as event}   
        <!-- svelte-ignore a11y-no-static-element-interactions -->
        <div class="card" style="background-image: url('{event.image}');" on:mouseenter={() => {
            bg.style.backgroundImage = `url("${event.image}")`;
            currentEvent = event;
            console.log(event.image)
        }}>
            <p>
                {event.name}
            </p>
        </div>
        {/each}
    </div>
    <div class="content">
        <div class="banner">
            <h1>{currentEvent && currentEvent.name}</h1>
            <span>{currentEvent.date}</span>
            <p>{currentEvent && currentEvent.about}</p>
            <div class="buttons">
                <a href="#rules" class="a-unset register">LEARN MORE</a>
                <a href="#register" class="a-unset register">REGISTER NOW</a>
            </div>
        </div>
        <div class="rulebook" id="rules">
            <div class="structure">
                {#each Object.keys(currentEvent.rulebook.structure) as struct}
                    <h2>{struct}</h2>
                    {#each currentEvent.rulebook.structure[struct] as point}
                        <li>{point}</li>
                    {/each}
                {/each}
            </div>
            <div class="rules">
                <h2>Rules</h2>
                {#each currentEvent.rulebook.rules as struct}
                    <li>{struct}</li>
                {/each}
            </div>
            <div class="judging">
                <h2>Judging</h2>
                {#each currentEvent.rulebook.judging as struct}
                    <li>{struct}</li>
                {/each}
            </div>
            <div class="prizes">
                <h2>Prizes</h2>
                {#each currentEvent.rulebook.prizes as struct}
                    <li>{struct}</li>
                {/each}
            </div>
        </div>
        <div id="register">
            <h2>Organisers</h2>
            <p>[insert organiser names here]</p>
            <button class="register">Register for {currentEvent.name}</button>
        </div>
    </div>
</div>


<style>
    .card{
        width: 90%;
        aspect-ratio: 2;
        background-position: center;
        background-size: cover;
        position: relative;
        cursor: pointer;
        margin: 1em;
        border-radius: 0.4em;
        transition: 200ms ease-in-out;
        overflow: hidden;
    }
    .card::before{
        background: rgb(0,0,0);
        background: -moz-linear-gradient(0deg, rgba(0,0,0,1) 0%, rgba(0,0,0,0) 100%);
        background: -webkit-linear-gradient(0deg, rgba(0,0,0,1) 0%, rgba(0,0,0,0) 100%);
        background: linear-gradient(0deg, rgba(0,0,0,1) 0%, rgba(0,0,0,0) 100%);
        content: "";
        position: absolute;
        height: 100%;
        width: 100%;
        transition: 100ms;
    }
    .card:hover{
        transform: scale(1.08) translateX(0px);
        z-index: 100000;
    }
    .card > p{
        position: absolute;
        font-size: 20px;
        bottom: 0;
        left: 1em;
    }
    .register{
        padding: 0.8em;
        padding-inline: 1em;
        background-color: rgba(237, 237, 237, 0.137);
        border-radius: 0.4em;
        border: unset;
        color:white;
        font-size: 20px;
        transition: 200ms ease-in-out;
    }
    .register:hover{
        background-color: rgb(255, 255, 255);
        color: black
    }
    .bg{
        position: fixed;
        top: 0;
        left: 0;
        height: 100vh;
        width: 100vw;
        background-position: center;
        background-size: cover;
        transition: background-image 2s;
        filter: blur(5px)
    }
    .sidebar{
        overflow-y: scroll;
        height: 100vh;
        width: 25%;
        background-color: #28282d98;
        backdrop-filter: blur(40px);
    }
    .parent{
        display: flex;
        width: 100%;
        /* font-size: 20px; */
    }
    .content{
        z-index: 1;
        padding-top: 5.5em;
        padding-left: 1em;
        position: relative;
        width: 100%;
        display: flex;
        flex-direction: column;
        place-items: center;
        height: 90vh;
        overflow-y: scroll;
    }
    .buttons{
        margin-top: 2rem;
        margin-bottom: 2rem;
    }
    .buttons > a{
        margin-left: 0.75em;
        margin-right: 0.75em;
    }
    .banner > h1{
        font-size: 9vw;
        /* position: absolute;   */
        right: 5vw;
        height: 13rem;
        margin-bottom: 1.33rem;
        margin-top: 1.33rem;
    }
    .banner > p{
        margin-top: 2em;
        font-size: 23px;
        width: 90%;
    }
    .banner > span{
        font-size: 20px;
    }
    .rulebook{
        margin-top: 2rem;
        width: 85%;
        font-size: 22px;
    }

    @keyframes banneranim{
        0%{
            background-position: 0 0;
        }
        50%{
            background-position: 100% 100%;
        }
        100%{
            background-position: 0 0;
        }
    }
    .banner{
        display: flex;
        flex-direction: column;
        width: 85%;
        place-items: center;
        background-color: rgba(0, 0, 0, 0.459);
        /*
* Created with https://www.css-gradient.com
* Gradient link: https://www.css-gradient.com/?c1=ab84d1&c2=1422c1&gt=l&gd=dtl
*/
        background: #ab84d14b;
        background: linear-gradient(135deg, #ab84d134, #1422c139);
        backdrop-filter: blur(100px);
        background-size: 150% 150%;
        padding: 1rem;
        border-radius: 12px;
        margin-top: 1em;
        animation: banneranim 5s linear infinite;
    }
    .cool {
		all: unset;
		margin-right: 1em;
		width: 100px;
		height: 30px;
		font-size: 16px;
		background: transparent;
		border: none;
		position: relative;
		color: #f0f0f0;
		cursor: pointer;
		z-index: 1;
		padding: 10px 20px;
		display: flex;
		align-items: center;
		justify-content: center;
		white-space: nowrap;
		user-select: none;
		-webkit-user-select: none;
		touch-action: manipulation;
	}

	.cool::after,
	.cool::before {
		content: '';
		position: absolute;
		bottom: 0;
		right: 0;
		z-index: -99999;
		transition: all 0.4s;
	}

	.cool::before {
		transform: translate(0%, 0%);
		width: 100%;
		height: 100%;
		background: #28282d;
		border-radius: 10px;
	}

	.cool::after {
		transform: translate(10px, 10px);
		width: 35px;
		height: 35px;
		background: #ffffff15;
		backdrop-filter: blur(5px);
		-webkit-backdrop-filter: blur(5px);
		border-radius: 50px;
	}

	.cool:hover::before {
		transform: translate(5%, 20%);
		width: 110%;
		height: 110%;
	}

	.cool:hover::after {
		border-radius: 10px;
		transform: translate(0, 0);
		width: 100%;
		height: 100%;
	}

	.cool:active::after {
		transition: 0s;
		transform: translate(0, 5%);
	}
</style>