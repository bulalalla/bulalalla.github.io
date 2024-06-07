<template>
    <footer :style="footerStyle" class="my-footer">
        <div class="d-flex flex-justify-center flex-items-center flex-column-reverse flex-lg-row flex-wrap flex-lg-nowrap">
            <div class="d-flex flex-items-center flex-shrink-0 mx-2">
                <a aria-label="Homepage" title="GitHub" class="footer-octicon mr-2" href="https://github.com">
                    <svg aria-hidden="true" height="24" viewBox="0 0 16 16" version="1.1" width="24" data-view-component="true" class="octicon octicon-mark-github">
                        <path d="M8 0c4.42 0 8 3.58 8 8a8.013 8.013 0 0 1-5.45 7.59c-.4.08-.55-.17-.55-.38 0-.27.01-1.13.01-2.2 0-.75-.25-1.23-.54-1.48 1.78-.2 3.65-.88 3.65-3.95 0-.88-.31-1.59-.82-2.15.08-.2.36-1.02-.08-2.12 0 0-.67-.22-2.2.82-.64-.18-1.32-.27-2-.27-.68 0-1.36.09-2 .27-1.53-1.03-2.2-.82-2.2-.82-.44 1.1-.16 1.92-.08 2.12-.51.56-.82 1.28-.82 2.15 0 3.06 1.86 3.75 3.64 3.95-.23.2-.44.55-.51 1.07-.46.21-1.61.55-2.33-.66-.15-.24-.6-.83-1.23-.82-.67.01-.27.38.01.53.34.19.73.9.82 1.13.16.45.68 1.31 2.69.94 0 .67.01 1.3.01 1.49 0 .21-.15.45-.55.38A7.995 7.995 0 0 1 0 8c0-4.42 3.58-8 8-8Z"></path>
                    </svg>
                </a>
                <span>
                    © 2024 GitHub,&nbsp;Inc.
                </span>
            </div>
        </div>
    </footer>
</template>
  
<script>
export default {
    name: 'FooterView',
    props: {
        width: {
        type: String,
        default: '100%'
        },
        height: {
        type: String,
        default: '60px'
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
        footerStyle() {
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
    footer {
        /* 可以添加其他默认样式 */
        display: flex;
        flex-wrap: wrap;
    }
</style>
  