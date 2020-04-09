# [beefed.it](https://beefed.it)

## Add content

Into the `tmp/` directory, download files:

```bash
pushd tmp
  wget  https://i.imgur.com/bVKK2wN.mp4 \
        https://i.imgur.com/nDtqWWC.gif \
        https://i.imgur.com/52nBiEe.mp4 \
        https://i.imgur.com/JoiI1Qi.gif \
        https://i.imgur.com/wDWcBwe.mp4
popd

rake process
```

The `process` task will process the files in `tmp/`:
* mp4 files will be converted into a gif in `img` with `ffmpeg` and the mp4 will be deleted
* gif files will be moved into `img/`

Obviously, you will need `ffmpeg` to installed on the system to actually process mp4 files.

Next, generate the new index.html:

`rake gen`

This interpolates the list of images in `img/` into the erb into index.html.

## Deploy changes

Just run `rake sync` to rsync the files to sinope. It's fine.

## NOTE

Do NOT run the tasks from either `img` or `tmp`
