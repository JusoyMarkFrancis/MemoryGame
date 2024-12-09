<script>
    import "../app.css";
</script>

<svelte:head>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@100;400;700&display=swap" rel="stylesheet">
</svelte:head>

<main class="main-content">
    <slot></slot>
</main>
<style>
    :global(body) {
        font-family: 'Poppins', sans-serif;
        margin: 0;
        padding: 0;
        background: linear-gradient(135deg, #0f1c38, #22417e, #137cc2); /* Gradient background */
    }

    /* Main content styles */
    .main-content {
        padding: 2rem; 
    }

    /* Responsive styles */
    @media (max-width: 480px) {
        .main-content {
            padding: 1rem; 
        }
    }
</style>
