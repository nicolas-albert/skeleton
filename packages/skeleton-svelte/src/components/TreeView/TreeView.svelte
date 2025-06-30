<script>
	import { normalizeProps, useMachine } from '@zag-js/svelte';
	import * as tree from '@zag-js/tree-view';
	import TreeNode from './TreeNode.svelte';

	// type Node = {
	//   id: string
	//   name: string
	//   children?: Node[]
	// }

	// export let nodes: Node[] = []
	// let { nodes } = $props();

	const {
		base = 'w-full',
		classes = '',
		label = 'Tree View',
		labelBase = 'text-lg font-semibold mb-2',
		treeBase = '',
		rootNode = {
			id: 'root',
			name: 'Root',
			children: []
		},
		...zagProps
	} = $props();

	let collection = $state(
		tree.collection({
			nodeToValue: (node) => node.id,
			nodeToString: (node) => node.name,
			rootNode
		})
	);

	function onLoadChildrenComplete({ collection: c }) {
		console.log('Load children complete', c);
		collection = c;
	}

	const id = $props.id();
	const service = useMachine(tree.machine, () => ({
		id,
		collection,
		onLoadChildrenComplete,
		...zagProps
	}));
	const api = $derived(tree.connect(service, normalizeProps));
</script>

<div {...api.getRootProps()} class="{base} {classes}" data-testid="tree-view">
	<h3 {...api.getLabelProps()} class={labelBase}>{label}</h3>
	<div {...api.getTreeProps()} class={treeBase}>
		{#each collection.rootNode.children as node, index}
			<TreeNode {node} {api} indexPath={[index]} />
		{/each}
	</div>
</div>
