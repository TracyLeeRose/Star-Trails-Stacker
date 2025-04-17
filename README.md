# Star-Trails-Stacker
Stacks and combines numerous star images of the sky into one


markdown
# Aquiplicity 2025 - Star Trail Image Stacker (v4 - Fix)

A browser-based tool for stacking images to create star trail effects or reduce noise in astrophotography. Load multiple images (JPG, PNG, WEBP) of the same dimensions, select a stacking algorithm,
and generate a composite image. This version includes fixes for improved reliability and performance.

## Features

- **Image Stacking Algorithms**:
  - **Lighten**: Combines images by taking the brightest pixel at each position, ideal for star trails.
  - **Maximum**: Similar to Lighten, emphasizes brightest pixels.
  - **Average**: Averages pixel values across images, useful for noise reduction.
- **Thumbnail Preview**: Displays loaded images in a sidebar for reference.
- **Progress Tracking**: Visual progress bar during image loading and stacking.
- **Cancel Operation**: Stop stacking process if needed.
- **Save Output**: Export the stacked image as a PNG file.
- **Responsive UI**: Clean, user-friendly interface with error handling and status updates.

## Installation

No installation required! Simply open the `index.html` file in a modern web browser (e.g., Chrome, Firefox, Edge). The application runs entirely client-side using HTML, CSS, and JavaScript.

### Steps:
1. Clone or download this repository:
   ```bash
   git clone https://github.com/your-username/star-trail-image-stacker.git
Open index.html in a web browser:
bash
open index.html
Or double-click the file in your file explorer.

Usage
Load Images:
Click the file input button to select multiple images (JPG, PNG, WEBP).
Ensure all images have the same dimensions to avoid errors.
Select Algorithm:
Choose from "Lighten," "Maximum," or "Average" in the dropdown menu.

Stack Images:
Click "Stack Images" to process the loaded images.
Monitor progress via the progress bar and status messages.

Save Result:
Once stacking is complete, click "Save Stacked Image" to download the result as a PNG.

Reset:
Click "Reset" to clear loaded images and start over.

Cancel:
Click "Cancel" during stacking to stop the process.

Notes:
The application checks for dimension consistency and skips invalid or non-image files.
Large images or many files may take longer to process; consider testing with smaller sets first.
Console logs are available for debugging (open browser DevTools with F12).

Screenshots
(Optional: Add screenshots here if you have them, e.g., interface, sample output.)
Dependencies
None! The application uses vanilla JavaScript and standard browser APIs (Canvas, File, Image).
Tested on modern browsers (Chrome, Firefox, Edge). Older browsers may not support all features.

Limitations
All images must have identical dimensions.
Large images or numerous files may strain browser memory; performance depends on the user's device.
No server-side processing; everything runs locally in the browser.

Canvas size limitations may apply for very large images (browser-dependent).

Contributing
Contributions are welcome! To contribute:
Fork the repository.
Create a new branch (git checkout -b feature/your-feature).
Make changes and commit (git commit -m "Add your feature").
Push to your fork (git push origin feature/your-feature).
Open a Pull Request.
Please ensure code follows the existing style and includes comments for clarity.
License

This project is licensed under the MIT License. See the LICENSE file for details.
Acknowledgements

Built for astrophotography enthusiasts to simplify star trail creation.
Inspired by tools like StarStaX but implemented as a lightweight, browser-based alternative.

Contact
For issues, suggestions, or questions, open an issue on GitHub or contact the maintainer at aquiline.photos@gmail.com

Happy stargazing! 🌟
