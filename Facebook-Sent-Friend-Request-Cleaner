(function () {
    'use strict';

    // ============================================================
    // SETTINGS
    // ============================================================

    // Cancel button from the current Facebook HTML
    const CANCEL_BUTTON_SELECTOR =
        '[role="button"][aria-label="Cancel request"]';

    // Current scrollable area
    const SCROLL_LIST_SELECTOR = '.x1y1aw1k';

    // Delay between cancellations
    const CLICK_DELAY = 800;

    // Delay after scrolling/loading more requests
    const SCROLL_DELAY = 2500;

    // Number of failed scroll attempts before considering it finished
    const MAX_SCROLL_ATTEMPTS = 3;

    // ============================================================
    // STATE
    // ============================================================

    let isStopped = true;
    let cancelCounter = 0;
    let scrollAttempts = 0;

    // ============================================================
    // REMOVE OLD PANEL IF SCRIPT WAS RUN BEFORE
    // ============================================================

    const oldPanel = document.getElementById('cancelRequestsControlPanel');

    if (oldPanel) {
        oldPanel.remove();
    }

    // ============================================================
    // CONTROL PANEL
    // ============================================================

    const controlPanel = document.createElement('div');

    controlPanel.id = 'cancelRequestsControlPanel';

    controlPanel.style.cssText = `
        position: fixed;
        top: 10px;
        right: 10px;
        z-index: 2147483647;
        background: #f0f2f5;
        border: 1px solid #ccc;
        padding: 15px;
        border-radius: 8px;
        text-align: center;
        font-family: Arial, sans-serif;
        box-shadow: 0 4px 12px rgba(0,0,0,0.25);
        min-width: 250px;
    `;

    controlPanel.innerHTML = `
        <h3 style="
            margin: 0 0 12px 0;
            color: #1877f2;
            font-size: 18px;
        ">
            Cancel Sent Requests
        </h3>

        <button
            id="cancelRequestsStartButton"
            style="
                background-color: #42b72a;
                color: white;
                padding: 10px 18px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
                margin-right: 5px;
                font-weight: bold;
            "
        >
            Start
        </button>

        <button
            id="cancelRequestsStopButton"
            style="
                background-color: #fa3e3e;
                color: white;
                padding: 10px 18px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
                font-weight: bold;
            "
            disabled
        >
            Stop
        </button>

        <div
            id="cancelRequestsStatus"
            style="
                margin-top: 12px;
                font-size: 14px;
                color: #606770;
            "
        >
            Status: Ready to start.
        </div>

        <div
            style="
                font-size: 13px;
                color: #555;
                margin-top: 8px;
            "
        >
            Cancelled:
            <strong id="cancelRequestsCount">0</strong>
        </div>
    `;

    document.body.appendChild(controlPanel);

    // ============================================================
    // UI REFERENCES
    // ============================================================

    const startButton =
        document.getElementById('cancelRequestsStartButton');

    const stopButton =
        document.getElementById('cancelRequestsStopButton');

    const statusDiv =
        document.getElementById('cancelRequestsStatus');

    const cancelCountSpan =
        document.getElementById('cancelRequestsCount');

    // ============================================================
    // HELPERS
    // ============================================================

    function updateStatus(message, color = '#606770') {
        statusDiv.textContent = `Status: ${message}`;
        statusDiv.style.color = color;
    }

    function updateCounter() {
        cancelCounter++;
        cancelCountSpan.textContent = cancelCounter;
    }

    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    // ============================================================
    // FIND SENT REQUESTS DIALOG
    // ============================================================

    function findSentRequestsDialog() {

        const dialogs =
            [...document.querySelectorAll('[role="dialog"]')];

        return dialogs.find(dialog => {

            const text =
                (dialog.textContent || '').toLowerCase();

            return (
                text.includes('sent requests') ||
                dialog.querySelector(CANCEL_BUTTON_SELECTOR)
            );

        }) || null;
    }

    // ============================================================
    // FIND SCROLLABLE AREA
    // ============================================================

    function findScrollableList(dialog) {

        // First try the class from the HTML
        const knownList =
            dialog.querySelector(SCROLL_LIST_SELECTOR);

        if (
            knownList &&
            knownList.scrollHeight > knownList.clientHeight
        ) {
            return knownList;
        }

        // Fallback:
        // Automatically find the biggest scrollable element
        const elements =
            [...dialog.querySelectorAll('*')];

        const candidates = elements.filter(element => {

            const style =
                window.getComputedStyle(element);

            const overflowY =
                style.overflowY;

            return (
                element.scrollHeight >
                element.clientHeight + 20 &&
                (
                    overflowY === 'auto' ||
                    overflowY === 'scroll'
                )
            );
        });

        if (!candidates.length) {
            return knownList || null;
        }

        candidates.sort(
            (a, b) =>
                b.scrollHeight - a.scrollHeight
        );

        return candidates[0];
    }

    // ============================================================
    // STOP
    // ============================================================

    function stopProcess(message = 'Stopped by user.', color = 'orange') {

        isStopped = true;

        startButton.disabled = false;
        stopButton.disabled = true;

        updateStatus(message, color);

        console.log(
            `[Cancel Requests] ${message}`
        );
    }

    // ============================================================
    // PROCESS REQUESTS
    // ============================================================

    async function processRequests() {

        while (!isStopped) {

            const dialog =
                findSentRequestsDialog();

            if (!dialog) {

                stopProcess(
                    'Sent requests window not found. Open it first.',
                    'red'
                );

                return;
            }

            // ----------------------------------------------------
            // FIND CURRENT CANCEL BUTTON
            // ----------------------------------------------------

            const cancelButtons =
                [...dialog.querySelectorAll(
                    CANCEL_BUTTON_SELECTOR
                )];

            const cancelButton =
                cancelButtons.find(button => {

                    if (!button.isConnected) {
                        return false;
                    }

                    const rect =
                        button.getBoundingClientRect();

                    return (
                        rect.width > 0 &&
                        rect.height > 0
                    );
                });

            // ----------------------------------------------------
            // CANCEL REQUEST
            // ----------------------------------------------------

            if (cancelButton) {

                updateStatus(
                    `Cancelling request #${cancelCounter + 1}...`,
                    'green'
                );

                try {

                    cancelButton.scrollIntoView({
                        behavior: 'auto',
                        block: 'center'
                    });

                    await sleep(100);

                    if (isStopped) {
                        return;
                    }

                    cancelButton.click();

                    updateCounter();

                    scrollAttempts = 0;

                    console.log(
                        `[Cancel Requests] Cancelled #${cancelCounter}`
                    );

                } catch (error) {

                    console.error(
                        '[Cancel Requests] Click error:',
                        error
                    );
                }

                await sleep(CLICK_DELAY);

                continue;
            }

            // ----------------------------------------------------
            // NO BUTTON FOUND -> SCROLL
            // ----------------------------------------------------

            updateStatus(
                'No visible requests. Loading more...',
                '#1877f2'
            );

            const scrollableList =
                findScrollableList(dialog);

            if (!scrollableList) {

                stopProcess(
                    'Cannot find the scrollable request list.',
                    'red'
                );

                return;
            }

            const oldHeight =
                scrollableList.scrollHeight;

            const oldTop =
                scrollableList.scrollTop;

            // Scroll to bottom
            scrollableList.scrollTop =
                scrollableList.scrollHeight;

            // Also fire scroll event
            scrollableList.dispatchEvent(
                new Event('scroll', {
                    bubbles: true
                })
            );

            await sleep(SCROLL_DELAY);

            if (isStopped) {
                return;
            }

            const newHeight =
                scrollableList.scrollHeight;

            const newTop =
                scrollableList.scrollTop;

            // ----------------------------------------------------
            // CHECK IF NEW CONTENT LOADED
            // ----------------------------------------------------

            if (
                newHeight > oldHeight ||
                newTop > oldTop
            ) {

                scrollAttempts = 0;

                updateStatus(
                    'More requests loaded...',
                    '#1877f2'
                );

                continue;
            }

            // ----------------------------------------------------
            // NOTHING NEW
            // ----------------------------------------------------

            scrollAttempts++;

            if (
                scrollAttempts >=
                MAX_SCROLL_ATTEMPTS
            ) {

                stopProcess(
                    `Process complete! Cancelled ${cancelCounter} requests.`,
                    'green'
                );

                return;
            }

            updateStatus(
                `Nothing new loaded. Retrying (${scrollAttempts}/${MAX_SCROLL_ATTEMPTS})...`,
                'orange'
            );

            // Small extra scroll attempt
            scrollableList.scrollBy({
                top: 1000,
                behavior: 'auto'
            });

            scrollableList.dispatchEvent(
                new Event('scroll', {
                    bubbles: true
                })
            );

            await sleep(1000);
        }
    }

    // ============================================================
    // START BUTTON
    // ============================================================

    startButton.onclick = () => {

        if (!isStopped) {
            return;
        }

        const dialog =
            findSentRequestsDialog();

        if (!dialog) {

            updateStatus(
                'Open "Sent requests" first.',
                'red'
            );

            return;
        }

        isStopped = false;

        startButton.disabled = true;
        stopButton.disabled = false;

        scrollAttempts = 0;

        updateStatus(
            'Starting...',
            '#1877f2'
        );

        console.log(
            '[Cancel Requests] Starting...'
        );

        processRequests();
    };

    // ============================================================
    // STOP BUTTON
    // ============================================================

    stopButton.onclick = () => {

        stopProcess(
            'Stopped by user.',
            'orange'
        );
    };

    console.log(
        '[Cancel Requests] Script loaded successfully.'
    );

})();
