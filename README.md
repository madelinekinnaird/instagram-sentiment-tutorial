
### TITLE HERE

Photo here 

We live in a world where social media is constantly analyzed, optimized, and leveraged to perpetuate more engagement (insert lyric from Bo Burnham's Inside here). While parts of this statement are somewhat depressing, there is a lot of opportunity for data scientists to contribute to this engagement analysis cylce. 

This tutorial will walk through how you can put together a python script that allows you to enter an instagram url and render a visualization of that particular post's sentiment. It will do this by walking through the following steps:
- 1. authenticating to an instagram account for instaloader 
- 2. using instaloader to scrape instagram metadata
- 3. using sentiment analysis to label each comment
- 4. display the results 


```python
pip install instaloader

```



## Authenticating to an Instagram Account using Instaloader
Most times if you need to gather large amounts of data from Instagram, it is easiest to use a scraper 

## Instaloader Scraper
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

## Sentiment Analysis
Each companies ESG & sustainbility score was obtained from the student version of [CSRhub](https://www.csrhub.com/).



## Display the Results


## Putting it All Together 
