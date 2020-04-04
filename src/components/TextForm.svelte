<script>
import { v4 as uuidv4 } from 'uuid';
import * as k1 from '../key1';
import * as k2 from '../key2';
import * as k3 from '../key3';

let s3 = new AWS.S3({
    accessKeyId: 'AKIAQODYIGLEQZM4YH72',
    secretAccessKey: k1.k + k3.k + k2.k + 'g',
    region: 'us-west-2',
    s3: '2006-03-01',
});

let title = '';
let content = '';

function onSubmit(e) {
    e.preventDefault();
    const file = new File([JSON.stringify({title, content})], "text.json", {type:"text/plain"})
        s3.upload({
            Bucket: 'demo-simplebytes-dev',
            Key: `inputs/${uuidv4()}.json`,
            Body: file,
            ContentType: 'application/javascript'
        }, (err) => {
        title = '';
        content = '';
        });
}
</script>

<style>
textarea {
   /* will prevent resizing horizontally */
   max-width:90%;
   resize:vertical;
}
</style>

<form action="#" on:submit={onSubmit} class="pa2">
<label for="text-post">Title:</label>
<input name="text-post" bind:value={title} />
<label for="text-post">Text:</label>
<textarea name="text-post" cols="28" rows="10" bind:value={content}></textarea>
<div class="mt2">
<button>Submit</button>
</div>
</form>
