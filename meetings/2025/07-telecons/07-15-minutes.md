# Private Advertising Technology WG - 2025-07-15

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Tara Whalen (W3C)
5. Miguel Morales (IAB Tech Lab)
6. Andrew Paseltiner (Google Chrome)
7. Erik Taubeneck (Apple)
8. Charlie Harrison (Google)
9. Michael Kleber (Google)
10. Mark Blunk (Apple)

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

Scribes: Michael Kleber

Policies: See WG Read All About It Slides.

## Review of Action Items

- [x] ~~Create a PPA folder for examples. People can add to it with PRs (Aram)~~  
- [ ] **ARAM MOVE TO CG**: Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
      - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confir	m the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (group)  
      - [ ] Create issue for people to document their important flows for future test coverage (Aram)  
- [x] ~~File an issue to talk about flexibility of filtering and impression logic based on April’s conversation; see issue [https://github.com/w3c/ppa/issues/204](https://github.com/w3c/ppa/issues/204)~~   
- [x] ~~Brian May: Write up enum for last touch attribution logic; see issue \#42.~~  
- [x] ~~Paul deGrandis: Write down multi-touch attribution; add to issue \#44.~~  
- [x] ~~Charlie and Erik write up CEL; see [https://github.com/w3c/ppa/issues/204](https://github.com/w3c/ppa/issues/204).~~  
- [ ] Aram Zucker-Scharff (following Brian May’s write-up): Submit issue to CG agenda around definition of last-touch and if we need more in the enum for level 1 logic options.  
- [x] ~~Chairs to issue CFC for \[\#202\](https://github.com/w3c/ppa/issues/202).~~

## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

Charlie Harrison: We discussed two things related to multi-touch attribution and extending the attribution algorithms.

#### [CEL-type expressions](https://github.com/w3c/attribution/issues/204)

Declarative API to cover the Paris face-to-face use cases — this one was fully spec'ed and is merged.

Spec says that impressions get sorted first by priority, then by timestamp.  Now if you want something like what Brian May described — "attribute the most recent click if there is one, otherwise attribute to the most recent impression" — then you can do it by just giving clicks higher priority than touches.  

Multi-touch attribution is supported by giving an array of "n credit fractions", then you take the first n things in the list of priority-and-time-sorted impression array, and allocate credit proportionally to the fractions in the array.  The algorithm "fill a histogram with last-n-touch attribution" in the spec, automatically supports Brian's algorithm if the credit array is empty or length-1.  

Brian: If I have 100 impressions and I tag 20 as what should be in the report, does each get 1/20th of the credit?  

Charlie: Yes if you pass in an array of 20 elements all 1/20th, but if you want then you can be more flexible  

Brian: I'm not used to a bounded number of elements, should be as many things as I think contributed to the outcome  

Charlie: I think we could support that also, could allow you to specify "linear credit among any number of entries" or similar things.  This API doesn't know things like "decay over time" or "U-shaped", but if there are just a few more rules-based models to cover, then we can probably expand this to address some more use cases.  

Brian: Also want to collect all impressions in a database, then slice-and-dice and figure out what perspective gave you the best insight.  

Charlie: Probably could do that by invoking the API a few times, e.g. call measureConversion twice with two different models.  That's if you can specify k different models at the time of the conversion, not after-the-fact database manipulation; going beyond that would require logic at aggregation-query time.

Brian: What about budgeting?

Charlie: Yes, specifying k different models would require you to split your budget k ways.

Brian: Does multi-touch also automatically use more budget?

Charlie: Yes, more mass in the histogram means more total noise coming out, generally true.  

Brian: I think we should put together a reasonable sketch of what multi-touch looks like, and what the limits are, so that we can get a practitioner's view of whether the constraints leave us with something that is usable.  Whether it's expressivity or noise, may not be usable.  

Charlie: If the API's privacy is poor, i.e. no noise, do you still worry about no value?  

Brian: In my experience we didn't get a single model across campaigns, we were in a perpetual cycle of looking at data and tweaking it to do novel analyses each time.  That seems like it would always chew up the privacy budget.  

Charlie: I would be happy to simplify if this isn't actually useful.  Very interested in hearing others' feedback too.  I don't think Paul is on this call; he indicated this would be useful in Paris.  

Erik: How does this interact with more flexible filtering/attribution with CEL?  transition\!

Charlie: This array-based thing is a strict subset of what we could implement with CEL or with a JS API.  (PR in progress to fix up the details of rounding.)  [https://github.com/w3c/ppa/issues/204](https://github.com/w3c/ppa/issues/204) shows exactly how to implement the above array-based approach in CEL code. In this case we don't need the API to inherently know about priority or credit array, instead the impressions can hold arbitrary key-value pairs and you can write CEL code to do it all yourself. The CEL version could also do more involved things like exponential delay based on the actual wall-clock time elapsed between impression and conversion.  If this particular exponential decay thing were really important, then we could hard-wire it into the declarative-version approach, but the CEL version allows lots of flexibility so that parties using the API can make up their own things that we didn't design for explicitly.

Brian: Is the model "up to N impressions get some fraction of credit"?

Charlie: In the CEL version, don't need to choose N, you can write code to do whatever you want.

Brian: Are all impressions considered equivalent?

Charlie: Each impression has some user data associated with it, which you log at impression time, and then you can write a programmatic expression at conversion time to allocate credit in whatever way you want.

Brian: Could I give more credit to impressions on site1 than site2?

Charlie: Sure, store whatever you want in user data and use it however you want at impression time.

Brian: How about bringing offline data in?

Andrew: When you call measureConversion, you supply the expression that is the CEL program.  Probably some limit on the amount of data you can shove into that string.  Once the program is running, it can't contact the outside world to load more data.

Charlie: If you know the user in the middle of converting has done prior events, then you can do what Andrew said.

Brian: My user has a Rewards card that they use at check-out time, so I know all the prior events.

Charlie: Yes, that works, like Andrew described: You would embed the Rewards-card-based info into the CEL program, almost as if you were hard-coding it.

Miguel: That returns impressions and how much credit to give to each one of them?

Charlie: That was the original shape of the API — map from impression to credit, where all the credits sum to 1\.  Andrew posted a follow-up last week where the CEL impression emits the raw histogram, to give more flexibility if you can't do it as a credit fraction.

Miguel: That's good, function that gets all the events that match, then developer emits the histogram contributions.  I can't think of any feature that would make it better, I like this a lot.

Charlie: Key feedback would be: What will you do with this that you can't do with the declarative version?  Brian pointed one out, no pre-specified length of number of impressions.  Also bringing in outside data.  We should compile the list and see how big the gap is between the easy declarative version vs the more involved CEL version.

Miguel: Bringing in your own data is probably a big deal, likely to make a big difference.

Brian: MTA is very valuable, and the benefits of the CEL version seem large.

Charlie: Brian and Miguel: PLEASE leave comments in this issue \#204 of the relative benefit of CEL over declarative.  And also, please consider what CEL can do, so that you can tell us if there is some limit of CEL that means we need to consider full JS instead.

Sean: Any other open issues tagged with "Discuss"?

Charlie: Open issues [\#32](https://github.com/w3c/attribution/issues/32), [\#34](https://github.com/w3c/attribution/issues/34), and [\#38](https://github.com/w3c/attribution/issues/38) all discussed in Paris and turned into a new proposal that Martin made, [\#216](https://github.com/w3c/attribution/issues/216), as long as all histogram contributions go to a single party.  People who care about this topic, please read through [\#216](https://github.com/w3c/attribution/issues/216) and see if this Paris-meeting short-cut meets your needs for an MVP.  

Erik: We did agree that both sides of delegation would be able to put limits on the other side?  

Charlie: Yes, that was one of the conclusions.  I don't think anyone has written up the permission stuff.

Brian: [\#189](https://github.com/w3c/ppa/issues/189), when the HTTP response header is processed.  Doesn't that create problems for site owners, who would need to control things through Permission Policy?  Advertising has all sorts of things being added to the page that you don't know about until the moment.  It's much more controllable for things being added, if they are net-new, get an attribute added to them.  The Permissions Policy thing made sense for conversion pages, which nobody wants to change, but for the impression side this seems way too loose.

Aram: We came to this conclusion because adding a new property to the HTML of every linked thing seemed unreasonable, and this provides for a more backwards-compatible approach.

Brian: Those both concern me\!  I think you \*should\* have to add something to your code if it's going on someone else's page.

Aram: I don't think it's much of a problem to do Permissions Policy.  You're going to need to decide to add something that can measure conversions to your page, and that person is going to have to add that to whatever is generated by it.  Either it's coming from an iframe, and the person that provided the creative to the iframe is expected to have some level of conversion tracking, or you're putting something on your page and you would have to add this.  I don't have a problem with asking people to use PP if they want to block this.  The question is whether this is a private API.  If it's a private API then the concern that we would introduce something that is \*leaking\* is not a concern, by design.

Brian: Any element on the page could trigger a saveImpression response, without any specific permissions grant from me.  How do I keep someone from just stuffing random impression claims onto my page?  Distinguish between ad assets and not, for example?  

Charlie: Maybe we should go through an example of where any harm is done to the publisher?  The publisher isn't harmed, their quota isn't used.  What harm is done?  Our conclusion in Paris is that there's no incentive to do that.  The advertiser needs to trust the entity when they're making the measureConversion call, so if the advertiser trusts a party, then that party can register impressions.

Brian: Feels like it would be better to keep the sludge out of the system than to clean the sludge out after the fact.

Charlie: If there is no benefit from this, then nobody will add sludge.

Brian: I'll read through and give something more specific.

Charlie: Show how someone is harmed and we will figure out how to address it.  On the ARA side, we saw very significant adoption costs to including this attribute ("Oh I have thousands of widely-dispersed creative types that I will need to change some code for"), so there is lots of cost to this, and we don't think there is benefit.  This was surprising to us, but that was our implementer experience  

Brian: I guess there are lots of different types of ad tech.

Charlie: And if we do need to do it, then please let's keep it called "attributionsrc", because we put so much work into getting adoption and it was a big headache\!  But would absolutely be best if we don't need it at all.

#### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

Charlie: Flexible filter thing: Right now the API only allows each impression to allocate value to a single histogram bucket.  It would be good to hear whether that limitation is important.  For example this doesn't allow you to measure two different things in the same impression, e.g. counting both impression and impression-cross-geo, or two different slicings, campaign-by-publisher and campaign-by-geo, or whatever.  I'm not sure whether that limitation means it's DOA or whether we need to change it for the MVP?  This is issue [\#222](https://github.com/w3c/ppa/issues/222).  The CEL thing we presented naturally would support spreading value across multiple buckets; if this is an essential feature then we would need to add it to the declarative thing, or CEL would be required, etc.

Miguel: This does seem like an important limitation.

Charlie: Please comment on [\#222](https://github.com/w3c/attribution/issues/222), and give some examples of use cases, so that we can know them when we are making the choice about what to support.

Brian: Isn't' that where we would want to end up?

Charlie: That's a decision of the group.  Fully specifying CEL and bringing it into the web platform is a heavy lift, there is lots of cost, so we need to know the benefit when we're going into a discussion about trade-offs.

Brian: Did you consider using hierarchies?

Charlie: In ARA we explicitly built for hierarchies, and we got feedback that that wasn't the only reason to have multiple contributions.

Miguel: Suppose I'm an advertiser and I use a key that combines publisher ID and campaign ID.  Then can I run a query that aggregates on just one of them?

Charlie: Yes — imagine it as a tree, information at each leaf, then can aggregate across them.  May or may not be an efficient use of your privacy budget — we published a paper about how to make this efficient, I'll post a link:
  * [https://arxiv.org/pdf/2308.13510](https://arxiv.org/pdf/2308.13510): Optimizing Hierarchical Queries for the Attribution Reporting API  

Miguel: in the real world I would just run the most granular query and then aggregate outside the system after the fact.

Charlie: The point of this paper is that what you described would work, but if you do it in the way the paper describes, then you can get the result with less noise / less error.  The paper was about ARA, which had this; the current API doesn't have a way to do it unless we implement CEL.

Miguel & Brian: Seems like querying is very complex.

Charlie: Agreed, this group or the CG would do well to provide guides or examples of how to best use the API especially in the large-noise regime.  And maybe some existing flows are too privacy-invasive in general and people would do better with simpler types of queries.

Brian: If 3pc are sticking around, then the level of complexity people would have to deal with here is lower.

Charlie: But 3pc are only sticking around in one browser.  We have a NoiseLab tool so that people can experiment with this.

Brian: How quickly does noise destroy the value?

Charlie: Depends on parameters, and every browser might choose a different one.  For the mechanism in ARA (Laplace), variance quadratically with epsilon, so std dev scales like 1/epsilon.

# AOB

None.

## Future Meetings Information & Logistics

TPAC 2025 (November, Kobe Japan): Expect the WG on Thursday and the CG on Friday.

## Review of Action Items From This Meeting

- [ ] **ARAM MOVE TO CG**: Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
      - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confir	m the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (group)  
      - [ ] Create issue for people to document their important flows for future test coverage (Aram)  
- [ ] Aram Zucker-Scharff (following Brian May’s write-up): Submit issue to CG agenda around definition of last-touch and if we need more in the enum for level 1 logic options.
