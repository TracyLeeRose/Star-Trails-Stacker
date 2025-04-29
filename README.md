# Star-Trails-Stacker 2025 (Image Stacker for creating astro photography star trails & Video Creator)
This is a browser-based utility designed to perform two main functions using a sequence of images:

1.  **Image Stacking:** Combine multiple images into a single image using different algorithms (Lighten, Average, Maximum). This is useful for astrophotography (star trails), noise reduction, or creating artistic effects.
2.  **Video Creation:** Create a video (timelapse) from the sequence of loaded images at a specified frame rate (FPS).

The application runs entirely in the user's web browser using HTML, CSS, and JavaScript. No server-side processing is required.

## Features

*   **Multiple Image Loading:** Load multiple images (JPG, PNG, WEBP) simultaneously using the file input.
*   **Automatic Sorting:** Loaded image files are automatically sorted alphanumerically based on their filenames, ensuring correct sequence for stacking and video creation.
*   **Dimension Validation:** The application checks if all loaded images have the **same dimensions**. Images with mismatching dimensions are skipped during loading to ensure processing integrity.
*   **Thumbnail Preview:** Displays thumbnails of all successfully loaded images in a scrollable strip.
*   **Image Deletion:** Allows removing individual images from the loaded set via a delete ('X') button on each thumbnail.
*   **Image Stacking:**
    *   Requires at least **two** images.
    *   Offers multiple stacking algorithms:
        *   **Lighten (Default):** For each pixel position, the brightest pixel value across all images is kept. Ideal for star trails.
        *   **Average:** Calculates the average pixel value for each position across all images. Useful for noise reduction.
        *   **Maximum:** Similar to Lighten, keeps the maximum RGB values for each pixel.
    *   Processes images sequentially using the Canvas API for pixel manipulation.
    *   Includes a yield mechanism (`setTimeout(resolve, 0)`) during stacking to prevent the browser UI from freezing completely on large tasks.
*   **Video Creation:**
    *   Requires at least **one** image.
    *   Uses the browser's native `MediaRecorder` API. **Browser support is required.**
    *   Allows setting the **Frames Per Second (FPS)** for the output video (1-60 FPS).
    *   Draws each image frame onto a `<canvas>` element and captures the stream.
    *   Outputs video primarily in **WEBM** format (VP8/VP9 codec) or potentially MP4 depending on browser support for `MediaRecorder` MIME types.
*   **Canvas Preview:** Displays the first loaded image, the final stacked image result, or the frames being drawn during video creation in the main working area.
*   **Progress Indicator:** Shows a progress bar during image loading, stacking, and video creation.
*   **Status Updates:** Provides informative messages about the current state, loaded image count, dimensions, ongoing processes, errors, and results.
*   **Cancellation:** Allows cancelling ongoing stacking or video creation operations.
*   **Reset Functionality:** Clears all loaded images, thumbnails, canvas content, and resets the UI to its initial state. Includes memory cleanup by revoking Object URLs.
*   **Save Results:**
    *   Save the **stacked image** as a PNG file (with a fallback to JPG if PNG saving fails). Filename includes algorithm and frame count (e.g., `stacked_lighten_50frames.png`).
    *   Save the **created video** as a WEBM or MP4 file. Filename includes frame count and FPS (e.g., `video_50frames_10fps.webm`).
*   **Responsive UI:** Basic layout using Flexbox adapts to different window sizes. Max canvas size is constrained to fit within the viewport.
*   **Dark Theme:** Uses a dark color scheme for the interface.

## How to Use

1.  **Open the File:** Open the `[your_filename].html` file in a modern web browser (like Chrome, Firefox, Edge).
2.  **Load Images:**
    *   Click the "Choose Files" button.
    *   Select multiple image files (`.jpg`, `.png`, `.webp`). Ensure they are sequentially named if order matters (e.g., `IMG_001.jpg`, `IMG_002.jpg`, etc.) as they will be sorted automatically.
    *   **Crucially, all selected images intended for processing must have the exact same width and height.**
    *   The application will load the images, display thumbnails, show the first image on the canvas, and update the status.
3.  **Manage Thumbnails (Optional):**
    *   Review the thumbnails in the left strip.
    *   If an image is incorrect or flawed, click the red 'X' button on its thumbnail to remove it from the set.
4.  **Choose Operation:** Decide whether you want to stack the images or create a video.
5.  **Configure:**
    *   **For Stacking:** Select the desired `Stack Alg` (Lighten, Average, Maximum) from the dropdown menu.
    *   **For Video:** Enter the desired `Video FPS` (Frames Per Second) in the number input field (1-60).
6.  **Run Operation:**
    *   Click the **"Stack Images"** button (enabled if >= 2 images are loaded).
    *   *OR* Click the **"Create Video"** button (enabled if >= 1 image is loaded and `MediaRecorder` is supported).
    *   The progress bar and status message will indicate the progress.
7.  **Cancel (Optional):** If the operation takes too long or you change your mind, click the **"Cancel"** button that appears while processing.
8.  **Save Result:**
    *   After stacking completes successfully, the **"Save Stacked Image"** button will become enabled. Click it to download the resulting PNG/JPG file.
    *   After video creation completes successfully, the **"Save Video"** button will become enabled. Click it to download the resulting WEBM/MP4 file.
9.  **Reset:** Click the **"Reset"** button to clear everything and start over with new images.

## Technical Details

*   **Frontend:** Pure HTML, CSS, and JavaScript. No external libraries or frameworks are used.
*   **Image Loading:** Uses `FileReader` (implicitly via `new Image().src = URL.createObjectURL()`) to load images selected by the user. `URL.createObjectURL()` is used for efficient handling and display.
*   **Sorting:** Uses `localeCompare` with `numeric: true` option for robust alphanumeric sorting of filenames.
*   **Asynchronous Operations:** Leverages `async/await` and `Promise.allSettled` for handling image loading concurrently and managing success/failure states gracefully.
*   **Canvas API:** The `<canvas>` element is central to the application:
    *   It displays previews of loaded images.
    *   For stacking, `getImageData()` is used to read pixel data from loaded images (drawn onto a hidden worker canvas), and `putImageData()` is used to write the calculated result back to the visible canvas. Pixel manipulation happens directly on the `ImageData.data` array.
    *   For video creation, `canvas.captureStream(fps)` generates a `MediaStream` from the canvas content as frames are drawn onto it sequentially using `drawImage()`.
*   **Stacking Logic:**
    *   Iterates through the `ImageData` arrays (RGBA values) of the images.
    *   Applies the selected algorithm (`Math.max` for Lighten/Maximum, summation then division for Average) pixel by pixel.
    *   Alpha channel (`A` in RGBA) is generally forced to 255 (fully opaque) in the result.
    *   Uses a secondary hidden canvas (`workerCanvas`) to draw intermediate images for getting their pixel data without disturbing the main display canvas until the final result is ready (though in this version, it seems `workerCtx.drawImage` is used primarily to get `ImageData` without necessarily *needing* a separate canvas if performance isn't critically impacted by drawing to the main one repeatedly before `getImageData`).
*   **Video Encoding:** Relies entirely on the browser's built-in `MediaRecorder` implementation.
    *   The supported codecs (VP8, VP9, H.264) and container format (WebM, MP4) depend heavily on the specific browser and operating system. The code attempts to find a supported MIME type, defaulting to the browser's choice if common ones aren't explicitly supported.
    *   Video data is collected in chunks (`ondataavailable` event) and then assembled into a `Blob` when recording stops (`onstop` event).
*   **Memory Management:** `URL.revokeObjectURL()` is called when images are removed (deleted via thumbnail or via reset) or after video blob creation to free up browser memory associated with the created object URLs.
*   **UI Responsiveness:** Uses non-blocking techniques like `async/await` and a `setTimeout` yield (`await new Promise(resolve => setTimeout(resolve, 0));`) within the intensive stacking loop (`YIELD_INTERVAL`) to try and keep the UI somewhat responsive during processing.
*   **State Management:** Uses global JavaScript variables (e.g., `loadedImageObjects`, `imageDimensions`, `currentOperation`, `stackedImageAvailable`, `recordedVideoBlob`) to maintain the application's state. Buttons are enabled/disabled based on this state (`resetUIState`, `setControlsBusy` functions).

## Browser Compatibility

*   **Core Functionality (Image Loading, Stacking, Saving Image):** Should work on most modern browsers that support the Canvas API (Chrome, Firefox, Edge, Safari).
*   **Video Creation & Saving Video:** Requires a browser that supports the `MediaRecorder` API and `canvas.captureStream()`. This is generally available in recent versions of Chrome, Firefox, and Edge, but may have limitations or be unavailable in older browsers or Safari (historically, Safari's support has lagged or required specific configurations). The application attempts to detect support and disable video features if unavailable.
*   **Performance:** Handling a very large number of high-resolution images can consume significant RAM and CPU resources, potentially leading to slow performance or browser instability, as all processing happens client-side.

## Limitations & Known Issues

*   **Memory Intensive:** Loading many large images can consume a lot of browser memory. There's no hard limit enforced by the code, relying on the browser's capacity.
*   **UI Freezing:** Despite the yielding mechanism, extremely intensive stacking operations (many large images) might still cause the browser UI to become temporarily unresponsive.
*   **Video Format/Codec:** The output video format and codec are determined by the browser's `MediaRecorder` implementation and cannot be explicitly chosen by the user beyond the browser's capabilities.
*   **Dimension Rigidity:** Images *must* have identical dimensions. There's no built-in resizing or cropping. Mismatched images are simply skipped.
*   **No Reordering:** Thumbnails cannot be reordered via drag-and-drop; the sequence is fixed by the initial filename sort.
*   **Error Handling:** While basic error handling is present, edge cases or specific file corruption issues might lead to unexpected behavior. Status messages provide some feedback.

## Potential Future Improvements

*   **Web Workers:** Offload the heavy image stacking and potentially video frame processing/encoding logic to Web Workers to prevent UI freezing entirely.
*   **Drag-and-Drop Reordering:** Allow users to reorder the image sequence using thumbnails.
*   **More Stacking Algorithms:** Implement other modes like Median, Darken, etc.
*   **Image Preprocessing:** Add options for resizing, cropping, or aligning images before processing.
*   **Video Options:** Explore if `MediaRecorder` offers more control over bitrate or quality settings (often limited).
*   **Project Save/Load:** Allow saving the current state (list of files, settings) to resume later (would likely require more advanced file handling APIs or local storage).
*   **Improved Error Reporting:** Provide more detailed error messages and potentially highlight problematic images.
*   **UI/UX Enhancements:** Improve visual design, add tooltips, refine layout.

## License

(No license specified in the code) - Consider adding an open-source license like MIT if distributing.
