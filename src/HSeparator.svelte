<script lang="ts">
    import { onMount } from 'svelte';
    let separator;
    export let left;
    export let right;
    export let updateCallback = () => {
        // do nothing
        return;
    }

    let md;
    let originalLeftWidth, originalRightWidth;
    let originalSeparatorLeft;
    const onMouseMove = (e) => {
        e.preventDefault();
        var delta = {x: e.clientX - md.e.clientX,
                y: e.clientY - md.e.clientY};
        // Prevent negative-sized elements
        delta.x = Math.min(Math.max(delta.x, -md.firstWidth),
                    md.secondWidth);

        separator.style.left = md.offsetLeft + delta.x + "px";
        left.style.width = (md.firstWidth + delta.x) + "px";
        right.style.width = (md.secondWidth - delta.x) + "px";
    }
    const onMouseUp = (e) => {
        e.preventDefault();
        updateCallback();
        window.removeEventListener('mousemove', onMouseMove);
        window.removeEventListener('mouseup', onMouseUp);
    }
    onMount(() => {
        window.addEventListener('resize', () => {
            if (left && left.style && right && right.style) {
                left.style.width = originalLeftWidth;
                right.style.width = originalRightWidth;
                separator.style.left = originalSeparatorLeft;
            }
        });
    });

    export let marginTop = 0;
</script>

<style>
    div.separator {
        cursor: col-resize;
        height: 100%;
        width: 5px;
        position: fixed;
        top: 0px;
        right: 311px;
        background-color: #aaa;
        background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='30'><path d='M2 0 v30 M5 0 v30 M8 0 v30' fill='none' stroke='black'/></svg>");
        background-repeat: no-repeat;
        background-position: center;
    }
</style>

<div bind:this={separator} class="separator" style="margin-top:{marginTop}px"
on:mousedown={(e) => {
    if (!originalLeftWidth && !originalRightWidth) {
        originalLeftWidth = left.style.width;
        originalRightWidth = right.style.width;
        originalSeparatorLeft = separator.style.left;
    }
    md = {e,
        offsetLeft:  separator.offsetLeft,
        offsetTop:   separator.offsetTop,
        firstWidth:  left.offsetWidth,
        secondWidth: right.offsetWidth
    };
    window.addEventListener('mousemove', onMouseMove);
    window.addEventListener('mouseup', onMouseUp);
}}
>
</div>
