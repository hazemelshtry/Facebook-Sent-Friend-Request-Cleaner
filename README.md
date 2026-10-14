# Facebook Sent Friend Request Cleaner

**Stop cancelling old Facebook friend requests one by one.**

A free browser Console script that automates cancellation clicks in Facebook’s **Sent requests** window. Open the list, run the script, and use its **Start** and **Stop** controls to manage the process.

Created by **Hazem El Shtry** · **Time = Knowledge = Value**

## Video tutorials

Prefer to follow along on screen? Visit [Hazem El Shtry on YouTube](https://www.youtube.com/@HazemElShtry) for practical tutorials on automation and tools that save time.

## What it does

- Finds available **Cancel request** buttons in the sent-requests dialog and clicks them sequentially.
- Scrolls the request list to try to load more entries.
- Adds a floating panel with **Start**, **Stop**, a status message, and a **Cancelled** counter.
- Runs directly in the browser page without installing an extension or using an API key.

It targets **pending outgoing friend requests**. It does not unfriend existing friends, cancel incoming requests, or unfollow accounts. It does not explain why a request was originally sent.

## Before you start

- Use Facebook’s desktop website in a browser with Developer Tools, such as Chrome or Edge.
- Sign in to your own Facebook account.
- **Set Facebook’s interface language to English.** The script matches the English label `Cancel request`.
- Review your sent requests first. The script has no per-person exclusion list: pressing Start begins cancelling the requests it can find.
- Keep the sent-requests window open while it runs.

**These clicks change your account.** Stop does not undo requests already cancelled. This script has no restore function.

## How to use it

### 1. Open your sent requests

Follow the route demonstrated in the tutorial:

**Your profile → Friends → Friend requests → View sent requests**

If Facebook shows another **Friend requests** entry in the intermediate screen, open that before selecting **View sent requests**. Navigation can vary between Facebook layouts. The important starting point is the dialog showing your pending sent requests and their **Cancel request** buttons.

### 2. Copy the script

Open [Facebook-Sent-Friend-Request-Cleaner.js](https://github.com/hazemelshtry/Facebook-Sent-Friend-Request-Cleaner/blob/main/Facebook-Sent-Friend-Request-Cleaner.js), then select **Raw**.

You can also [open the raw code directly](https://raw.githubusercontent.com/hazemelshtry/Facebook-Sent-Friend-Request-Cleaner/main/Facebook-Sent-Friend-Request-Cleaner.js). Copy the entire script, from the opening `(function () {` to the final `})();`. If the browser downloads the file, open it in a plain-text editor and copy its contents.

Return to your Facebook tab before continuing. Paste the code into Facebook’s Console, not the Console on GitHub or the raw-code page.

### 3. Open the browser Console

On the Facebook tab:

1. Right-click the page and choose **Inspect**, or press **Ctrl + Shift + I** on Windows. **F12** may also open Developer Tools.
2. Select the **Console** tab.
3. Review the script before pasting it. Console code runs inside your signed-in page; only run code you understand and trust.
4. Paste the script with **Ctrl + V** and press **Enter**.

If Chrome displays its self-XSS paste warning, read it first. If you have reviewed the script and choose to proceed, follow the Console’s instruction to type `allow pasting` manually and press Enter, then paste the script again and press **Enter** to run it. This step is only for the warning when it appears; it is not a Facebook command. See [Chrome’s explanation of the warning](https://developer.chrome.com/blog/self-xss).

### 4. Start the cleanup

The **Cancel Sent Requests** panel appears in the top-right corner.

| Control | What it does |
| --- | --- |
| **Start** | Begins finding and clicking Cancel request buttons. |
| **Stop** | Signals the running process to stop. It does not reverse earlier clicks. |
| **Cancelled** | Counts cancellation clicks issued by this script instance. It is not a count of server-confirmed cancellations. |
| **Status** | Shows progress, loading attempts, or a detected problem. |

Press **Start** once and monitor the page. If Facebook displays an error or action restriction, stop the process instead of repeatedly retrying.

**Run only one copy at a time.** Pasting the script again removes the old panel but does not stop its running process. The new Stop button does not control that old process. Rapidly pressing Stop and then Start can also leave overlapping runs.

For a clean restart, press Stop, reload Facebook, reopen **View sent requests**, and run the script once. Reloading removes the panel, ends the old page’s script execution, and resets the counter.

### 5. Verify the result

After the process stops:

1. Reload Facebook.
2. Open **View sent requests** again.
3. Check the actual remaining requests.

An empty list after reloading is the result to check. The panel’s completion message alone is not proof that Facebook accepted every cancellation.

<details>
<summary><strong>Technical details</strong></summary>

The script looks for a dialog containing “sent requests” or a matching Cancel request button. It clicks connected buttons with a non-zero rendered size, then tries to scroll when no matching button is available.

The supplied code uses:

- Button selector: `[role="button"][aria-label="Cancel request"]`
- Initial scroll-area selector: `.x1y1aw1k`, with a fallback search for scrollable elements inside the dialog
- A 100 ms wait before a click and an 800 ms wait after a cancellation attempt
- A 2,500 ms wait after scrolling, with additional retry waits
- A completion condition based on three scroll checks without detected progress

These timings are implementation details, not a guaranteed processing speed or protection against Facebook restrictions. No configuration changes are needed to follow the demonstrated workflow.

</details>

## Troubleshooting

| Problem | What to check |
| --- | --- |
| **Open "Sent requests" first** or **Sent requests window not found** | Open View sent requests before pressing Start, and keep that dialog open. |
| The panel appears but requests are not cancelled | Confirm that Facebook is in English and that the dialog contains Cancel request buttons. Facebook may have changed its page structure. |
| **Cannot find the scrollable request list** | The list may be empty, too short to scroll, or use an unsupported layout. Reload and inspect the actual requests manually. |
| The counter increases but requests remain | The counter records clicks, not Facebook’s response. Stop, reload, and check for failed actions or a Facebook restriction. |
| **Process complete** appears but some requests remain | The script stopped after detecting no further scrolling progress. Slow loading or interface changes can make it stop early. Check the list after reloading. |
| You need to start over | Stop, reload, reopen the sent-requests dialog, and run one copy of the script. |

## Limitations

This is a page-automation script, not an official Facebook API integration. Facebook can change its labels, dialogs, or scrolling behaviour, so compatibility with every account or future interface is not guaranteed.

If a cancellation fails and its button remains available, the script may keep clicking the same request. It does not automatically detect Facebook action restrictions; stop the process if you see a warning or repeated failed actions.

It cannot stop someone from accepting a request that Facebook already processed before cancellation. It also cannot reverse friendships that already exist or guarantee that you will never receive another request-acceptance notification.

## Privacy

The supplied script contains no explicit analytics, file export, credential collection, or third-party upload code. It reads the current page structure, adds its control panel, and clicks Facebook controls. Those actions still use your signed-in Facebook session and Facebook’s normal page requests.

Never share your password, cookies, access tokens, or session details when asking for help. Remove names, profile pictures, account identifiers, and other personal information from screenshots before posting them publicly.

## Educational purpose

This project is shared for educational purposes to demonstrate JavaScript DOM selection, browser automation, asynchronous loops, scrolling, and simple user-interface controls. Use it only on an account you own or are authorized to manage, and review the code before running it.

## Disclaimer

This is an independent project and is not affiliated with, endorsed by, or supported by Meta or Facebook.

The script is provided as is, without guarantees of compatibility, accuracy, availability, or uninterrupted operation. Use it at your own risk. You are responsible for the actions performed on your account and for complying with applicable platform rules and laws. An educational purpose does not grant permission to bypass platform restrictions.

No claim is made that automation is approved by Facebook or that using this script cannot lead to errors, temporary limits, or other account restrictions. The author makes no guarantee that cancelled requests can be recovered.

---

**Maximize your productivity. Minimize wasted time.**

More practical tutorials: [Hazem El Shtry on YouTube](https://www.youtube.com/@HazemElShtry)
