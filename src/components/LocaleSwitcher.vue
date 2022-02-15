<template>
    <div class="lang-select">
        <p>Select language: </p>
    <select v-model="$i18n.locale">
        <option v-for="locale, i in locales" :key="'locale'+i">
            {{locale}}
        </option>
    </select>
    </div>
</template>

<script>
export default {
    name: 'LocaleSwitcher',
    data() {
        return {
            locales: process.env.VUE_APP_I18N_AVAILABLE_LOCALES.split(',')
        }
    },
}
</script>