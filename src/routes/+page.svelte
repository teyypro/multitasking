<script>
	import { onMount, onDestroy } from 'svelte';
	import { writable } from 'svelte/store';
	import { browser } from '$app/environment';
	import 'golden-layout/dist/css/goldenlayout-base.css';
	import 'golden-layout/dist/css/themes/goldenlayout-light-theme.css';
	import Bar from './Bar.svelte';

	// Store để quản lý nội dung các editor
	let editors = writable([]);
	let layout;
	let editorCount = 0;
	let layoutContainer;

	// Component Contenteditable
	function createEditorComponent() {
		return {
			componentName: 'Editor',
			component: {
				create: (container, state) => {
					const editorDiv = document.createElement('div');
					editorDiv.className = 'editor-container';
					editorDiv.innerHTML = `
						<div spellcheck = 'false' class="contenteditable" contenteditable="true">${state.content || ''}</div>
					`;

					// Lưu nội dung khi chỉnh sửa
					const contenteditable = editorDiv.querySelector('.contenteditable');
					contenteditable.addEventListener('input', () => {
						editors.update(current => {
							current[state.id] = contenteditable.innerHTML;
							return current;
						});
					});

					container.element.appendChild(editorDiv);
					container.on('destroy', () => {
						editors.update(current => {
							current[state.id] = undefined;
							return current;
						});
					});
				}
			}
		};
	}

	// Khởi tạo layout
	onMount(async () => {
		if (!browser || !layoutContainer) return; // Chỉ chạy trên client

		try {
			// Dynamic import để load GoldenLayout
			const goldenLayoutModule = await import('golden-layout');
			// Kiểm tra cấu trúc module
			const GoldenLayout = goldenLayoutModule.default?.GoldenLayout || goldenLayoutModule.GoldenLayout || goldenLayoutModule.default;

			if (!GoldenLayout || typeof GoldenLayout !== 'function') {
				console.error('GoldenLayout constructor not found:', goldenLayoutModule);
				throw new Error('GoldenLayout is not a constructor');
			}

			layout = new GoldenLayout({
				content: [{
					type: 'row',
					content: []
				}]
			}, layoutContainer);

			// Đăng ký component
			layout.registerComponentFactoryFunction('Editor', (container, state) => {
				createEditorComponent().component.create(container, state);
			});

			layout.init();

			// Thêm editor đầu tiên
			addNewEditor();
		} catch (error) {
			console.error('Failed to initialize GoldenLayout:', error);
		}

		return () => {
			if (layout) layout.destroy();
		};
	});

	// Hàm thêm editor mới
	function addNewEditor() {
		const id = editorCount++;
		editors.update(current => {
			current[id] = '';
			return current;
		});

		if (layout) {
			layout.addComponent('Editor', { id, content: '' }, `Draft ${id + 1}`);
		}
	}

	// Xử lý sự kiện từ Bar component
	function handleAddEditor() {
		addNewEditor();
	}
</script>
<div id="containerall">
	<Bar on:openAddModal={handleAddEditor} />
<div class="layout-container" bind:this={layoutContainer}></div>
</div>


<style>
	#containerall{
		position: relative;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex
;
    flex-direction: column;
	}
	:global(.layout-container) {
		height: 96vh;
    width: 100%;
    position: relative;
	}
	:global(.editor-container) {
		padding: 10px;
    height: 100%;
    background-color: #ffffff;
	}
	:global(.contenteditable) {
		padding: 10px;
    height: 100%;
    background-color: #ffffff;
	overflow:auto;
	}

	:global(.button-bar) {
		display: flex;
		gap: 10px;
	}
	:global(.lm_content) {
		overflow: auto !important;
	}

	:global(.lm_tab:hover, .lm_tab.lm_active) {
    background: #000000 !important;
    color: #ffffff !important;
    font-weight: bold !important;
    font-family: monospace !important;
}
:global(.lm_tab:hover){
background: #979797 !important;
    color: #ffffff !important;
    font-weight: bold !important;
    font-family: monospace !important;
}

:global(.lm_close_tab){
	opacity: 1;
    background-color: #3c3c3c;
    border-radius: 50%;
	top: 3px !important;
    right: 6px !important;
	width: 10px !important;
    height: 10px !important;
}

:global(.lm_header .lm_tab) {
    font-family: monospace;
    font-size: 12px;
    color: #979797;
    background: #ffffff;
    margin-right: 2px;
    padding-bottom: 4px;
	border:0;
}
</style>