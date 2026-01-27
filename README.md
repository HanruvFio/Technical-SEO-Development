# Technical-SEO-Development

## Webflow

### Images
* Always use AVIF Compression
* Use Eager Load for top level images, lazy on scroll for the rest
* Avoid background images, rather:
  * Set div containing image to: position relative, overflow hidden
  * Set image inside div to: object fit:cover, withd and height to 100%, z-index lower than the text (-1 as a default)
* If you have to use

### Fonts
* DO NOT use the google font feature (This fetches fonts from google server)
 * Upload custom fonts
    * Always use Woff2 fonts where possible
    * Swap enabled
  
### CSS and JS Rendering 
* Site settings > Publishing
 * Enable asynchronously load JS, Minify HTML, Minify CSS, Minify JS, Per page CSS
