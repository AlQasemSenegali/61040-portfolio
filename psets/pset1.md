# Problem Set 1: Reading and Writing Concepts

## Exercise 1: Reading a concept

### Invariants:
1. The count of items in all requests in all registries is nonnegative
2. All purchases has a request they're under that share the same item as them

- The second invariant is more important, since it insure that our gift regisrty is storing for which person has a certain purchac been made
- The **purchace** action is the one most affected by it since it should make sure that it's creating a purchace linked to the asked registry user. It preserve it by **requiring** the existence of a request with the same item under the regisrty being purchaced for.

### Fixing an action:
- The **removeItem** action might break it, since it an remove an item that has purchaces under that, thus redering some purchaces being linked to nothing. This could be fixed by making the **removeItem** action removes the purchaces with the same item as well.

### Inferring behavior:
- It's allowed, since a registry doesn't get deleted after closing it. We might allow this if the gift reciever wants to become active in a later period of time for a different occasion (next year birthday, etc.)

### Registry deletion:
- Probably not. Since the space issue wouldn't be that big as a user of the system would probably want to use it again, so having the regisrty archived (closed) but not deleted would help in just needing to activate it again. 

### Queries:
1. A registry owner might query the purchaces made for a certain item so they see what items they requested have been bought for them.
2. A giver of a gift might query the count of an item in a certain user's registry so they know how much they the registry's owner wants and make their purchace accordingly.

### Hiding purchases:
- I would to the state a **visible** flag under each regisrty, and will add two actions, one for making an invisible registry visible, and one to do the opposite.

### Generic types:
- Becuase we want to acheive modularity within concepts. Under the giftRegistration concept, we shouldn't care about the item's price or functionality, since these aren't essential elements for the registration aspect of giftsm and they can be modeled by other concepts in vase they're needed.


---


## Exercise 2: Extending a familiar concept

### concept specs: 
- **concept** PasswordAuthentication
- **purpose** limit access to known users
- **principle** after a user registers with a username and a password,
they can authenticate with that same username and password
and be treated each time as the same user
- **state**
    a set of Users with:
    * a username
    * a password
- **actions**
    * register (username: String, password: String): (user: User)
    + **requires** no current user has this username
    + **effects**  add a user to the list of users with the given username and password and return it
    * authenticate (username: String, password: String): (user: User)
    + **requires** a user with the given name and password exists
    + **effects**  return that user

### Invariant: 
- No two users has the same username. It's held by the precondition set on the **register** action.

### Email Confirmation:
- **concept** PasswordAuthentication
- **purpose** limit access to known users
- **principle** a user registers with a username and a password and email, then a secret token is created for the user to confirm their email. after the user confirms their email, they can authenticate with that same username and password and be treated each time as the same user
- **state**
    * a set of Users with:
      + a username
      + a password
      + a confirmed flag
      + a SecretToken token
- **actions**
    * register (username: String, password: String): (user: User, token: SecretToken)
    + **requires** no current user has this username
    + **effects**  add a user to the list of users with the given username and password, make them unconfirmed, and return them with a secret token _token_ to be added as well
    * authenticate (username: String, password: String): (user: User)
      + **requires** a user with the given name and password exists and is confirmed
      + **effects**  return that user
    * confirm (username: String, token: SecretToken)
      + **requires** a user with the given name exists, unconfirmed, and with a SecretToken token
      + **effects**  make the user confirmed


---


## Exercise 3: Comparing concepts

### Personal Access Token Specs:
- **concept** PersonalAccessToken
- **purpose** allow and automate access to certain resources
- **principle** a user request the creation of a token, specifying an expiration date and scopes of access. Then, a user can use the token to authinticate logging in the service, and then to read/write from a certain repo.
- **state**
    * a set of Users with:
      + a username
      + a set of Tokens

    * a set of Tokens with:
      + a name
      + an expiration Date
      + an accessed Flag
      + a set of Scopes
- **actions**
    * createToken (user: User, expirationDate: date, scopes: Scope[]): (tokenName: String)
      + **effects**  adds a token to the user's list that expire at expirationDate and has the set of scopes _scopes_, and return the tokenName generated by the service.
    * authenticate (username: String, tokenName: String, expirationDate: Date, today: Date): (user: User)
      + **requires** a user exists with username and a token whose name is tokenName and today's date isn't past the expiration date
      + **effects**  return that user and make the token accessed
    * unaccess (token: Token)
      + **requires** the token is accessed
      + **effects** unaccess the token

### The main change from passwordAutintication is that:
- The token name is generated by the service after registerarion has been done
- A token is used to allow access only to a poriton of resources and actions 

I would highlight the second difference in the beginning of the reading in github, becuase it's the main functional difference that the user getsto interact with mostly.


---


## Exercise 4: Defining familiar Concepts

### 1. Conference Room Booking
- **concept** conferenceBooking
- **purpose** reserving a conference room
- **principle** a user select a date, a room, and a time slot (start and end time) from the availables, then a booking created for them. They can afterwards edit or cancel the booking.
- **state**
    * a set of Bookings with:
      + a user
      + a room
      + a duration
    * a set of Rooms with
      + a set of available durations
    * a set of durations with:
      + a day
      + a start time
      + an end time 
- **actions**
    * reserve (user: User, room: Room, start: Time, end: Time, day: Day):
      + **requires** the room on day has an available duration from at least earlier than start to at least later than end
      + **effects**  makes a reservation for user from start to end on day for room, and adjust the available duration of room on day that included (start, end) not to include it
    * edit (user: User, booking: Booking, newDuration: , newRoom: Room)
      + **requires** the user is the one assigned to the booking and newDuration is available under the booked room
      + **effects**  delete the booking and make a new reservation under the newRoom with the newDurarion, and adjust the availble duration on the newDay to to include newDuration
    * cancel (user: User, booking: Booking)
      + **requires** the user is the one assigned to the booking
      + **effects**  remove the booking from the list of bookings, and add the duration of that booking to the available durations of that room 
    * addTime (room: Room, day: Day, start: Time, end: Time)
      + **requires** there's no duration under a romm on day that emcapsulate the (start, end) period
      + **effects**  if the period intersect with another available period, extend that period with (start, end), else just add (start, end) with day as an available duration under room
- Note: the duration concept/object can either rfer to the booked duration, or an available duration to book. For a certain room and day, all available (start, end) times have a space between them (otherwise we can just merge them together)


---


### 2. Electronic Boarding Pass
- **concept** boardingPass
- **purpose** store and authinticate through boarding passes for trips
- **principle** a user (plane/train agents) can add a boarding pass to a traveller (another user), then an agent can edit or cancel the boarding pass based on the realtime trip info. Also, a traveller can authinticate using the boarding pass.
- **state**
    * a set of Users with:
      + a set of Boarding Passes
    * a set of BoardingPasses with:
      + a trip
      + an active Flag 
    * a set of Trips with: 
      + a date
      + a boarding Time
      + a boarding Gate 
      + activation Time    
- **actions**
    * createBoarding (traveller: User, date: Date, time: Time, gate: Gate, trip: Trip): (boarding: Boarding)
      + **requires** there isn't a boarding pass for the trip under the user
      + **effects** add a boarding pass for the trip under the user with the trip info. 
    * activate (pass: BoardingPass, current: Time)
      + **requires** pass isn't active and the activation time is less than current. 
      + **effects**  activate the pass
    * authinticatePass (user: User, current: Time, pass: BoardingPass)
      + **requires** current is at least after the boarding time


---


### URL Shortener
- **concept** urlShortener
- **purpose** decreasing the length of urls
- **principle** a user inputs a url to be shortened, then the system generate a random, shorter url that delievers to the same website of the original url
- **state**
    * a set of shortUrls with:
      + a user
      + an original URL
      + a website    
- **actions**
    * generateURL (user: User, url: URL, website: Website): (shortURL: ShortURL)
      + **requires** there's no shortURL for the same user and url
      + **effects**  add to the list and return a new generated shortURL with the user and url that points to the same website as URL    

