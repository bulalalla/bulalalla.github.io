<template>
    <my-header :style="headerStyle">
        <div class="container">
			<div class="navbar-brand">
				<a class="navbar-item has-text-weight-bold" href="/">Andrii Nakryiko's Blog</a>
			</div>
			<div id="navMenu" class="navbar-menu">
				<div class="navbar-end">
					
					<a href="https://nakryiko.com/" class="navbar-item">
						Home
					</a>
					
					<a href="https://nakryiko.com/posts" class="navbar-item">
						Posts
					</a>
					
					<a href="https://nakryiko.com/atom.xml" class="navbar-item">
						RSS
					</a>
					
				</div>
			</div>
		</div>
    </my-header>
</template>

<script>
export default {
    name: 'HeaderView',
    props: {
        width: {
        type: String,
        default: '100%'
        },
        height: {
        type: String,
        default: '100%'
        },
        backgroundColor: {
        type: String,
        default: '#333'
        },
        color: {
        type: String,
        default: '#fff'
        }
    },
    computed: {
        headerStyle() {
            return {
                width: this.width,
                height: this.height,
                backgroundColor: this.backgroundColor,
                color: this.color,
                display: 'flex',
                'flex-flow': 'row nowrap',
                alignItems: 'center',
                justifyContent: 'center',
                padding: '0 20px',
                boxSizing: 'border-box'
            };
        }
    }
};
</script>
  
<style scoped>
    .container {
        align-items: stretch;
        display: flex;
        min-height: 3.25rem;
        width: 100%;
    }
    .navbar-brand {
        align-items: stretch;
        display: flex;
        flex-shrink: 0;
        min-height: 3.25rem;
        width: max-content;
        margin-right: auto;
    }
    .navbar-end {
        display: flex;
        align-items: stretch;
        flex-direction: row;
        justify-content: flex-end;
        width: 100%;
        margin-left: auto;
    }
    .navbar-item {
        align-items: center;
        padding: 0.75rem 0.75rem;
    }
</style>
  