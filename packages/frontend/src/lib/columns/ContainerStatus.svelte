<script lang="ts">
import { ContainerIcon } from '@podman-desktop/ui-svelte/icons';
import { StatusIcon } from '@podman-desktop/ui-svelte';
import type { LocalContainer } from '@podman-desktop/extension-hummingbird-core-api/src';

interface Props {
  object: LocalContainer;
}

let { object: container }: Props = $props();
</script>

<StatusIcon icon={ContainerIcon} status={container.state.toUpperCase()} />
