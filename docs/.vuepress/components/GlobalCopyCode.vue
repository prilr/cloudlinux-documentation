<template>
  <div class="user-global-copy-code-root"></div>
</template>

<script setup>
import { onMounted, watch, nextTick } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();

const addCopyButtons = () => {
    // Select all code blocks. VuePress code blocks typically have class starting with "language-"
    const blocks = document.querySelectorAll('div[class*="language-"]');
    
    blocks.forEach(block => {
        // Prevent adding multiple buttons
        if (block.querySelector('.copy-code-button')) return;
        
        const button = document.createElement('button');
        button.className = 'copy-code-button';
        button.type = 'button';
        button.ariaLabel = 'Copy code';
        button.innerHTML = `
           <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16" class="copy-icon">
             <path d="M0 6.75C0 5.784.784 5 1.75 5h1.5a.75.75 0 0 1 0 1.5h-1.5a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-1.5a.75.75 0 0 1 1.5 0v1.5A1.75 1.75 0 0 1 9.25 16h-7.5A1.75 1.75 0 0 1 0 14.25Z"></path>
             <path d="M5 1.75C5 .784 5.784 0 6.75 0h7.5C15.216 0 16 .784 16 1.75v7.5A1.75 1.75 0 0 1 14.25 11h-7.5A1.75 1.75 0 0 1 5 9.25Zm1.75-.25a.25.25 0 0 0-.25.25v7.5c0 .138.112.25.25.25h7.5a.25.25 0 0 0 .25-.25v-7.5a.25.25 0 0 0-.25-.25Z"></path>
           </svg>
           <span class="copy-success-icon" style="display: none;">
             <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" fill="currentColor" viewBox="0 0 16 16">
               <path d="M13.78 4.22a.75.75 0 0 1 0 1.06l-7.25 7.25a.75.75 0 0 1-1.06 0L2.22 9.28a.751.751 0 0 1 .018-1.042.751.751 0 0 1 1.042-.018L6 10.94l6.72-6.72a.75.75 0 0 1 1.06 0Z"></path>
             </svg>
           </span>
        `;
        
        button.onclick = async () => {
             const pre = block.querySelector('pre');
             if (!pre) return;
             
             // Get text content but careful not to include potential line numbers if they are implemented via psuedo elements it's fine.
             // If line numbers are separate elements, we might scrape them.
             // Standard VuePress prismjs plugin usage:
             const code = pre.textContent;
             
             try {
                 await navigator.clipboard.writeText(code);
                 
                 // Show success state
                 const copyIcon = button.querySelector('.copy-icon');
                 const successIcon = button.querySelector('.copy-success-icon');
                 
                 if (copyIcon && successIcon) {
                     copyIcon.style.display = 'none';
                     successIcon.style.display = 'inline';
                     button.classList.add('copied');
                     
                     setTimeout(() => {
                         copyIcon.style.display = 'inline';
                         successIcon.style.display = 'none';
                         button.classList.remove('copied');
                     }, 2000);
                 }
             } catch (err) {
                 console.error('Failed to copy!', err);
             }
        }
        
        // Append to the block
        block.appendChild(button);
    });
};

onMounted(() => {
    // Initial run
    addCopyButtons();
    
    // Observer for dynamic changes (if any)
    const observer = new MutationObserver(() => {
        addCopyButtons();
    });
    observer.observe(document.body, { childList: true, subtree: true });
});

watch(
  () => route.path,
  () => {
    nextTick(() => {
      // Small delay to ensure content is swapped
      setTimeout(addCopyButtons, 300);
    });
  }
);
</script>

<style>
/* Ensure the code block container is relative so absolute button works */
div[class*="language-"] {
    position: relative;
}

/* Button styles */
.copy-code-button {
    position: absolute;
    top: 7px;
    right: 10px;
    z-index: 10;
    padding: 6px;
    border: 1px solid rgba(255,255,255,0.1);
    background: transparent;
    border-radius: 4px;
    cursor: pointer;
    color: rgba(255,255,255,0.4);
    transition: all 0.25s ease;
    line-height: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}

.copy-code-button:hover {
    background: rgba(255, 255, 255, 0.1);
    color: #fff;
    border-color: rgba(255,255,255,0.3);
}

.copy-code-button.copied {
    color: #4caf50;
    border-color: #4caf50;
}
</style>
