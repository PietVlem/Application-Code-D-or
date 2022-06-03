<template>

    <div>
        <div hidden>
            <div data-name="popover-content">
                <div class="input-group">
                    <input type="text" class="form-control form-control-sm" placeholder="Search" name="search">
                    <div class="input-group-btn">
                        <button class="btn btn-danger" type="submit">
                            <i class="bi bi-search fa fa-search"></i>
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <a id="example" tabindex="0" class="btn btn-lg btn-danger" role="button" data-bs-toggle="popover"
            title="Default: Html inside popover">Html inside popover</a>
    </div>

</template>

<script>
import { Popover } from 'bootstrap'

export default {
    name: 'Popover',
    mounted() {
        var options = {
            html: true,
            title: "Optional: HELLO(Will overide the default-the inline title)",
            //html element
            //content: $("#popover-content")
            content: document.querySelector('[data-name="popover-content"]')
            //Doing below won't work. Shows title only
            //content: $("#popover-content").html()

        }
        var exampleEl = document.getElementById('example')
        var popover = new Popover(exampleEl, options)
    }
}
</script>