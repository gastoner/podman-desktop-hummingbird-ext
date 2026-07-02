<script lang="ts">
// app.css includes tailwind css dependencies that we use
import '/@/app.css';
import '@fortawesome/fontawesome-free/css/all.min.css';
import { onMount } from 'svelte';
import { getRouterState, routingAPI, rpcBrowser } from '/@/api/client';
import { afterNavigate, goto } from '$app/navigation';
import { page } from '$app/state';
import { Messages } from '@podman-desktop/extension-hummingbird-core-api';
import Navigation from '$lib/navigations/Navigation.svelte';

let { children } = $props();

function getPageName(): string {
  return (page.data as { pageName?: string }).pageName ?? 'Hummingbird';
}

let isExternalNavigation = false;

onMount(() => {
  getRouterState().then(state => {
    if (state.url !== '/' && state.url !== page.url.pathname) {
      isExternalNavigation = true;
      // eslint-disable-next-line svelte/no-navigation-without-resolve
      goto(state.url).catch(console.error);
    }
  });

  const unsubscribe = rpcBrowser.subscribe(Messages.ROUTE_UPDATE, location => {
    isExternalNavigation = true;
    // eslint-disable-next-line svelte/no-navigation-without-resolve
    goto(location).catch(console.error);
  }).unsubscribe;

  return (): void => {
    unsubscribe();
  };
});

afterNavigate(({ to }) => {
  if (to?.url) {
    if (isExternalNavigation) {
      isExternalNavigation = false;
      return;
    }
    const name = getPageName();
    routingAPI.reportNavigation(to.url.pathname, name).catch(console.error);
  }
});
</script>

<div class="flex flex-row w-full h-full">
  <Navigation />
  {@render children()}
</div>
