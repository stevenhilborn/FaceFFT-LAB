# FaceFFT-LAB
An interactive, browser-based Fourier Transform lab that converts images into math and rebuilds them, featuring progressive reconstruction, spectrum views, and accuracy metrics. 100% local.
# Face FFT Lab ∿

**Turn an image into mathematics—and reconstruct it from the numbers alone.**

Face FFT Lab is a self-contained, browser-based playground for exploring the **two-dimensional Fast Fourier Transform (2D FFT)**. Upload a photograph, generate its full set of RGB Fourier coefficients, and reconstruct the sampled image using the inverse transform. Move a slider to see how low spatial frequencies establish broad shapes while higher frequencies restore fine detail.

No installation, account, backend, or external JavaScript libraries are required.

## Features

- **Upload a photo:** PNG or JPEG; drag and drop or browse for a file.
- **Try samples instantly:** Illustrated portrait, grid, and stripes.
- **Choose FFT resolution:** 128 × 128, 256 × 256, or 512 × 512 pixels.
- **Explore six visualizations:** Original photo, FFT input, log-magnitude spectrum, retained frequencies, inverse-FFT reconstruction, and an amplified difference image.
- **Progressive reconstruction:** Adjust a radial frequency-cutoff slider from 0% to 100%. At 100%, all coefficients are retained. *The slider controls the cutoff radius, not a literal percentage of the coefficients*; the interface shows the actual coefficient count.
- **Check reconstruction accuracy:** View peak signal-to-noise ratio (**PSNR**) and mean absolute pixel error (**MAE**) relative to the sampled FFT input, while that input is available.
- **Export the equation as plain text:** Save or copy a `.txt` document containing the reconstruction formula and the numerical complex Fourier coefficients for red, green, and blue.
- **Rebuild from text alone:** Import a previously exported `.txt` file (or paste its contents) and reconstruct without the original photograph.
- **Download the reconstruction:** Save the current output as a PNG.
- **Local processing:** Photo processing happens in your browser, with no image-upload server or third-party dependencies in the application.

## Try it

### Run locally

1. Download or clone this repository.
2. Open `index.html` in a modern browser, such as Chrome, Edge, or Firefox.
3. Upload an image or choose a preset.
4. Select a resolution and click **Calculate face equation**.
5. Drag the **Fourier coefficients retained** slider to explore reconstruction at different frequency cutoffs.
6. Click **Download reconstruction as PNG** to save the current result.

### Reconstruct from the equation alone

1. After calculating an image, click **Save formula as .txt** (or **Copy formula**).
2. Click **Remove original photo**, or reopen the app later.
3. Paste the saved coefficient text into the formula field, or select **Import .txt**.
4. Click **Rebuild from pasted text**.

The text export is **not JSON**. It includes a readable equation plus the full numerical RGB coefficient tables. The equation by itself is general; the coefficient values are what encode the particular image.

## The mathematics

For each color channel \(c\in\{R,G,B\}\), an \(N\times N\) image is represented by its 2D discrete Fourier transform coefficients \(F_c(u,v)\). The inverse transform reconstructs the pixels:

$$
f_c(x,y)=\frac{1}{N^2}\sum_{u=0}^{N-1}\sum_{v=0}^{N-1}
F_c(u,v)\,e^{2\pi i(ux+vy)/N}.
$$

The implementation uses a **radix-2 Cooley–Tukey FFT** in vanilla JavaScript, applied across rows and columns for each RGB channel. The magnitude spectrum is displayed on a logarithmic scale with zero frequency centered. The progressive slider retains frequencies within a centered radial cutoff before applying the inverse FFT.

With all coefficients retained, the FFT/IFFT is mathematically lossless for the sampled pixels, aside from floating-point computation and final pixel rounding. **Resizing the source to the selected square FFT resolution can discard image detail before the transform**; reconstructed output should therefore be compared against the **FFT input**, not necessarily the original full-resolution photograph.

## Understanding accuracy

- **PSNR (dB):** Higher is better; infinite PSNR indicates zero measured squared error.
- **MAE:** Average absolute difference in 8-bit pixel values; lower is better, and zero indicates an exact pixel match.
- **Difference image:** Displays absolute pixel differences, amplified 4× to make subtle errors visible.

After rebuilding from an imported formula without its original sampled pixels, accuracy comparison is unavailable and the metrics show **N/A**. The reconstruction itself does not need the source photo.

## Privacy

**Your photos never leave your browser.** Face FFT Lab performs image loading, FFT calculation, and reconstruction locally. The application does not contain an image-upload endpoint, analytics, telemetry, or external library requests.

**Be careful with exported coefficient files:** A complete Fourier coefficient file can reconstruct the image, including a face. Treat it as sensitive image data and avoid publicly committing your personal coefficient exports to GitHub. Hosting the app on GitHub Pages serves the application code, not visitors' images.

## Performance and file size

| FFT resolution | RGB complex coefficients | Notes |
| --- | ---: | --- |
| 128 × 128 | 49,152 | Quick experiments; limited fine detail |
| 256 × 256 | 196,608 | Balanced starting point |
| 512 × 512 | 786,432 | More detail; large text exports and heavier processing |

Plain-text Fourier data is intentionally verbose and can grow to tens of megabytes at higher resolutions. Performance varies by device and browser. For smoother interaction, start at **256 × 256**.

## Publish with GitHub Pages

1. Create a public GitHub repository, for example **`Face-FFT-Lab`**.
2. Add `index.html` and this `README.md` to the repository root.
3. Open **Settings → Pages** in the repository.
4. Under **Build and deployment**, choose **Deploy from a branch**; select your default branch and the **`/ (root)`** folder, then save.
5. When deployment completes, GitHub Pages will provide the site URL in the Pages settings.

The site needs only the `index.html` file to run; the README is for documentation.

## Limitations

- The output describes **a particular 2D image**, not a universal equation of someone's 3D face.
- Images are resampled to a square power-of-two resolution (128, 256, or 512) before the FFT. This may change aspect ratio and remove fine detail.
- Larger coefficient exports are much bigger than compressed image files; this project demonstrates Fourier mathematics, not efficient image compression.
- A simplified progressive reconstruction is achieved by radial frequency filtering; it is not a semantic model of facial features.

## Tech stack

**HTML5 · CSS3 · Vanilla JavaScript · Canvas API · 2D FFT / inverse FFT**

Everything is implemented in one `index.html` file—no build step and no external runtime dependencies.

---

**Suggested GitHub repository description:**

> Explore image-to-equation reconstruction with a browser-based 2D Fourier Transform lab. Progressive frequency filtering, accuracy metrics, PNG downloads, and private, local processing.
