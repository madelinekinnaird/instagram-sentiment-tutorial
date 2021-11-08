<img src="https://www.natureinabox.com.my/wp-content/uploads/2014/10/greenwashing-1024x355.png" width="500">

### The aim of this project is to analyze the language and behaviours of [Global 2000](https://www.forbes.com/global2000/#3acf0a88335d) companies around [greenwashing](https://www.sustainablejungle.com/sustainable-living/what-is-greenwashing/). 

## Selected Company Data
-  **Every Global 2000 Company with:**
    - a verified instagram account
    - captions that are 80% in english
- **Instagram posts per company:**
    - every post in 2020

## Instagram Metrics
The [Instagram scraper](https://github.com/madelinekinnaird/quantifying-greenwashing/blob/main/ig_scraper/post-scraper-list.py) built for this project was built using the extraordinary [Instaloader](https://instaloader.github.io/) package. The fifteen attributes below were collected for each companies Instagram feeds. 

```python
post_info = {
				"shortcode": post.shortcode,
				"username": company,
				"date_utc": post.date_utc.strftime("%Y-%m-%d %H:%M"),
				"is_video": "yes" if post.is_video else "no",
				"is_sponsored": post.is_sponsored,
				"hashtags": ",".join(post.caption_hashtags),
				"mentions": ",".join(post.caption_mentions),
				"caption": (emoji.demojize(post.caption)).encode('utf-8'),
				"video_view_count": post.video_view_count if post.is_video else 0,
				"video_length": post.video_duration if post.is_video else 0,
				"likes": post.likes,
				"comments": post.comments,
				"likes+comments": (post.likes + post.comments),
				"location_name": post.location.name if post.location else "",
				"location_latlong": " ".join((str(post.location.lat), str(post.location.lng))) if post.location else ""
				}
```

## Sustainability Score
Each companies ESG & sustainbility score was obtained from the student version of [CSRhub](https://www.csrhub.com/).

![Alternate image text](https://blog.csrhub.com/hs-fs/hubfs/blog/CSRHub%20LLC%20ratings.png?width=790&height=250&name=CSRHub%20LLC%20ratings.png)
