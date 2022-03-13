<script context="module">
    const query = `
    query MyQuery {
        posts {
            nodes {
            postId
            title
            content
            featuredImage {
                node {
                mediaItemUrl
                }
            }
            uri
            }
        }
        }


    `;
    export async function load( { fetch } ) {
        const response = await fetch (import.meta.env.VITE_PUBLIC_WORDPRESS_API_URL, {
            method: 'POST',
            headers: {
                'Content-type': 'application/json',
            },
            body: JSON.stringify( {query} ),
        });
        if(response.ok) {     
            const responseObj = await response.json();            
            const posts = responseObj.data.posts.nodes;            
            return {
                props: {
                    posts
                }
            };
        }
        return{
                status: response.status,
                error: new Error (`Не получилось загрузиь данные из ${response.url}`),
            }
    }
</script>

<script>
    export let posts;

</script>

<h1>Посты: </h1>

<div>
    {#each posts as post}
        <a href={`/posts${post.uri}/`}>
            <h2>{post.title}</h2>
        </a>
        {#if post.featureImage}
            <img src={`${post.featureImage.node.mediaItemUrl}`} alt/>
        {/if}
        <p>{@html post.content}</p>
    {/each}
</div>