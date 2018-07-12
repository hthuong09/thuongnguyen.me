---
layout: post
author: Thuong Nguyen
title: 'Effortless Watching Anime'
tags: ['en', 'life', 'anime']
description: 'This is how I made "my own anime site"'
---

## Tales of a lazy otacu ;)

### Introduce

This post was taken from [an album on my Facebok](https://www.facebook.com/hthuong09/media_set?set=a.776792712518387.1073741853.100005629950531&type=3).

This post will explain how I could automatically download new anime releases immediately and push it to my server so I can watch it every where on any device. 

To do this I used the following software:
- __Sonarr__: track anime release and find download content (<https://sonarr.tv/>)
- __Deluge__: torrent downloader (<https://deluge-torrent.org/>)
- __Plex__: media content management, it helps to organize, transcode, stream your media on ANY DEVICE. (<https://www.plex.tv/>)
- __Pushbullet__: a cross platform notification. (<https://www.pushbullet.com/>)

### How it works

First, add the series I want to follow to Sonarr. It will monitor and let me know which and when each episode will be aired
![manage anime with sonarr](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/01-add-anime.jpg)

---

Sonarr also give you a release calendar
![sonarr calendar](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/02-sonarr-calendar.jpg)

---

When episode is aired on TV, it will be moved to "Wanted List" on Sonarr.
![sonarr wanted list](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/03-wanted-list.jpg)

---

Sonarr will be constantly looking for download content using configured "Indexers".
When the download content is found, episode will be moved to Queue list and push to download client, in this case, Deluge (Sonarr also support usenet).
![sonnar queue list](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/04-downloading.jpg)

---

File will be pushed to Deluge. Sonarr will continue to track progress of torrent.
![deluge torrent anime](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/05-deluge.jpg)

---

Once the file is downloaded successfully it will be pushed to my Plex library, Plex will automatically look for anime information.
![plex library anime](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/06-plex-anime.jpg)

---

With very detailed information.
![anime overlord on plex](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/07-detail-view.jpg)
Even episode name...
![anime overlord on plex - episode name](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/08-detail-view-2.jpg)

---

In the mean time, Sonarr will tell Pushbulled to send notification to all devices.

On browser.
![pushbullet notification on chrome](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/09-notification.jpg)

On mobile
![pushpullet notification on ipad](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/10-notification-mobile.jpg)

---


What now? Enjoy the new episode, of course. And, did I tell you. You can watch Plex media on ANY DEVICE: Smart phone, tablet, TV, PS4, XBOX, Web... bla bla
![enjoy overlord anime on ipad](https://s3-ap-southeast-1.amazonaws.com/thuongnguyen.me.blog/blog-assets/20180712-effortless-anime-watching/11-enjoy.jpg)

## How do I create one like you?
Well, I'm planning on write a tutorial to do this, probably in Vietnamese, though. If you can't read Vietnamese, Google Translate will help you then :joy:

---

That's it. Until next time :ok_hand: