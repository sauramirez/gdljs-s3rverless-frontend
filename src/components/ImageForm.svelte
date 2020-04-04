<script>

import { v4 as uuidv4 } from 'uuid';
import * as k1 from '../key1';
import * as k2 from '../key2';
import * as k3 from '../key3';

import * as Pmap from 'p-map';

let hovering = false;
let files = [];

let s3 = new AWS.S3({
    accessKeyId: 'AKIAQODYIGLEQZM4YH72',
    secretAccessKey: k1.k + k3.k + k2.k + 'g',
    region: 'us-west-2',
    s3: '2006-03-01',
});

function onDragStart(e) {
    e.preventDefault();
    hovering = true;
}

function onDrop(e) {

    e.preventDefault();
    hovering = false;
    if (e.dataTransfer.items) {
        // Use DataTransferItemList interface to access the file(s)
        for (var i = 0; i < e.dataTransfer.items.length; i++) {
            // If dropped items aren't files, reject them
            if (e.dataTransfer.items[i].kind === 'file') {
                var file = e.dataTransfer.items[i].getAsFile();
                files.push(file);
            }
        }
    }
    return false;
}

function onDragEnd(e) {
    e.preventDefault();
    hovering = false;
}

function onChange(e) {
    e.preventDefault();
}

function onSubmit(e) {
    e.preventDefault();
    Pmap(files, async (file) => {
        const extname = file.name.split('.').pop();
        const filename = `inputs/${uuidv4()}.${extname  || ''}`;
        s3.upload({
            Bucket: 'demo-simplebytes-dev',
            Key: filename,
            Body: file,
            ContentType: file.type
        }, (err) => {
        if (err) {
            console.error(err);
        }
        });
    })
    .then(() => {
    files = [];
    })
    .catch((err) => {
    files = [];
    console.error(err);
    });
}

</script>

<style>
.dnd-image {
    min-height: 250px;
    min-width: 200px;
}

.hovering {
  border-color: green;
}

input[type="file"] {
max-width: 100%;
}
</style>

<form action="#" on:submit={onSubmit} class="pa2">
<div class="dnd-image ba b-dotted w-50 center pa2" class:hovering={hovering} on:dragover={onDragStart} on:drop={onDrop} on:dragend={onDragEnd} on:dragleave={onDragEnd} on:dragexit={onDragEnd}>
    Drag & drop image here

<div class="mt2">
    <input type="file" bind:files on:change={onChange} />
</div>
    {#if files && files[0]}
    <p>
        {files[0].name}
    </p>
    {/if}
</div>
    <div class="mt2">
        <button>Submit</button>
    </div>
</form>
