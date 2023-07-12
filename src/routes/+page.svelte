<script>
    import {onMount} from 'svelte';
    import dayjs, {Dayjs} from 'dayjs';
    import 'dayjs/locale/en';
    import Button from './Button.svelte';
    import Input from './Input.svelte';
    import Image from './Image.svelte';

    let inputText = '';
    let imageUrl = '';
    let imageAlt = '';

    let title;
    let notification;
    let date;
    let datePassed;


    async function getId() {
        try {
            const response = await fetch(`https://fwd.innopolis.university/api/hw2?email=${inputText}`);
            return await response.json();
        } catch (error) {
            notification.classList.remove("hidden");
            setTimeout(() => {
                notification.classList.add("hidden");
            }, 2000);
            throw error;
        }
    }

    async function getComic() {
        try {
            const id = await getId();
            const response = await fetch(`https://fwd.innopolis.university/api/comic?id=${id}`);
            const data = await response.json();
            imageUrl = data["img"];
            imageAlt = data["alt"];
            Image.src = data["img"];
            title.textContent = data["safe_title"];
            Image.alt = data["alt"];
            const currentDate = dayjs();
            const dateComic = dayjs([data["year"], data["month"], data["day"]]).locale('en').format('dddd, MMMM DD, YYYY');
            const timePassed = currentDate.diff(dateComic, 'millisecond');
            datePassed.textContent = timePassed
            date.textContent = dateComic;
        } catch (error) {
            console.error(error);
        }
    }


    onMount(() => {
        const button = document.querySelector("button");
        if (button !== null) {
            button.onclick = getComic;
        }
    });
</script>

<svelte:head>
    <title>Home</title>
    <meta name="description" content="Svelte demo app"/>
</svelte:head>

<section>

    <Input bind:bindValue={inputText}/>
    <Button on:click={async () => await getComic()}/>
    <Image src={imageUrl} alt={imageAlt}/>
    <h1 bind:this={title} id="title"></h1>
    <div bind:this={notification} id="notification" class="hidden">Error occurred.</div>
    <p bind:this={date} id="date"></p>
    <p bind:this={datePassed} id="datePassed"></p>
</section>

<style>
    section {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        flex: 0.6;
    }

    h1 {
        width: 100%;
    }

    .hidden {
        visibility: hidden;
    }

</style>
