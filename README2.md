
# Installation

If using virtual env:

		# In project directory
		virtualenv -p python3 env
		. env/bin/activate
		pip install -r requirements.txt


If not using using virtualenv :

		# In project directory
		sudo pip install -r requirements.txt

# Classes

## Connection:

		import albatross
 		etiConn = albatross.Connection(username="LlamaGuy", password="hunter2")`

##### Properties:
* `etiUp` - checks if ETI is up
* `loggedIn` - checks if you're still logged in

##### Functions:
* `topics` - Returns TopicList (see [TopicList](#TopicList) class)
	* in: `allowedTags` (list) - List of allowed tag names.
	* in: `forbiddenTags` (list)  List of forbidden tag names

* `topic` - Returns a Topic specified by the id (see [Topic](#Topic) class)
	* in: `id` (int) - Topic id
	* in: `page` (int, default=1)

* `tags` - Returns TagList (see [TagList](#TagList) class) (note: this class appears to be broken as the tags list is now loaded via javascript post page load, so the list is emtpy when bs4 attempts to parse)
	* in: `tags` - Same as `allowedTags`
	* in: `active` (bool, default=False)

* `tag` - Returns a Tag (see [Tag](#Tag) class)
	* in: `name` (str) - name of tag. e.g. `etiConn.tag("LUE")`

* `post` - Returns a Post (see [Post](#Post) class)
	* in: `id` (int) - post id
	* in: `topic` (Topic) - instance of Topic (see [Topic](#Topic) class)

* `user` - Returns a User (see [User](#User) class)
	* in: `id` (int) - id of user e.g. `etiConn.user(9017)`

* `users`- Returns UserList (see [UserList](#UserList) class)

* `image` - Returns Image (see [Image](#Image) Class)
	* in: `md5`
	* in: `filename`
	* e.g.: `etiConn.image('34f635c1ea464c463c8c8f5c8578f658', 'bit.jpg')`

* `inbox` - Returns PMInbox (see PMInbox class)
* 
* `pmThread` - Returns PMThread (See PMThread class)

* `pm` - Returns PM (See PM Class)


## Image:
Image information retrieval and manipulation.

```etiConn.image('34f635c1ea464c463c8c8f5c8578f658', 'bit.jpg')```

##### Properties:
* `relatedCount` - Returns a count of images that are related to this image.
* `topicCount` - Returns a count of topics containing this image.

##### Functions:
* `related` - Returns a list of images that are related to this image.
	* in: `maxPage`

* `topics` - Returns list of Topics (see [Topic](#Topic) class) which this image is in
	* in: `maxPage` (int, default=None)


## Post:
Post information retrieval and manipulation.

Using `print(post)` returns a formatted string with the id, user info, date, post content and sig.
##### Properties:
* `id` - Returns post id
* `topic` - Returns a Topic object (see [Topic](#Topic) class)
* `date` - Returns a python `datetime` object.
* `user` - Returns a User object (see [User](#User) class)
* `html` - Returns the post content.
* `sig` - Returns the sig
* `replies` (int) - Reply count

## Tag:
Tag information retrieval and manipulation.

Using `print(tag)` returns a formatted string with the tag name, tag staff, related, forbidden and dependent tags.

##### Properties:
* `staff` -  Returns a json array of `{name: username role: 'administrator|'moderator'}`
* `description` 
* `forbidden` - List of forbidden tag names, comma separated
* `related`
* `dependent`

##### Functions:
* `topics` - Returns list of Topic (see [Topic](#Topic) class) under that tag


## TagList (broken)

Tag list information retrieval and manipulation.

##### Properties:
* `tags` - Returns list of Tags (see [Tag](#Tag) class)


##### Functions:
* `topics` - Returns list of topics (see [TopicList](#TopicList) class)


## Topic:

```topic = etiConn.topic(12345)```

Using `print(topic)` prints a formated string with the ID, title, tags, page count, posts count  and date.
##### Properties:
* `id` - Topic id
* `page` - Current page number, if provided
* `closed` (bool)
* `archived` (bool)
* `date` - Returns python `datetime` object
* `title` (str)
* `user` - Returns User object, user being TC (see [User](#User) class)
* `pages` (int) - number of pages
* `postCount` (int)
* `tags` - list of Tags (see [Tag](#Tag) class)
* `lastPostTime` - Returns the time of the last post, useful for how pagination works on ETI
* `csrfKey`
* `images` - List of Images in the topic (see [Image](#Image) class)

##### Functions:
* `getPosts`
	* in: `endPageNum` (int) - stop searching after this page
	* in: `user` (User object) - Filters by user
	* NOTE: This function is weird, in that it doesn't actually return the list of posts, but sets the `posts` property. Use it as such:
			
			topic.getPosts(user=user)
			posts = topic.posts()
	
* `posts` - Returns list of Post objects (see [Post](#Post) class)
	* in: `page` (int, default=None) - Page number. By default grabs posts from all pages

* `make_post` - posts to ETI, in the provided topic
	* in: `html` (str) - The content you wish to post.
	* e.g.:
			
			topic = etiConn.topic(12345)
			topic.make_post('this is a post')


## TopicList:

```topics = etiConn.topics(allowedTags=["LUE"], forbiddenTags=["Anonymous"]).search()```

Note: Probably have to understand ETI pagination to understand this class a bit.

		http://boards.endoftheinter.net/topics/LUE?ts=1544943527&t=9788211

The `ts` parameter is an epoch time, and if found will list the first 50 topics of that tag after that time. The `t` optional parameter is the last topic ID from the previous page. 

##### Properties:
* `topics` - List of Topics (see [Topic](#Topic) class). Pretty much only search should be used on this class though.

##### Functions:
* `search` - Returns a list of topics (see [Topic](#Topic) class)
	* in: 	`query` (str, default="") - Serach  query. By default will search all topics
	* in: `maxTime` (datetime, default=None)  - sets the `ts` parameter
	* in: `maxID` (int, default=None) - sets the `t` parameter
	* in: `activeSince` (datetime, default=None) - `Gets all topics made after the set date`
	* in: `topics` (list,default=[]) - List of Topics, which to append the new topics too
	* in: `recurse` (bool, default=False) - If False, returns 50 topics (1 page worth), otherwise will return all topics under that tag, until `activeSince`.
	* e.g. etiConn.topics(allowedTags=["LUE"], forbiddenTags=["Anonymous"]).search()
		* This

## User:

```user = etiConn.user(9017)```

##### Properties:
* `name`
* `level` - Your custom title, I think
* `formerly` - The formerly known as string
* `banned` (bool)
* `suspended` (bool)
* `reputation` (dict) - key is Tag object, value is reputation number
* `tokens` (int)
* `goodTokens` (int)
* `badTokens` (int)
* `created` (datetime)
* `active` (bool)
* `lastActive` (datetime)
* `sig` (str)
* `quote` (str)
* `email` (str)
* `picture` (str) - link to avatar

##### Functions:
* `send_pm` - Sends a PM to the user
	* in: `subject` (str)
	* in: `message` (str)
	* e.g.: `user.send_pm('Title', 'This is a PM')`

## UserList

```literally_all_users = etiConn.users().search(recurse=True)```

##### Properties:
* `users` - List of User objects (see [User](#User) class). Pretty much just search should be used on this class though.

##### Functions:
* `search`- Searches for users using given parameters, and returns the current user listing object. Performs operation in parallel. Returns list of Users
	* in: `query`(str, default="")
	* in: `maxID` (int, default=None)
	* in: `activeSince` (datetime, default=None)
	* in: `createdSince` (datetime, default=None)
	* in: `startPageNum`(int, default=None)
	* in: `endPageNum` (int, default=None)
	* in: `recurse` (bool, default=False)
3