<script>
	import TreeNode from './TreeNode.svelte';

	let {
		node,
		indexPath,
		api,
		nodeIcon = ({ hasChildren, isOpen }) => (hasChildren ? (isOpen ? '◈' : '▢') : '─'),
		controlClass = 'flex items-center gap-2 cursor-pointer',
		textClass = 'text-sm',
		indicatorClass = 'ml-auto text-muted',
		childrenClass = 'pl-4'
	} = $props();

	let nodeState = $derived(api.getNodeState({ node, indexPath }));
</script>

{#if nodeState.isBranch}
	<div {...api.getBranchProps({ node, indexPath })}>
		<div {...api.getBranchControlProps({ node, indexPath })} class={controlClass}>
			<span>
				{nodeIcon({
					node,
					hasChildren: true,
					isOpen: nodeState.isOpen
				})}
			</span>
			<span {...api.getBranchTextProps({ node, indexPath })} class={textClass}>
				{node.name}
			</span>
			<span {...api.getBranchIndicatorProps({ node, indexPath })} class={indicatorClass}></span>
		</div>
		<div {...api.getBranchContentProps({ node, indexPath })} class={childrenClass}>
			<div {...api.getBranchIndentGuideProps({ node, indexPath })}></div>
			{#each node.children as child, index}
				<TreeNode node={child} {api} indexPath={[...indexPath, index]} />
			{/each}
		</div>
	</div>
{:else}
	<div {...api.getItemProps({ node, indexPath })} class={controlClass + ' pl-6'}>
		<span>
			{nodeIcon({
				node,
				hasChildren: false,
				isOpen: false
			})}
		</span>
		<span class={textClass}>{node.name}</span>
	</div>
{/if}
