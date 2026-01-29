# Technical-SEO-Development

## Webflow

### LCP - Best Practices (Identify Where the bottle neck is)
<img width="1916" height="1067" alt="image" src="https://github.com/user-attachments/assets/8840d223-e1ae-4a94-9294-bf00b78b14aa" />

* Time to Byte (HTML Document)
* Resource load delay (Stylesheet) -> should get this as small as possible
* Respirce load time (Image Resource)
* Element Render Delay (the time your resource is done loading and is then rendered to the screen)

#### Goals for LCP
   * Spend bulk of time fetching the LCP resource
   * Minimize all other time as much as possible
<img width="1363" height="798" alt="image" src="https://github.com/user-attachments/assets/58e1e98b-cecc-489f-928e-a730abd313df" />

#### Recipe or LCP oprimization:
* Step 1: Eliminate uneccessary Resource Load Delay
  * Ensure the LCP resource starts loading as early as possible
    * Add priority hints or preload
    * LCP image should have eager and not lazy load
    * minimize network contention
    * use same-origin resources (if possible)
<img width="1897" height="1062" alt="image" src="https://github.com/user-attachments/assets/65a029c0-9950-486d-b60d-7eba1de84789" />

* Step 2: Eliminate unneccessary element render delay
  * Ensure the LCP element can render immediately after its resource has finished loading (no matter when that happens)
    * Remove or minimize render blocking stylesheets
    * Defer render blocking JS
    * Use font-display optional
    * Ensure nothing is blocking the rendering after the LCP resource is done loading
      * Try service side rendering and not client side for JS scripts
     
<img width="1898" height="1055" alt="image" src="https://github.com/user-attachments/assets/81c36f4d-ff5d-4d99-91f9-27ad44a99325" />

* Step 3: Reduce resource load time
  * Reduce LCP resource load times as much as you can without compromising quality
    * Compress to AVIFF
    * Properly size images
    * Set far-future cache expiry headers
    * Use a CDN to reduce network distance
    * Reduce the css file zise smaller than the LCP element
<img width="1905" height="1064" alt="image" src="https://github.com/user-attachments/assets/6aaf4818-3dc8-4ff0-912e-476f8f0edc36" />

* Step 4: Reduce Time to first byte
  * Deliver the initial page as quickly as possible
    * USE CDN to reduce network distance
    * Upgrade seerver hardware
    * Ensure response bodies can be streamed





### Fonts
* DO NOT use the google font feature (This fetches fonts from google server)
 * Upload custom fonts
    * Always use Woff2 fonts where possible
    * Swap enabled
  
### CSS and JS Rendering 
* Site settings > Publishing
 * Enable asynchronously load JS, Minify HTML, Minify CSS, Minify JS, Per page CSS
