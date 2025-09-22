# Problem Set 2: Composing Concepts

## Concepts for URL Shortening

### 1. Contexts:
- The context is a collection that we want to have unique elements, so it's to ensure that for each group/base/website, we have strings that are not the same. In the case of URL shortening, a coolection will probably be defined by the URL base, so that each shortening app/website/tool (each defined by a specific base) has a collection of strings that are all unique.

### 2. Storing used strings:
- Because otherwise, it might generate a string that was generated and used within some context. 
- In the counter case, the counter can present an index in an arbitrary hash function that generate a string based on an integer as an input. So the set will have  alength equal to the counter, and it's elments will be h(1),...,h(counter) where h is the hash funtion.

### 3. Words as nonces:
- It can be advantageous beacuase it's easier to remember by the user and whomever they're gonna share the shortURL with (might potentially have name related to the content of the orignal URL).
- However, this might cause the length to become large as the number of links for similar websites increases.
- For each context, you can have a a dictionary object associated with it that specify the collection of words that generate can use.


---


## Synchronizations for URL Shortening

### 1. Partial matching:
- Because in the first sync, we just want to generate a new nonce for targetURL and we're doing nothing with targetURL for now, so we don't need it. But in the second sync, we are using targetURL to bind its destination to the new shortURL.

### 2. Omitting names:
- Because when the name of the argument and the return value is different, it might not be clear what we would be referring to by the name we're keeping, so we might confuse a name for a return value and end upsearching for it to realize that it's just the variable name

### 3. Inclusion of request:
- Because we only need the output of the register method (shortURL) to set the expiry date, and we don't need to wait anymore for a shortBase or something else (we are guarnteed to have gotten everything we need by getting the full shortURL)

### 4. Fixed domain:
- We wouldn't need to wait the shortURLBase variable so I'll remove it from the requests, and specify that it's return value is the constant one (and change context to it).
- I would potentially remove the first sync after automitating the genration via the counter idea, and make the register function use the counter. Thus, whenever we get a targetURL to shorten, we knows beforehand the suffix to add.

### 5. Adding a sync:
- **sync** handleExpiry
    - **when** ExpiringResource.expireResource() :(resource: shortURL)
    - **then** UrlShortening.delete(shortUrl)


---


## Synchronization Questions

### 1. Concepts Specs:

### countVisits:
- **concept** countVisits[location]
- **purpose** records the visits of a certain location (physical or digital)
- **principle** each visit to the location would increment the record of visits
- **state**
    a set of locations with:
    * a counter Number
- **actions**
    * increment (location)
      + **requires** the location exists in the set
      + **effects**  add 1 to the value of the counter
    * getCount (location): (count: Number)
      + **requires** the location exists
      + **effects**  returns the counter value of the location
    * intialize (location)
      + **effects**  add location to the set with counter 0

### access:
- **concept** access[resource]
- **purpose** manages the access of certain resources
- **principle** a user is regitered to have access to certain resources
- **state**
    a set of Users with:
    * a set of resources
- **actions**
    * add (resource, u: User)
      + **effects**  adds the resource to the set user u have
    * checkAccess (resource, u: User): (ownership: Boolean: Number)
      + **requires** the user u has access to teh resource


### 2. Syncs Specs:

- **sync** assignUser
    - **when** 
      + UrlShortening.register (shortUrlSuffix, shortUrlBase, targetUrl: String): (shortUrl: String)
      + Request.countVisits(shortUrl, user: User)
    - **then**
      + countVisits.intialize(shortUrl)
      + access.add(shortUrl, user: User)

- **sync** countIncrease
    - **when** UrlShortening.lookup(shortUrl)
    - **then** countVisits.increment(shortUrl)

- **sync** checkAnalytics
    - **when** access.checkAccess(shortUrl, u: User)
    - **then** countVisits.getCount(shortUrl)


### 3. Requests Analysis:

**feat1:** I'd modify the NonceGeneration concept to have an action that allow writing a nounce and accepting it as long as it's not in the set of used nounces

**feat2:** We wouldn't want it, as it may defeat the purpose of having short urls as explained in problem 1.3

**feat3:** that seems like more of a UI problem and not something that needs a change in our concept design

**feat4:** add a dictionary to the NonceGeneration that only include "not easily guessed" words, and enforce only generating words that are in that dictionary

**feat5:** we could assign the user to the resource by the IP address of their device.