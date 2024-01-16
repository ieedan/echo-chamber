<script lang="ts">
	import { Select, Option, Header, Text } from "geist-ui-svelte";
    import "geist-ui-svelte/styles/geist-ui-svelte.css";
    import { changePreference, ColorPreference, getCurrentPreference } from "$lib/dark-mode";
	import { onMount } from "svelte";

    let preference: ColorPreference; 

    const setPreference = (pref: ColorPreference | undefined) => {
        if (!pref) return;
        changePreference(pref);
    };

    onMount(() => {
        preference = getCurrentPreference();
    })
</script>

<main>
    <Header sticky>
        <div class="flex justify-between w-full px-6 max-w-6xl">
            <Text type="h2" size="2xl">Echo-Chamber</Text>
            <Select bind:value={preference} on:change={() => setPreference(preference)} width="150px">
                <Option value={ColorPreference.dark}>Dark</Option>
                <Option value={ColorPreference.light}>Light</Option>
                <Option value={ColorPreference.OS}>System</Option>
            </Select>
        </div>
    </Header>
    <slot/>
</main>