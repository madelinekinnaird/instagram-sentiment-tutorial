
### TITLE HERE

Photo here 

We live in a world where social media is constantly analyzed, optimized, and leveraged to perpetuate more engagement (insert lyric from Bo Burnham's Inside here). While parts of this statement are somewhat depressing, there is a lot of opportunity for data scientists to contribute to this engagement analysis cylce. 

This tutorial will walk through how you can put together a python script that allows you to enter an instagram url and render a visualization of that particular post's sentiment. It will do this by walking through the following steps:
- 1. authenticating to an instagram account for instaloader 
- 2. using instaloader to scrape instagram metadata
- 3. using sentiment analysis to label each comment
- 4. display the results 

Before we get started you'll want to install the instaloader package.
```python
pip3 install instaloader

```
Instaloader's [documentation](https://instaloader.github.io/) is pretty good and if you plan to work with a lot of Instagram data it is a worthwhile read. 

For getting started, the simplest way to set up Instaloader is through the command line and using an account username and password (below). This method does work but I have found lots of issues to arise, especially if you are scraping large amounts of data. A more robust way to authenticate and load a saved Instagram session through firefox. I'll walk through this in the next section. 
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



## Authenticating to an Instagram Account using Instaloader
Here I'll walk through the code that will allow you to quickly and easily set up a powerful Instagram scraper. First, import the following packages (and install as neccessary). 
```python
from glob import glob
from os.path import expanduser
from sqlite3 import connect

from instaloader import ConnectionException, Instaloader
```
In order for this code to work, you'll need to be logged into the specified instagram account on firefox at runtime. I recommend creating a burner instagram account for scraping so your personal account does not get timed out. 
![image](https://user-images.githubusercontent.com/14099908/140822121-10564cc1-bd6f-469e-b92c-ed45647b9cf9.png)

Once you're logged in you'll be able to load the firefox session for instaloader. You'll need to find the path to your firefix cookies database on your local machine. For a Windows machine you can use [this guide](https://www.digitalcitizen.life/cookies-location-windows-10/) to find the file you will need which will end with `cookies.sqlite`. This is generally where the path will be: `C:/Users/YOUR_USERNAME\AppData\Roaming\Mozilla\Firefox\Profiles\YOUR_PROFILE_FOLDER/cookies.sqlite`

![image](https://user-images.githubusercontent.com/14099908/140823874-d729675d-bc03-4885-b050-5e2f81b28610.png){ width=50% }


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


## Instaloader Scraper
Hooray! Now you are authenicated to Instagram and you can easily use Instaloader from your pytyhon IDE without having to think about your login again!! 

Now you are easily able to initiate instaloader and login.

```python
## initiating instaloader
instagram = instaloader.Instaloader()

## login to saved session
instagram.load_session_from_file('IG_USERNAME_LOGGED_INTO_FIREFOX_HERE_')
```
Once you are logged you are able to pull data of varying granularities from Instagram. You can pull account-level data or post-level data. In this example we'll be looking at individual Instagram posts using shortcode. Shortcode is the unique string of the post's url. 

As an example, we'll be looking at and analyzing comments on NPR's instagram posts. Here is the post and URL of a story about a squid games themed flash mob in the Philippines that promoted social distancing and mask wearing. We can see the shortcode highlighted at the top in the URL, `CV5WqggMDCb`.

![image](https://user-images.githubusercontent.com/14099908/140842335-f61b802c-c294-4691-9222-a741ee05c51f.png)

We can use this specific shortcode to scrap all of the comments from that specific post. 

```python
## direct instaloader to correct post using it's shortcode
SHORTCODE = 'CV5WqggMDCb'
post = instaloader.Post.from_shortcode(instagram.context, SHORTCODE)

## get comment metadata from the post
for x in post.get_comments():
        post_info = {
        "post_shortcode":post.shortcode,
        "commenter_username": x.owner,
        "comment_text": (emoji.demojize(x.text)).encode('utf-8', errors='ignore').decode() if x.text else "",
        "comment_likes": x.likes_count
        }
```
This code will return something like:



## Sentiment Analysis

```python
def basic_clean(text):
  """
 def getPolarity(text):
   return TextBlob(text).sentiment.polarity

df['text_polarity'] = df['comment_text'].apply(getPolarity)
  ```


## Display the Results


## Putting it All Together 
