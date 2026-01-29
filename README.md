# Technical-SEO-Development

## Webflow

### LCP - Best Practices (Identify Where the bottle neck is)
<img width="1916" height="1067" alt="image" src="https://github.com/user-attachments/assets/8840d223-e1ae-4a94-9294-bf00b78b14aa" />

* Time to Byte (HTML Document)
* Resource load delay (Stylesheet) -> should get this as small as possible
* Respirce load time (Image Resource)
* Element Render Delay (the time your resource is done loading and is then rendered to the screen)


### Fonts
* DO NOT use the google font feature (This fetches fonts from google server)
 * Upload custom fonts
    * Always use Woff2 fonts where possible
    * Swap enabled
  
### CSS and JS Rendering 
* Site settings > Publishing
 * Enable asynchronously load JS, Minify HTML, Minify CSS, Minify JS, Per page CSS
