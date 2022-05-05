# Social Image Signed URLs Integration

You can use Signed URLs to generate your images securely in your website/app.

On your first request to a Signed URL, the signature is validated and, if valid, your image is then generated and returned.

Once you've hit the Signed URL **once**, the generated image is cached at the edge by a global CDN.

# Steps to generate images

1. Retrieve your unique Signed URL base from Social Image - [Go to Step](#Retrieve-your-Signed-URL-base)
2. Combine your Signed URL base, modifications and signature hash [Go to step(#create-a-signed-url)
3. Open the URL / add to your meta tags to generate images on-the-fly.

## Retrieve your Signed URL base

Login to [https://use.socialimage.app](https://use.socialimage.app).

You'll need to create a **Project**, **Template** and **add your Template to your Project**.

You can then go to [https://use.socialimage.app/integrations/signedUrls/create](https://use.socialimage.app/integrations/signedUrls/create) to create a Signed URL.

<img width="838" alt="create-social-image-signed-url" src="https://user-images.githubusercontent.com/4564433/167026501-69f36a87-9798-45fd-85b7-26c93b8508db.png" style="margin: auto;">

## Create a Signed URL

The following is Javascript, but the same principle can be followed in the programming language of your choosing.

Install the required NPM modules with `yarn`:

```bash
yarn install crypto base64url
```

or with `npm`

```bash
npm install crypto base64url
```

```js
import crypto from "crypto";
import base64url from "base64url";
/**
 * Creates a new Social Image Signed URL
 * @param {Array} modifications An Array of modifications to apply to your image template.
 * @param {String} API_KEY Your Social Image API Key
 * @param {String} signedUrl The Signed URL base e.g https://api.socialimage.app/v1/signed/sc1V6yvQzKhb7JzKYVdvdy/image.jpg
 * @param {Boolean} encodeUri Whether to encode the URL or not
 * @returns {String} The signed URL
 */
export const createSignedImageUrl = (
  modifications,
  API_KEY,
  signedUrl,
  encodeUri = false
) => {
  // Sort the list of modifications first by the name property.
  let sortedModifications = modifications.sort((a, b) => a.name - b.name);
  // Craft the query string - base64 encode the sorted modifications array.
  let query =
    "?modifications=" + base64url(JSON.stringify(sortedModifications));
  // Create the signature
  let signature = crypto
    .createHmac("sha256", API_KEY)
    .update(signedUrl + query)
    .digest("hex");
  // Create the query parmaters string.
  // Encode the query parameter string if encodeUri is set to true.
  let queryParamatersString = encodeUri
    ? encodeURIComponent(query + "&sig=" + signature)
    : query + "&sig=" + signature;
  return signedUrl + path;
};
```

Generate your Signed URLs at build time or on the server since your API key is required.

```js
let signedImageUrl = createSignedImageUrl(
  [
    { name: "title", text: content.title },
    { name: "featured_image", url: image },
    { name: "date", url: content.date },
    { name: "author", text: content.author.name },
    { name: "avatar_image", url: authorImage },
  ],
  process.env.SOCIALIMAGE_API_KEY,
  "https://api.socialimage.app/v1/signed/sc1V6yvQzKhb7JzKYVdvdy/image.jpg",
  false
);
```

You can then add the generated Signed URLs in the OG meta tags of your website.

```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@site" />
<meta name="twitter:creator" content="@app_image" />
<meta property="og:type" content="website" />
<meta
  property="og:image"
  content="https://api.socialimage.app/v1/signed/sc1V6yvQzKhb7JzKYVdvdy/image.jpg?modifications=W3sibmFtZSI6ImZlYXR1cmVkX2ltYWdlIiwidXJsIjoiaHR0cHM6Ly9yZXMuY2xvdWRpbmFyeS5jb20vc29jaWFsLWltYWdlLWFwcC9pbWFnZS91cGxvYWQvdjE2NTEzMjA3NDMvc29jaWFsaW1hZ2UuYXBwL3NvY2lhbC1tZWRpYS1nODQ4N2QzZDIzXzE5MjBfcXd4cmM1LmpwZyJ9LHsibmFtZSI6InRpdGxlIiwidGV4dCI6IkhvdyB0byB1c2UgU2lnbmVkIFVSTHMgdG8gZ2VuZXJhdGUgc29jaWFsIHNoYXJlIGltYWdlcyBvbi10aGUtZmx5In0seyJuYW1lIjoiYXV0aG9yX2ltYWdlIiwidXJsIjoiaHR0cHM6Ly9yZXMuY2xvdWRpbmFyeS5jb20vc29jaWFsLWltYWdlLWFwcC9pbWFnZS91cGxvYWQvdjE2NDgxNTI0Njkvc29jaWFsaW1hZ2UuYXBwL2F1dGhvcnMva3lsZV91ZjJmNjguanBnIn0seyJuYW1lIjoiYXV0aG9yX25hbWUiLCJ0ZXh0IjoiS3lsZSJ9LHsibmFtZSI6ImRhdGUiLCJ0ZXh0IjoiMzB0aCBBcHIgMjAyMiJ9XQ&sig=87de36542a7035909d0061c4a53cef4501350ef0f8f1801c9ebd6e46ec7fc353"
/>
```
![84c6ea2d46b6d17f21e878f26e5a47fa](https://user-images.githubusercontent.com/4564433/167030937-1aa82889-fc5f-40f1-95c5-84a6de108280.jpeg)


