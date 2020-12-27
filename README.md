# Jekyll Docker image with Imagemagick6 instead of ImageMagick7

This Docker image extende [jekyll/jekyll:stable](https://hub.docker.com/r/jekyll/jekyll/) to use ImageMagick6 instead of ImageMagick7.

# Build

Build the image with

```
docker build . -t dgraziotin/jekyll-imagemagick6:stable
```

> You can target any Jekyll version [as available on Docker Hub](https://hub.docker.com/r/jekyll/jekyll/tags), just replace the instances of the word `stable` in the Dockerfile and the build command with the tag of interest.)

# Pull

Instead of building the image, you can pull it from the Docker Hub registry.

```
docker pull dgraziotin/jekyll-imagemagick6:stable
```

# Run 

Run your new image with the following command:

```
/usr/bin/env docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -it dgraziotin/jekyll-imagemagick6:stable \
  jekyll build
```

And that is it! You can serve your Jekyll site locally with:

```
/usr/bin/env docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -p 4000:4000 \
  -it dgraziotin/jekyll-imagemagick6:stable \
  jekyll serve
```

The site will be reachable at http://127.0.0.1:4000.

The container will behave the same as [jekyll-docker](https://github.com/envygeeks/jekyll-docker/blob/master/README.md). Head there to learn more.

# Why? 

If you are using Jekyll under macOS, several issues might be happening right now:

1. Something is broken under Big Sur.
2. HomeBrew installs Ruby 3.x.x, for which something is not ready yet.
3. A plugin requires ImageMagik6 or RMagick (like the excellent [wildlyinaccurate/jekyll-responsive-image](https://github.com/wildlyinaccurate/jekyll-responsive-image).

Issues 2 and 3 might even happen if you are using GNU/Linux, by the way.

After several downgrade and upgrade tries, I got sick of trying to `brew` and `rvm` the issue. I moved to docker for this, too.

Luckily, there is [jekyll-docker](https://github.com/envygeeks/jekyll-docker/blob/master/README.md) for this. This is a wonderful image. It even allows you to add Alpine Linux packages to it by specifying their name in a `.apk` file. This fixes issue 1 and issue 2.

Unfortunately, jekyll-docker does not fix issue 3, because it [installs ImagaMagick7 by default](https://github.com/envygeeks/jekyll-docker/blob/master/repos/jekyll/Dockerfile#L82). Using it as-is will result in the following error:

```
ruby 2.7.1p83 (2020-03-31 revision a0c7c23c9c) [x86_64-linux-musl]
Configuration file: /srv/jekyll/_config.yml
 Theme Config file: /usr/gem/gems/no-style-please-0.1.0/_config.yml
            Source: /srv/jekyll
       Destination: /srv/jekyll/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
  Liquid Exception: NoDecodeDelegateForThisImageFormat `JPEG' @ error/constitute.c/ReadImage/562 in /srv/jekyll/_posts/2011-04-10-contributing-on-arora-browser-quickview-mode.md
                    ------------------------------------------------
      Jekyll 4.1.1   Please append `--trace` to the `build` command
                     for any additional information or backtrace.
                    ------------------------------------------------
Traceback (most recent call last):

[...]

/usr/gem/gems/jekyll-responsive-image-1.5.5/lib/jekyll-responsive-image/image_processor.rb:12:in `read': NoDecodeDelegateForThisImageFormat `JPEG' @ error/constitute.c/ReadImage/562 (Magick::ImageMagickError)
```

This image will revert to ImageMagick6 and fix the issues.