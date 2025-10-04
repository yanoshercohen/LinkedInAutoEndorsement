# LinkedIn Auto-Endorse Script
Automatically endorses all skills on a LinkedIn profile by navigating to the skills section (`https://www.linkedin.com/in/[username]/details/skills/`).

> [!CAUTION]
> This JavaScript code is for **research purposes** only and is not intended to be used as a basis for any commercial or non-research purposes.
> The use of this code is at the user's own risk, and the author(s) assume no responsibility for any misuse or unintended consequences.
>
> **By using this code, the user acknowledges and agrees to the following:**
>
> - The user is solely responsible for any legal consequences arising from the use of this code.
> - The user will not hold the author(s) liable for any damages, losses, or legal actions resulting from the use of this code.
> - The user agrees to comply with any applicable laws and regulations, including but not limited to copyright, intellectual property, and privacy laws.
> - Furthermore, the user acknowledges that using this code may violate LinkedIn's Terms of Service and could potentially lead to account restrictions or other penalties.
> - The user is advised to consult their own legal counsel before using this code to ensure compliance with all applicable laws and regulations.
## Usage
1. Navigate to the target user's LinkedIn skills section: `https://www.linkedin.com/in/[username]/details/skills/`
2. Press F12 to open the developer tools.
3. Go to the `console` tab.
4. Paste the script.
```js
(function() {
    let isProcessing = false;
    let processedButtons = new Set();
    let observer = null;

    function clickEndorseButton(button) {
        if (!processedButtons.has(button)) {
            button.click();
            processedButtons.add(button);
            console.log(`Endorsed: ${button.textContent.trim()}`);
        }
    }

    function processNewButtons() {
        const newButtons = [...document.querySelectorAll('button')]
            .filter(btn => 
                btn.textContent.trim() === 'Endorse' && 
                !processedButtons.has(btn)
            );
        
        newButtons.forEach(clickEndorseButton);
        
        return newButtons.length;
    }

    function clickShowMore() {
        const showMoreSpan = [...document.querySelectorAll('span')]
            .find(span => 
                span.textContent.trim() === 'Show more results' && 
                span.offsetParent !== null
            );
        
        if (showMoreSpan) {
            const parentButton = showMoreSpan.closest('button');
            if (parentButton) {
                parentButton.click();
            } else {
                showMoreSpan.click();
            }
            return true;
        }
        return false;
    }

    function scrollToBottom() {
        window.scrollTo(0, document.body.scrollHeight);
    }

    async function executeAutoEndorse() {
        if (isProcessing) return;
        isProcessing = true;
        console.log("Starting auto-endorsement process...");

        observer = new MutationObserver(() => {
            processNewButtons();
            
            if (clickShowMore()) {
                scrollToBottom();
            }
        });

        observer.observe(document.body, {
            childList: true,
            subtree: true
        });

        const initialCount = processNewButtons();
        console.log(`Initially found ${initialCount} buttons.`);

        scrollToBottom();

        console.log("Observer started. Will automatically endorse new buttons and click 'Show more results' as they appear.");
    }

    executeAutoEndorse().catch(e => {
        console.error(e);
        alert("Error occurred.");
        isProcessing = false;
        if (observer) observer.disconnect();
    });
})();
```
