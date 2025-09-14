# Problem Set 1

## Exercise 1: Reading a concept
1. **Invariants**. What are two invariants of the state? (Hint: one is about aggregation/counts of items, and one relates requests and purchases). Say which one is more important and why; identify the action whose design is most affected by it, and say how it preserves it.
   - An invariant of the state is that the count cannot be negative, as in an item cannot be purchased more than the count recorded in the registry. The action *purchase* is most affected by it, as it requires that the registry "has a request for this item with at least count". This preserves the invariant by preventing a purchase that attempts to buy more than the count. Another invariant is that an item has to have a request (i.e. exist in the registry) for it to be purchased. The action *purchase* is again the most impacted because it requires that the request for this item exists in an active registry. This preserves the invariant by preventing purchases for items that were not added to the registry. The second invariant is more important because it prevents the owner of the registry from receiving random gifts that they did not want. It could cause none of the registered items being purchased, which defeats the purpose of a gift registry. If the first invariant were to be broken, then the owner may simply receive duplicates of a gift they wanted. 

2. **Fixing an action**. Can you identify an action that potentially breaks this important invariant, and say how this might happen? How might this problem be fixed?
   - The action *removeItem* can break the invariant. If an item was added to the registry, then someone can make a purchase for the item. However, the invariant breaks if this item were to be removed because there would be a purchase for an item that no longer has a request. This can be fixed by prohibiting a removal if an item exists in the purchase history and allowing the owner to decrease the count to match the number of purchases in case the owner decides they do not want more of the item. 

3. **Inferring behavior**. The operational principle describes the typical scenario in which the registry is opened and eventually closed. But a concept specification often allows other scenarios. By reading the specs of the concept actions, say whether a registry can be opened and closed repeatedly. What is a reason to allow this?
   - A reason to allow this is to prepare for an accidental opening or closing of a registry. If the user prematurely opens or closes a registry, then there should be an option to open/close again to fix the mistake, then still able to open/close at a later time. 

4. **Registry deletion**. There is no action to delete a registry. Would this matter in practice?
   - This would matter based on the design of the website, and in some extreme cases. If the website does not separate open and closed registries, then some users that frequently use gift registries would want to keep everything organized by deleting old registries. To add on, if someone creates a registry by accident, then there should be an option to delete it for organization purposes. In other extreme cases, the owner of the registry may have bad memories associated to one and wish to delete it completely.

5. **Queries**. What are two common queries likely to be executed against the concept state? (Hint: one is executed by a registry owner, and one by a giver of a gift.)
   - One common query likely to be executed against the concept state is to track what has been purchased. Another common query is for the givers of a gift to see what items have not been purchased yet to the full count.

6. **Hiding purchases**. A common feature of gift registries is to allow the recipient to choose not to see purchases so that an element of surprise is retained. How would you augment the concept specification to support this?
   - First, I would edit the state of Registrys to become:
      - a set of Registrys with
         - an owner User
         - an active Flag
         - a showPurchases Flag
         - a set of Requests
   - This flag can be edited by adding one action:
      - togglePurchaseVisibility (registry: Registry, flag: Boolean)
         - **requires** registry exists
         - **effects** allow the user to see purchases if flag is True, otherwise hide purchases

7. **Generic types**. The User and Item types are specified as generic parameters. The Item type might be populated by SKU codes, for example. Explain why this is preferable to representing items with their names, descriptions, prices, etc.
   - SKU codes are preferable because they are guaranteed to be unique, whereas some items may be listed with the same or similar name, description, and price. To add on, the codes do not fluctuate unlike the other representations that could change over time. This consistency ensures that each item is the desired or purchased item, even if the other data changes.


## Exercise 2: Extending a familiar concept

1. Complete the definition of the concept state.
2. Write a requires/effects specification for each of the two actions.
   - **concept** PasswordAuthentication
   - **purpose** limit access to known users
   - **principle** after a user registers with a username and a password, they can authenticate with that same username and password and be treated each time as the same user
   - **state**
      - a set of Users with
         - a username String
         - a password String
   - **actions**
      - register (username: String, password: String): (user: User)
         - **requires** username is unique (no existing user has the same username)
         - **effects** registers the new user by adding to Users, returns this user
      - authenticate (username: String, password: String): (user: User)
         - **requires** user exists with given username and password
         - **effects** authenticates and returns this user

3. What essential invariant must hold on the state? How is it preserved?
   - The essential invariant is that usernames are unique. This is preserved with the action *register*, as it requires that to register a new user, the given username must not already exist.

4. Extend concept to include confirmation with email.
   - **concept** PasswordAuthentication
   - **purpose** limit access to known users
   - **principle** after a user registers with a username and a password, they can authenticate with that same username and password and be treated each time as the same user
   - **state**
      - a set of Users with
         - a username String
         - a password String
         - an email String
      - a set of pendingUsers with
         - a username String
         - a password String
         - an email String
         - a token String
   - **actions**
      - register (username: String, password: String, email: String): (token: String)
         - **requires** username is unique (no user in Users or pendingUsers have the same username)
         - **effects** adds user to pendingUsers, emails token associated with this user
      - authenticate (username: String, password: String): (user: User)
         - **requires** user exists with given username and password in Users
         - **effects** authenticates this user, returns this user
      - confirmEmail (username: String, token: String): (user: User)
         - **requires** user exists with given username in pendingUsers
         - **effects** adds user to Users, removes user from pendingUsers, returns this user


## Exercise 3: Comparing concepts

### Minimal Specification of PersonalAccessToken
- **concept** PersonalAccessToken
- **purpose** limit access to certain users without needing a password
- **principle** after a registered user generates a personal access token, it can grant access for that user without the password
- **state**
    - a set of Tokens with
        - an owner User
        - a token String
        - a last-used Date
    - a set of Users with
        - a username String
        - a password String
- **actions**
    - createToken (owner: User): (token: String)
        - **requires** owner exists
        - **effects** generates a new token associated with given owner, adds to Tokens, returns the token
    - authenticate (owner: User, token: Token): (user: User)
        - **requires** owner and token exist, the token is owned by the given owner
        - **effects** authenticates the token, returns the token's owner
    - deleteToken (owner: User, token: String)
        - **requires** token exists and given owner is the token's owner
        - **effects** removes given token from Tokens
    - expiredTokens ()
        - **effects** if the last-used Date of a token is over a year ago, delete the token from Tokens

### Comparison of PasswordAuthentication and PersonalAccessToken
The main difference between PasswordAuthentication and PersonalAccessToken is that passwords allow a user to access to everything, whereas personal access tokens grant access to specific resources. Since there can be multiple tokens per user to be created according to specific use cases, tokens also expire if they are not used for more than a year. This differs from passwords, as there exists only one per user and last an infinite amount. 

### Improvements to the GitHub Documentation
One major improvement I would make to the documentation is to clearly outline cases in which someone would use a personal access token. I struggled to understand when the tokens would be used and how that is different from a password authentication. Another suggestion I have is to separate the pages for fine-grained personal access token and personal access token (classic) completely. It was hard to navigate through the page when I was only looking for information on personal access token (classic).

## Exercise 4: Defining familiar Concepts

