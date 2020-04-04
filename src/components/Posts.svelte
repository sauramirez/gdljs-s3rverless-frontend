<script>
import * as k1 from '../key1';
import * as k2 from '../key2';
import * as k3 from '../key3';

import * as Pmap from 'p-map';

let posts = [];
const staticUrl = 'https://demo-simplebytes-dev.s3-us-west-2.amazonaws.com/';

let s3 = new AWS.S3({
    accessKeyId: 'AKIAQODYIGLEQZM4YH72',
    secretAccessKey: k1.k + k3.k + k2.k + 'g',
    region: 'us-west-2',
    s3: '2006-03-01',
});

setInterval(() => {
console.log('running timeout');
    const params = {
    Bucket: 'demo-simplebytes-dev',
    MaxKeys: 200,
    Prefix: 'outputs',
    }
    s3.listObjectsV2(params, async (err, data) => {
        if (err) {
        console.error(err);
        return;
        }
        console.log(data);
        const _posts = [];
        await Pmap(data.Contents, async (content) => {
            const splitted = content.Key.split('_');
            const type = splitted[1];
            if (type === '1') {
                _posts.unshift({
                type: 'image',
                url: `${staticUrl}${content.Key}`,
                modifiedAt: content.LastModified,
                key: content.Key
                });
            }
            else if (type === '0') {
             const data = await s3.getObject({
                Bucket: 'demo-simplebytes-dev',
                Key: content.Key
            }).promise();
            console.log(JSON.parse(data.Body));
                _posts.unshift({
                type: 'text',
                content: JSON.parse(data.Body),
                key: content.Key,
                modifiedAt: content.LastModified
                });
            }
        })

        posts = _posts.sort((a, b) => {
            return a.modifiedAt < b.modifiedAt;
        });
    });
}, 3000);
</script>

<div>
<h1>Posts</h1>
{#each posts as post (post.key)}
<div class="w-80 w-50-ns center pa2">
{#if post.type === 'image'}
<img src="{post.url}" alt="">
{:else}
<h2>{post.content.title}</h2>
<p>{post.content.content}</p>
{/if}
</div>
{/each}
</div>
