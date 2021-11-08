
### TITLE HERE

Photo here 

We live in a world where social media is constantly analyzed, optimized, and leveraged to perpetuate more engagement (insert lyric from Bo Burnham's Inside here). While parts of this statement are somewhat depressing, there is a lot of opportunity for data scientists to contribute to this engagement analysis cylce. 

This tutorial will walk through how you can put together a python script that allows you to enter an instagram url and render a visualization of that particular post's sentiment. It will do this by walking through the following steps:
- 1. authenticating to an instagram account for instaloader 
- 2. using instaloader to scrape instagram metadata
- 3. using sentiment analysis to label each comment
- 4. display the results 

First you'll want to install instaloader.
```python
pip3 install instaloader

```
Instaloader's [documentation](https://instaloader.github.io/) is pretty good and if you plan to work with a lot of Instagram data it is worthwhile to read through. 

The simplest way to get started with instaloader is through the command line and using an account username and password: 
```python
import instaloader

# Get instance
L = instaloader.Instaloader()

# Optionally, login or load session
L.login(USER, PASSWORD)        # (login)
L.interactive_login(USER)      # (ask password on terminal)
L.load_session_from_file(USER) # (load session created w/
                               #  `instaloader -l USERNAME`)
```

I've found that this method that lots of issues arise, especially if you are scraping large amounts of data. A more robust way to authenticate is depcited below. It is not necessary to use instaloader, but I have found it to be the best method. 

## Authenticating to an Instagram Account using Instaloader
Here I'll walk through the code that will allow you to quickly and easily set up a powerful instagram scraper. First, import the following packages (and install as neccessary). 
```python
from glob import glob
from os.path import expanduser
from sqlite3 import connect

from instaloader import ConnectionException, Instaloader
```
In order for this code to work, you'll need to be logged into the specified instagram account on firefox at runtime. I recommend creating a burner instagram account for scraping so your personal account does not get timed out. 
![image](https://user-images.githubusercontent.com/14099908/140822121-10564cc1-bd6f-469e-b92c-ed45647b9cf9.png)

Once you're logged in you'll be able to load the firefox session for instaloader. You'll need to find the path to your firefix cookies database on your local machine. For a Windows machine you can use [this guide](https://www.digitalcitizen.life/cookies-location-windows-10/) to find the file you will need which will end with `cookies.sqlite`.


This is generally where the path will be: `C:/Users/YOUR_USERNAME\AppData\Roaming\Mozilla\Firefox\Profiles\YOUR_PROFILE_FOLDER/cookies.sqlite`
``` python
path_to_firefox_cookies = "C:/Users/YOUR_USERNAME\AppData\Roaming\Mozilla\Firefox\Profiles\YOUR_PROFILE_FOLDER/cookies.sqlite"
FIREFOXCOOKIEFILE = glob(expanduser(path_to_firefox_cookies))[0]
```

Once you are both logged into Instagram on Firefox and have located the cookies on your local machine you can use the following code to load that session's cookies and save it to a file for later use:

```python

## only allow one attempt for session connection
instaloader = Instaloader(max_connection_attempts=1)

## get cookie id for instagram
instaloader.context._session.cookies.update(connect(FIREFOXCOOKIEFILE)
                                            .execute("SELECT name, value FROM moz_cookies "
                                                     "WHERE host='.instagram.com'"))
## check connection
try:
    username = instaloader.test_login()
    if not username:
        raise ConnectionException()
except ConnectionException:
    raise SystemExit("Cookie import failed. Are you logged in successfully in Firefox?")

instaloader.context.username = username

## save session to instaloader file for later use
instaloader.save_session_to_file()
```

## only allow one attempt for session connection
instaloader = Instaloader(max_connection_attempts=1)

## get cookie id for instagram
instaloader.context._session.cookies.update(connect(FIREFOXCOOKIEFILE)
                                            .execute("SELECT name, value FROM moz_cookies "
                                                     "WHERE host='.instagram.com'"))
## check connection
try:
    username = instaloader.test_login()
    if not username:
        raise ConnectionException()
except ConnectionException:
    raise SystemExit("Cookie import failed. Are you logged in successfully in Firefox?")

instaloader.context.username = username

## save session to instaloader file for later use
instaloader.save_session_to_file()
```

## Instaloader Scraper
The [Instagram scraper](https://github.com/madelinekinnaird/quantifying-greenwashing/blob/main/ig_scraper/post-scraper-list.py) built for this project was built using the extraordinary  package. The fifteen attributes below were collected for each companies Instagram feeds. 

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
