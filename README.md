

    (function() {
    const targetDiv = document.querySelector('div.relative.leading-relaxed.select-none.overflow-hidden');
    const input = document.querySelector('input') || document.querySelector('textarea');

    if (!targetDiv || !input) {
        console.error("Could not find text container or input field!");
        return;
    }

    const text = targetDiv.innerText;
    let i = 0;

    const nativeInputValueSetter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype, "value").set;

    function typeNext() {
        if (i >= text.length) {
            console.log("Typing complete.");
            return;
        }

        if (!document.body.contains(input) || input.disabled) {
            console.log("Typing stopped.");
            return;
        }
        
        const char = text[i];
        input.focus();

        const newValue = input.value + char;
        if (nativeInputValueSetter) {
            nativeInputValueSetter.call(input, newValue);
        } else {
            input.value = newValue;
        }

        const eventData = {
            key: char,
            code: char === ' ' ? 'Space' : (char.match(/[a-zA-Z]/) ? `Key${char.toUpperCase()}` : ''),
            charCode: char.charCodeAt(0),
            keyCode: char.charCodeAt(0),
            which: char.charCodeAt(0),
            bubbles: true,
            cancelable: true
        };

        input.dispatchEvent(new KeyboardEvent('keydown', eventData));
        input.dispatchEvent(new KeyboardEvent('keypress', eventData));
        input.dispatchEvent(new Event('input', { bubbles: true }));
        input.dispatchEvent(new KeyboardEvent('keyup', eventData));

        i++;

        // Tuned for ~120 WPM (average ~100ms delay per char) with variance for stddiv
        let delay = Math.floor(Math.random() * 90) + 55; // 55ms to 145ms base
        
        // Minor natural pauses on spaces or occasional stutters
        if (char === ' ') {
            delay += Math.floor(Math.random() * 50) + 15;
        } else if (Math.random() < 0.05) {
            delay += Math.floor(Math.random() * 70);
        }

        setTimeout(typeNext, delay); 
    }

    typeNext();
    })();
