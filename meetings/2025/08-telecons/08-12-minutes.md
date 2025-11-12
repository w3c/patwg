# Private Advertising Technology WG - 2025-08-12

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Andrew Paseltiner (Google Chrome)
4. Charlie Harrison (Google Chrome)
5. Erik Taubeneck (Apple)
6. Martin Thomson (Mozilla)
7. Tara Whalen (W3C)
8. Brian May (unaffiliated)
9. Matthew Finkel (Apple)

## Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes  
2. Review of Action Items from Previous Meeting(s)  
3. PPA Discussion Topics  
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)  
      1. TBD   
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)  
4. AOB  
5. Future Meetings Information & Logistics  
6. Review of Action Items From This Meeting

## Hellos, Intros, Policies, & Call for Scribes

Scribes: Martin Thomson & Erik Taubeneck

Policies: See WG Read All About It Slides.

## Review Action Items

- [ ] **ARAM [MOVE TO CG](https://github.com/patcg/meetings/issues/227)**: Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
    - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
- [ ] Develop Web Platform Tests in PPA (group)
    * Create issue for people to document their important flows for future test coverage (Aram)
- [ ] Aram Zucker-Scharff (following Brian May’s write-up): Submit issue to CG agenda around definition of last-touch and if we need more in the enum for level 1 logic options.
    * Sean: That meeting was canceled last time; we’ll get to that next time the CG meets next in September (or later).
    * Aram: This is pending a write-up of different attribution types that we have discussed.  Maybe there was a conclusion that it didn’t make much sense to write up.
    * Charlie: This was from Paris and Brian did write this up.
    * Sean: We did some work; Brian wrote it up during the meeting, but we need to take it to the CG.
- [ ] Renaming is on its way.
    * Anyone can contribute to final cleanup (README etc)
    * Sean: Do we need to change the charter?
    * Tara: Don’t think we need to, but will confirm. And maybe decouple the names as much as possible without losing the precision.
- [ ] Charlie: Two things called out in the last meeting, but can we formalize these as action items.
    * Brian and Miguel to provide feedback on Issue \#204: [https://github.com/w3c/attribution/issues/204](https://github.com/w3c/attribution/issues/204)
    * Have discussed with Brian about this and the missing use cases, but need more feedback written down.

And on \#222: [https://github.com/w3c/attribution/issues/222](https://github.com/w3c/attribution/issues/222)   

Brian: It is common to have multiple campaigns that hit the same point of conversion.  With very few attributes, it will be difficult for them to follow how each of those campaigns is going.  Need to have some sort of extra mapping from campaign to indices.  Otherwise, people will feel that the API doesn’t address their needs.  Especially where advertisers run multiple concurrent campaigns.

## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

[https://github.com/w3c/attribution/issues/60](https://github.com/w3c/attribution/issues/60)  

Charlie: The use case here is that the user lands on a conversion page and so the site wants to register a conversion.  Then they refresh the page or something like that.  We don’t want the conversion to be logged twice (And budget spent twice).  In existing flows, there is a tag on the flow that allows duplicates to be detected and discarded.  This is hard to do server side in a naive way without consuming budget.  Maybe the report can be tagged with an ID on the server side, but the client side will eat budget.

We discussed several approaches: first, the API user has local deduplication logic.  They can look at an ID or whatever and not invoke the JS.  Or, we could build some support into the API, so that conversions have a key and the browser just errors out or no budget is consumed (pick one).

ARA has a dedupe key.  It’s a bit complicated, but you attach the key to each impression.  Then, if the impression is used twice, then it is suppressed.  Hard to transfer that design to multi-touch.

Editors met and agreed that this might be good to punt for now and recommend that the callers of the API use whatever mechanism they like to avoid duplicate conversions.  We’d need to hear that managing this is infeasible before going there.

Brian: People can figure this out on their own.  Pages can ensure that they don’t call multiple times.

Erik: Agree.

RESOLUTION: close that one out.

[https://github.com/w3c/attribution/issues/204](https://github.com/w3c/attribution/issues/204)  

Charlie: Does anyone want to put the effort in to solving this in V1.

Brian: Hard to find someone to put the effort into using the API without this.  I don’t think that people are going to spend the time to use this, rather than trying to work this out.  Maybe it could be used for simple things, but we saw with ARA- which has sophisticated facilities \- there was a lot of inertia and resistance.

Martin: This is very complicated, but it could be quite easy to use.  Suggest that we let this continue to evolve.

Charlie: The real trick is in deciding how much the “declarative” API needs to be able to do, vs. taking on this sort of thing.  That is, where is the line between “declarative” and this more complex thing.  Or can we add some simple things to make the declarative stuff better.  If we are planning on this being a follow-up, so we should keep it relatively simple in the base API, given that we seem to be on the path to having all the bells and whistles defined here.

Sean: We should let this sit.

Martin: Anyone want to make the base stuff more complex?

Charlie: We could also make the base simpler if this is coming.  For instance, the multi-touch stuff could be more complex.  So far, no one has said that what we have in the spec is \*good\*.  Maybe Paul thinks it is, because the MTA thing is to his specifications.  We could go simpler.

Martin: Maybe discuss [\#219](https://github.com/w3c/attribution/issues/219)

Brian: Maybe we should talk about multi-campaign.  The way this is designed, you need multitouch to do many campaigns.  You might need to allocate credit to both campaigns.

Charlie: Right now, you need to split credit, yes.  Campaigns are not a first-class construct in the API.

Brian: This is a basic problem here.

Charlie: I’m interested in why this is a problem.  Yes,   you don’t want to double-count conversions, counting against each.

Brian: To measure effectiveness, you need to be able to differentiate the campaigns.  Those conversions need to show up in both reports.  You can’t have only one or the other.

Martin: You would currently need to put the things into the same histogram, with multi-touch.

Erik: A few ways.  You could do separate measurements.  You could make two calls and give each a portion of your budget.

Martin: That works, but you can’t reallocate credit across the campaigns: If you have a 4/6 split, you can’t get all 10 points allocated to one.

Erik: Not ideal here, but don’t want perfect to get in the way of shipping something.  We can do this, but it might not be optimal utility for DP purposes.

Brian: It’s dawning on me that the browser represents the partition vs. the campaign.  Traditionally, we partition on campaign.  There’s no limit based on the browser, you bucket by campaign.  Here, we are saying that one browser cannot participate in many campaigns.  For adtech, that’s going to be a subtlety they will need to understand.  Today: the more campaigns you run, the more you learn.  Future: The more you run, the more the information is spread between campaigns.

Charlie: If there is one conversion, it is not that optimal to measure many things with that.  If you have many campaigns, it’s not going to work to spread it thinly.  ARA also doesn’t support this.  ARA forces you to use something like last touch, modulo some of the more fancy features.  None of our adopters were using it in a loop to cover many campaigns.

Brian: I expect adtech to have a problem with this.  If you have many campaigns, you would be splitting budget.  You pay for 5, but you have to spread the information from the user across those 5 campaigns.

Charlie: This is what the privacy constraints lead us to

Charlie: If you are getting attributed conversions, the measure is still accurate.  What you miss is the more granular breakdown.

Brian: Some more problems I didn’t catch.  If I run many campaigns, there might be overlap between them and you want to track what is performing.

Charlie: Maybe you lose the overlap.

Brian: If you only get the last touch, is there any value in trying different strategies.

Charlie: Do you have path-level data to construct queries?

Brian: You stamp everything with a browser ID and then put things together.

Charlie: Are you dropping campaigns to run this analysis to see the effect of each?  What analysis is running there?

Brian: Is the campaign performing.

Charlie: The suggestion Erik made might not give you that info because it smushes everything together.  It’s not clear what would have been decisive for a given user.  Because it’s an aggregate.  Need to know what analysis is being applied to know whether we can do it in the API.

Brian: Lots of magic in attribution.  Lots of ideas.  If I run 5 campaigns, I want impressions for each to be attributed.  Then I want to look at each in isolation.

Charlie: Say you have 2 campaigns and 100 people see them.  Case 1: These are totally overlapping sets.  Case 2: These are disjoint sets of people.  In aggregate, with just conversion counts, those are basically indistinguishable.  Is that your goal?  To make the cases hard to distinguish?  Because Erik’s suggestion works for that.  But if you want to know about interactions between campaigns, you need something more fancy.  What is your goal?

Martin: This seems rather academic to me and has not led to a concrete suggestion and we should move on to the multi-touch thing and make sure everyone understands. I think there are ways to do what Brian wants to do but none of them will be perfect. 222 first?

[https://github.com/w3c/attribution/issues/222](https://github.com/w3c/attribution/issues/222)  

Charlie: The issue I wanted to figure out … we nearly decided this last meeting… Need to get this in writing.  For the declarative API, you can express a single histogram bucket for each impression.  When you attribute that impression, the only thing that can happen is that the whole value is dropped into the bucket.  The value is based on the multi-touch algorithm and the value chosen, but it all goes into the one bucket.  Does that suffice for L1?  Or do we need to have a way to allocate across multiple buckets. 

Example would be linked from the issue: you might want to query disjoint things about the impression.  Such as campaign, geography, or other things, all separately.  That requires that you have histogram buckets across all combinations of inputs.  The existing approach points more to finer-grained buckets than you need.

There’s an approach in ARA for allocating the split between multiple buckets for hierarchical queries.  You get this question of how to split that value between buckets, but it adds another layer of credit allocation: both on impression level and within each impression.  Complicated.

Erik: Clarifying.  Is this something that is necessary for counting and summing values?

Martin: Complicated, but you can do something with a less flexible split by having multiple recorded impressions or multiple queries.

Charlie: Might punt to the JS version of the API.

Brian: Something in the multiple impression/query that makes me nervous. The reports are independent, so it is not possible to align things.  Hard to bring things together and make sense of thing.

Charlie: There is some confusion inherent in the privacy constraint.  Then there are the constraints from operationalizing something like this.  You are still going to have problems from aggregates that come from any way of structuring this API.  Things won’t add up, because of noise.  Some post processing can be done to reconcile things at different levels of hierarchies.  But that is an inherent problem.

For multiple impressions, the damage from a mistake looks a lot worse.  You rely on more things being correct.  That makes issues bigger.

Martin: Yes, mistakes are worse if you have more inputs to get right.

Brian: You can get different aggregates from different things and the difference between two sums need to be small enough that people don’t lose faith in the system.  If I measure things two ways, the result should be close.

Charlie: Variance/bias tradeoff again.  Reducing variance means more exposure to bias.  Finding the sweet spot will depend on tolerance for each.  And also this depends on understanding user behaviour for things.  Maybe you can blow the entire budget on one conversion, measured one way.

Martin: Useful discussion, but move to close this.

Erik: Good for V2.

Charlie: solved with JS proposal in [\#204](https://github.com/w3c/attribution/issues/204) (maybe also CEL).

Erik: Sounds like the answer is not V1.

Charlie: Maybe document the hacky solution Martin suggested and close this.
Charlie: Maybe add an issue to track the addition of a test for this use case.

[https://github.com/w3c/attribution/issues/219](https://github.com/w3c/attribution/issues/219)  

Martin: The paper Charlie found here is pretty neat. It looks at your values and assigns it based on how far the values are. The net effect is the expected value when many values are going through matches to the underlying non-integer value that came in. There’s a very simple algo (once you work through it all) and the expected value matches the floating point number. 

It only works with large numbers, but that is what we’re working with. 

Brian: Can we scale up the values?

Martin: There are always situations where you can scale up but end up with fractional conversions in the buckets.

Brian: Could one multiply results by 1000? 

Martin: Yes, but you don’t have to. You’re still getting fractional parts and losing some signal.

Charlie: There’s a strong reason to prefer not scaling up the values and instead use a more discrete domain. The reason is that the noise distribution algorithm will cause the max value sensitivity (when it is small to be less than epsilon) to create an exponential decay.

Brian: Can we offer both possibilities to people?

Charlie: Yes. It’s beneficial to use this algo, especially when dealing with small sensitivities.

Martin: It works out how far they are from the whole number, then determines which is closest and that gets moved to the integer and adjust.

Charlie: We want accuracy, and never have a large error. We want the array to be handled without bias. Allows for normalized credit. If the three properties are good you can be happy with the algorithm even if you don’t work the math on your own, so let’s confirm that. We avoid rounding errors.

[https://github.com/w3c/attribution/issues/216](https://github.com/w3c/attribution/issues/216) 

Martin: How much additional work should we put in for multi-intermediary, or should we wait until we have more programmatic APIs? Not sure that even solves it for free. The way the histogram is structured will depend on the intermediaries.

Charlie: I think this can be done in JS, as long as we’re ok with one large histogram out.

Martin: That’s the difference, need multiple histograms out.

Erik: Does this require JS, or could this be lower level.

Martin: If you want to do it without, need some of the more complicated declarative.

Charlie: Isn’t this issue compatible with the declarative API?

Martin: Yes.

Charlie: JS not required.

Martin: Need some coordination even if you have JS API.

Erik: Is this different (except for budget) from running parallel measurements?

Charlie: Yes, in the parallel case, every one would attribute with last touch. With this, only the global last touch would attribute.

Martin: Would require all intermediaries to coordinate at impression time.

Brian: What happens if you change your mind?

Martin: Very fragile. Need to start over.  JS API might give you some way to adapt, this can’t.

Martin: Key question “does it make sense to be able to split the histogram”?

Erik: May make more sense to think about "splitting histograms” as a part of the JS API, rather than wedging it into the declarative API.

#### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

None.

## AOB

None.

## Future Meetings Information & Logistics

TPAC 2025 (November, Kobe Japan).

# Review of Action Items From This Meeting

- [ ] Aram [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
    - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (group)  
    * Aram: Create issue for people to document their important flows for future test coverage  
- [ ] Aram (following Brian May’s write-up): Submit issue to *CG* agenda around definition of last-touch and if we need more in the enum for level 1 logic options.  
- [ ] Brian and Miguel to provide feedback on [Issue \#204](https://github.com/w3c/attribution/issues/204)  
- [ ] Brian and Miguel  to provide feedback on [Issue \#222](https://github.com/w3c/attribution/issues/222)
