# HSK Papers Admin Panel — Setup

## 1. Publish the panel

Repository: https://github.com/randirosandi-glitch/Admin-Panel-HSK-Paper

In **Settings → Pages**, choose **Deploy from a branch**, branch **main**, folder **/(root)**, then Save if GitHub still shows that option. The panel URL is:

https://randirosandi-glitch.github.io/Admin-Panel-HSK-Paper/

The GitHub Pages build can take a few minutes after a commit.

## 2. Create a narrowly scoped GitHub token

The panel does not contain a token. Create a fine-grained personal access token in GitHub:

1. GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token.
2. Choose the account that owns the project. Give the token a short expiry that suits your use.
3. Under repository access, choose **Only select repositories** and select exactly:
   - **Beta-HSK-Papers**
   - **latihanhsk**
4. Under repository permissions, grant **Contents: Read and write**. Metadata read is automatically included. Do not grant Administration, Actions, Workflows, or unrelated permissions.
5. Generate the token and copy it once. Paste it into the panel's GitHub token field and tap **Uji koneksi GitHub**.

The token is used from your browser to call api.github.com and is kept only in page memory. It is not written into the repository or browser storage. Reloading/closing the tab clears it. Do not share screenshots that reveal the token.

## 3. One-time Google Drive upload setup

The existing Apps Script deployment can already provide the audio index, but MP3 upload/linking requires the new drive-audio-index.gs code in the Beta repository to be deployed into the Google Apps Script project that owns the **HSK Papers Audio** folder.

1. Open the existing Google Apps Script project associated with the current /exec URL.
2. Replace its editor code with the complete contents of [drive-audio-index.gs](https://github.com/randirosandi-glitch/Beta-HSK-Papers/blob/main/drive-audio-index.gs).
3. In **Project Settings → Script properties**, add:
   - Property: **ADMIN_UPLOAD_KEY**
   - Value: a long random secret of at least 32 characters, generated privately. Do not commit it to GitHub.
4. Click **Deploy → Manage deployments**, edit the existing web-app deployment, choose **New version**, and deploy. Keep **Execute as: Me** and **Who has access: Anyone** so the public audio index can be read by HSK Papers. If you create a separate deployment instead, update the endpoint URL configured in the admin panel's DRIVE_INDEX_URL.
5. In the panel, enter the same secret in **Kunci upload Drive**.

The public GET index reads file names and IDs. POST upload/link operations require the secret; unauthorized upload requests are rejected. Uploaded audio is named H-level-code.mp3, placed in **HSK Papers Audio/HSK N/**, and the individual file is set to **Anyone with the link — Viewer**. The parent folder is not made public.

## 4. Test the workflow

- Open the /exec URL in a private browser. It should return JSON containing a files object.
- In the admin panel, refresh the catalog. It merges JSON package lists from Beta and Stable, with the Beta package taking precedence when the same code exists in both.
- Upload one valid JSON to **Beta** first. The file is saved under the repo's existing layout, and its code should appear in the refreshed catalog.
- Upload a small MP3 (12 MB maximum per file) or link an existing Drive MP3. Wait for the panel to verify that the audio appears in the Drive index.
- Confirm that the application can play and seek in the audio. For large audio files, upload to Drive manually first, then use the file link in the panel.

## Data layout and safety

- Beta package JSON usually lives at data/HSK N/CODE.json.
- Stable currently contains legacy flat paths such as data/H41001.json; the app catalog supports both flat and level-folder layouts. The panel updates an existing package at its current path rather than creating a duplicate when it can identify it.
- The JSON destination defaults to **Beta**. Choosing **Stable** is explicit and should be treated as a live change.
- The panel does not copy every upload to both repositories automatically.
- GitHub Pages is static and cannot securely deploy Google Apps Script code or store a private token. The one-time Script Property and deployment steps above must be completed by the Google account owner.
