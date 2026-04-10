# Project Plan: Secure Random YouTube Embed

## Objective
Implement a video player that selects and plays a random video from the **Thomas Grasha** YouTube channel using the YouTube Data API v3.

## 1. Technical Resources
- **Channel ID:** `UC8P0dc0Zn2gf8L6tJi_k6xg` (@thomasgrasha)
- **API Required:** [YouTube Data API v3](https://developers.google.com/youtube/v3)
- **Base Endpoint:** `https://www.googleapis.com/youtube/v3/`

## 2. Security Strategy (Client-Side)
To keep your API key secure on a static site:
1. **HTTP Referrer Restriction:** In Google Cloud Console, restrict the key to your specific domain (e.g., `https://thomasgrasha.com/*`).
2. **API Restriction:** Limit the key to only the "YouTube Data API v3."
3. **Environment Variables:** If using a build tool (Vite, React, etc.), use `.env` files to manage the key, though it will still be bundled in the final JS.

## 3. Implementation Logic
Since the API has no "random" endpoint, follow these steps:

1. **Get Uploads Playlist:** 
   - Call `channels.list?part=contentDetails&id=UC8P0dc0Zn2gf8L6tJi_k6xg`
   - Save the `uploads` ID from `relatedPlaylists`.
2. **Get Total Video Count:**
   - Call `playlistItems.list?part=snippet&playlistId=[UPLOADS_ID]&maxResults=1`
   - Read `pageInfo.totalResults`.
3. **Select Random Video:**
   - Generate a random number between 0 and `totalResults - 1`.
4. **Fetch Target Video:**
   - Use the `nextPageToken` to iterate to the page containing your random index (each page holds up to 50 items).
   - Once on the correct page, extract the `videoId`.
5. **Update Player:**
   - Set the `src` of your `<iframe>` to `https://www.youtube.com/embed/[VIDEO_ID]`.

## 4. Sample Code (JavaScript)
```javascript
const API_KEY = 'YOUR_RESTRICTED_KEY';
const CHANNEL_ID = 'UC8P0dc0Zn2gf8L6tJi_k6xg';

async function fetchRandomVideo() {
  // 1. Get Playlist ID
  const chRes = await fetch(`https://www.googleapis.com/youtube/v3/channels?part=contentDetails&id=${CHANNEL_ID}&key=${API_KEY}`);
  const chData = await chRes.json();
  const uploadsId = chData.items[0].contentDetails.relatedPlaylists.uploads;

  // 2. Get Count
  const plRes = await fetch(`https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&playlistId=${uploadsId}&maxResults=1&key=${API_KEY}`);
  const plData = await plRes.json();
  const total = plData.pageInfo.totalResults;

  // 3. Randomize and Fetch (Simplified for small channels)
  const randomIndex = Math.floor(Math.random() * total);
  // Note: For channels > 50 videos, you must loop through nextPageTokens.
  // ...
}
```

## 5. Next Steps
- [ ] Create a Google Cloud Project.
- [ ] Enable YouTube Data API v3.
- [ ] Generate API Key and apply restrictions.
- [ ] Update `youtube.html` with the logic above.
