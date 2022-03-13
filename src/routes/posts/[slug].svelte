<script context="module">
    const query = `
    query MyQuery ($slug: ID!) {
        post(id: $slug, idType: SLUG) {
            content
            date
            title
            featuredImage {
            node {
                mediaItemUrl
            }
            }
        }
        }
    `;

    export async function load ( { url, params, fetch } ){
        console.log(params);
    
    const response = await fetch ( import.meta.env.VITE_PUBLIC_WORDPRESS_API_URL, {
        method: 'POST',
        headers: {
            'Content-type': 'application/json',
        },
        body: JSON.stringify({
            query,
            variables: {
                slug: params.slug
            }
        }),
    });

    if(response.ok){
        const responseObj = await response.json();
        const post = responseObj.data.post;
        return {
            props: {
                post
            }
        };
        return {
            status: response.status,
            error: new Error(`Запрос на адрес ${response.url} не доступен`)
        };
    }
    }
</script>

<script>
    export let post;
</script>

<h1>{post.title}</h1>
<i>{post.date}</i>
{#if post.featuredImage}
    <img src={`${post.featuredImage.node.mediaItemUrl}`} alt />
{/if}
<p>
    {@html post.content}
</p>

<style>
    img{
        max-width: 500px;
    }
</style>