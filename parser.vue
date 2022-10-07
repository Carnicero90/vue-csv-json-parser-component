<template>
    <div>
        <input type="file" accept="application/json,.csv" @change="loadContent">
        <p>Manca ancora
            gestione csv formattati strani, con virgole nei valori, con a capo di troppo, etc. <br><strong>necessario
                che i csv abbiano gli
                header</strong> (sennò come li mappi?)</p>
    </div>
</template>

<script>
export default {
    data() {
        return {
            floa: null,
            is_csv: false,
            parsedContent: null,
        }
    },
    methods: {
        loadContent(e) {
            this.floa = e.target.files[0];
            const reader = new FileReader();
            reader.onload = (e) => this.outputContent(e);
            reader.readAsText(this.floa);
            this.is_csv = this.floa.name.endsWith('.csv');
        },
        outputContent(e) {
            const content = e.target.result;
            this.parsedContent = this.is_csv ? this.parseCSV(content) : JSON.parse(content);
            this.$emit('contentParsed', this.parsedContent);
        },
        parseCSV(target) {
            const content = target.split('\n').filter((i) => i);
            const headers = content.shift().split(',');
            return content.reduce((acc, i) => {
                return [...acc, Object.assign(...i.split(',').map((k, i) => ({ [headers[i]]: k })))];
            }, []);
        }
    }
}
</script>

<style scoped>

</style>
