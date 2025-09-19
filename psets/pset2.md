# Problem Set 2

## Concept Questions
1. **Contexts**. The NonceGeneration concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what will a context end up being in the URL shortening app?
   - The contexts are for different domains or subdomains of a webpage. For instance, even if both two contexts contain the same Nonce, they are inherently different URLs due to the difference in domains. In the URL shortening app, a context will be the base of the URL, such as "tinyurl.com" or "yellkey.com", whereas the Nonce would be a string like "happy". This combined would generate a URL like "tinyurl.com/happy".

2. **Storing used strings**. Why must the NonceGeneration store sets of used strings? One simple way to implement the NonceGeneration is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an abstraction function.)
   - The NonceGeneration must store sets of used strings so that it can ensure there are no duplicate strings in one context. If there were duplicate strings, it would mean that two different URLs are shortened to the same URL, breaking the invariant that there one shortened URL should match to only one long URL. The set of used strings in the specification is related to the counter in the implementation because they both maintain the invariant and output unique shortened URLs. When the counter increments, a new URL is provided for the user because of the change in domain. This is related to how the set of used strings work because they ensure that a new shortened URL is provided for each user. From the user's perspective, the function is the same since the operations are abstracted away.

3. **Words as nonces**. One option for nonce generation is to use common dictionary words (in the style of yellkey.com, for example) resulting in more easily remembered shortenings. What is one advantage and one disadvantage of this scheme, both from the perspective of the user? How would you modify the NonceGeneration concept to realize this idea?
   - One advantage of the scheme is that the user does not have to come up with a word themselves, and there is a lower chance of mistyping the link. It makes the URL more user friendly, as all the users of the shortened link would have to do is type in a short link with a simple word. This is an easier alternative than a random combination of alphanumeric characters. However, a disadvantage that the shortened URLs will have to be reset more frequently. Since there are less common dictionary words than there are random combinations, the lifespan of each link is shorter. I would modify NonceGeneration to allow for a combination of words, rather than just single dictionary words. The data can be represented in a dictionary, where the keys are an integer, and the values are a common dictionary word. the set of used strings can include strings that represent the combination of these dictionary words, such as "12" or "231". This way, there can be more options to use such words without having to reset many URLs.


## Synchronization Questions
1. **Partial matching**. In the first sync (called generate), the Request.shortenUrl action in the when clause includes the shortUrlBase argument but not the targetUrl argument. In the second sync (called register) both appear. Why is this?
   - 

2. **Omitting names**. The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn’t this convention used in every case?
   - 

3. **Inclusion of request**. Why is the request action included in the first two syncs but not the third one?
   - 

4. **Fixed domain**. Suppose the application did not support alternative domain names, and always used a fixed one such as “bit.ly.” How would you change the synchronizations to implement this?
   - 

5. **Adding a sync**. These synchronizations are not complete; in particular, they don’t do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.
   - 


## Extending the Design
