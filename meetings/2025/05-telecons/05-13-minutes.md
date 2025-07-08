# PAT WG Minutes - 2025-05 Meeting

### Scribes



* Aram Zucker-Scharff

## Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes
2. Review of Action Items from Previous Meeting(s)
3. PPA Discussion Topics
    1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)
        1. [Decide about how conversion site matching works](https://github.com/w3c/ppa/issues/145)
        2. [Side channels for measureConversion operation ](https://github.com/w3c/ppa/issues/153)
    2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)
4. AOB
5. Future Meetings Information & Logistics
6. Review of Action Items From This Meeting


### Introductions, Code of Conduct, Minutes Document, Scribes


### Review of Action Items

Copied from earlier:



- [ ] Create a PPA folder for examples. People can add to it with PRs (Aram)
    - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
        - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example -[ Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))
- [ ] Develop Web Platform Tests in PPA (group)
    - [ ] Create issue for people to document their important flows for future test coverage (Aram)
- [ ] File an issue to talk about flexibility of filtering and impression logic based on April’’s conversation.


### PPA Discussion Topics


#### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)


#### Topic 1: [Decide about how conversion site matching works](https://github.com/w3c/ppa/issues/145)

Slides:

Martin: Recall what we store. What we have listed is a set of sites and we say please save this and here’s a list of the sites you might place this in. Any one of those sites can query and find that impression and any other site cannot find them. Andrew put up a pull request that is really good for an empty set.

Let’s look at what we actually want. You can specify one conversion site, many conversion sites, or empty. Empty now we are thinking of being any number of sites.

One is the absolute minimum. You can save one for each site. The question is if you want to go to multiple conversion sites and this seems to be a fairly common thing to do.

Having multiple conversion sites is  a special optimization we can do to streamline the use of the API.

The key question is if it makes sense to have an empty conversion site set.

Brian: If we have an empty set then anyone who wants to find a conversion can do so right?

Martin: It is more flexible, but is it useful?

Registering impressions for other websites is something that can then happen, making it easier to pollute the set of impressions someone is sorting over. But anyone who wants to make sure will specify the specific sites they want to query. (Intermediary sites are a separate issue)

We’re already exposed to a random site stuffing impressions, that’s not something we’d deal with here.

Brian: I don’t know if it would work, but my concern is someone adding impression records with no conversion sites associated with it and they happen to be on a site where they can take credit for impressions.

Martin: If people can run a script on your website then they can save impressions.

Brian: if they have a scripted list of impression partners then that is protecting from anyone stuffing impressions in.

Martin: I’d imagine that a site would have a limited number of partners included. Or use content security policies to control the allowed set.

Michael: We haven’t really dealt with something like the empty set behavior in ARA. I can’t think of a use case for that. As long as we have a list with multiple possible conversion sites that covers all the needs I can think of. Unfortunately the ARA folks are not on this call.

Martin: Andrew couldn’t answer either and we couldn’t figure out a way. Does it match the things we need is the question?

Michael: I have no objection to considering an empty list as an error. Both seem like reasonable behavior.

Arpana: Today we don’t allow unlimited in ARA. It is set to 3 or something like that. The use cae we were considering was something like car sales or something like that you see an impression at a site like Chevrolet but convert on a different site like a car dealership local to you. There is a very large set of car dealers in the US where you might convert. This is something ARA hasn’t solved.

Martin: that does argue for including the unbounded option. That example is likely true of a number of different types of product and anything with reseller arrangements and anything along those lines might need that use case and this can cover it.

Brian: We are going to want to be careful to think through and communicate the impact of what an empty set is.

Martin: We can follow up with an editorial change not a substantial change. We can follow up and say that an empty set matches anything and that’s fine.

None one or many is what we’re doing. Maybe we need an implementation defined limit and we can set a minimum number like 3 that can be supported. I might have gone a little higher but not a lot higher.

Brian: 3 is considered small. 10 is a better number.

Martin: I don’t think there is any special cost to storing a few extra things. We’ll have to do multimatch on it. A little annoying but totally manageable.

Brian: Let’s do 10 as a strawman and see what the response is.


#### Topic 2: [https://github.com/w3c/ppa/issues/125](https://github.com/w3c/ppa/issues/125)

Martin: all the different situations that might clear the information out of the browser store. The idea is an HTTP header field that if you get it back in the response that means delete everything. This would include cookies, localStorage, impressions, all the things. But browsers can support a header that says only select ones. Part of the value of that proposal is it allows a site to clear things. See e.g. [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Clear-Site-Data](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Clear-Site-Data)

Aram: I think this is a good idea generally, the capability of specifying particular things to clear seems very useful.  For example, when a user logs out, clear local storage in general but keep certain cookies.

Martin: Right, this is good for a lot of things.  Main use of this feature is for a website that gets into a "broken" state, and uses this general clearing to get back to a known-good state.

Aram: I would think we would want both of these options — two Clear-Site-Data keywords, to clear impressions stored on a site and separately to clear impressions that will convert on a site.

Martin: I’m thinking that the impressions a site saves is not for its own use it is for the conversion side’s use so having you clear that out has no effect on the site that clears it, only impacts the side that the conversion happens on.

Michael: So you are saying that the impression site’s point of view is a fire and forget operation. So clearing that site’s data should be irrelevant.

Brian: I think that makes sense but would the impression site want to clear their side as well b/c they’ve screwed up in some way?

Martin: You don’t know what the conversion site has done at this point. You’d need to communicate with them to make sure that things are correct. Deleting the impressions just makes it impossible to use the impressions. But if you communicate you have to update the matching logic instead.

Brian: That makes sense. The conversion sites own the impressions once registered.

Martin: the intermediary site simplifies that even further.

Michael: Breakage use case. A particular site has accidentally (or maliciously) polluted the impression database by registering a bunch of garbage impressions from ads they didn’t actually intend to register. They end up defacto trying to claim impressions incorrectly. The result is that people who are asking the api for conversion information keep getting this claim that a particular site is responsible and everyone realizes that it is garbage someone is claiming credit for everyone in the world. If the site that made the mistake wants to remedy the situation what can they do?

If the user does go back to the site that makes the mistake then the site can say I messed up let me store all the operations and I want to stop spamming the world with erroneous claims of credit.

Brian: Seems unlikely a site that messes this up will care enough to fix it or see the user ever again and have the opportunity to. The person who cares is the conversion side. They want to nuke the impressions and start fresh.

Michael: I think that’s the right remedy.

Brian: How would the conversion site figure that out?

Michael: Through the impression site or the polluted data?

Brian: How long is it going to take to figure out that things have been polluted? The site that created the problem - they won’t tell.

Martin: more likely the impression site gets a bad feed and puts the wrong ids in the stream. Everything ends up one bucket to the side. But the two sides would likely manage that by talking to each other. The intermediary site would be a big help with that. That would be more difficult and targeted for managing that type of problem. A criteo could come in and save that impression and it would only ever be on the conversion side to know what sort of thing they are saving and construct the queries. Random people won’t pollute the database in that case.

Brian: You could have a long period of time in which bad impression registrations are being generated. No one may notice for a while until they look at an aggregated report.

Arpana: Is it the site or intermediary that saves the impression that the data belongs to. Would the expectation be that every piece of data is gone or would the expectation be that it is retained so the user’s visit is retained and connectable?

Martin: Clear site data isn’t going to clear browser history.

Martin: There are proposals out there like the off the record header which is going to ask to not be in the browser history. But that’s a different thing.

Arpana: who saves the data and who does it belong to. If you are an intermediary that is double keyed. It is owned by the top level website scoped to the intermediary. The pair of them.

Brian: Attribution with cookies - it is the intermediary. They then own the value once that flag is planted.

Martin: scoped to the conversion side.

Brian: At the point of registration of the impression control is ceded.

Martin: That’s what I’m leaning towards. Intermediaries need to be cleared up as to how that will work. We don’t do anything with knowledge that the intermediary is an intermediary right now. We want to proceed on that basis.

Brian: Has there been any review of the ways in which people could screw up the data for registration and how to alert people?

Martin: The browser can’t distinguish good from bad here. We have discussed that when the impression is saved the site could be given a handle to that record in the store and it can give them the ability to say whoops it isn’t an impression - delete it or update the record properly.

Has a bunch of implications, like for bait and switch. But maybe it is reasonable.

Brian: In a case like this I think the safest thing to do is, if you don’t like your impression delete and add a new one, but don’t allow mutating existing records.

Martin: This may be a case for limited fields. If the site wants to update based on assumptions about fraud. Is that an update case though?

Brian: Modify time window?

Martin: We would have to sort through questions like that.

Brian: You could have the browser indicate what all the match keys are for all the impressions it has seen provided in aggregate with other browser records. A person could look through and say this is not the list of match keys we expected.

Martin: There may be a way, but that’s the next iteration I think. We haven’t even made it work yet.

Aram:I think that I disagree with what Brian said earlier.  There is a possibility of highly loyal users returning, so sites can correct or clear incorrect impressions. In terms of modification logic, that’s a tough one.  I would resist fraud being a good example there.  Either the site knows that it is fraudulent, which means sending the signal that it is fraudulent.  Or they don’t know.  A retroactive change is not going to be good for the ecosystem.

Martin: Any information that a site might sync with regards to posture is not on the cards. Sites don’t like sending that information for anyone else. I just am coming up dry for a good example.

Brian: match key is wrong on a bunch of impressions maybe.

Martin: there may be better ways to deal with that, like specifying a range and requesting adjustments.

Brian: We want to consider the possibility of giving people impression-store hygiene capabilities.

Martin: ?

Brian: How to keep impression stores clean and reliable; how to help them figure out when it is unhealthy and what to do and tools to help them with both detection and correction.

Martin: difficult to speculate on that.

Next: [https://github.com/w3c/ppa/issues/24](https://github.com/w3c/ppa/issues/24)

Martin: one of the oldest ones we have now - naming is hard.

We have a bunch of better names possible. Important but draining. I would like someone else to solve this problem. I can’t. Tag is terrible it has a lot of information on that name. Mark is a strange one to me. ID and Anchor are interesting.

Brian: key and ID are references used to look something up. But I don’t know if that’s what it is.

Martin: ID in html is the attribute used to allow broad identification.

Aram: Mark is a reference to use in editing software. One marks a location.

Brian: Maybe selector value?

Martin: a selector is a thing. You’re going composite but I was hoping single.

Michael: is there an argument against matchValue? Seems appealing to me.

Martin: Brian’s idea. I have no objections.

Brian: I worked on another proposal where that was used for a value with a similar function and people seemed to get it.

Martin: let’s do matchValue and let someone who has a better idea argue against it.


#### Topic 3: [https://github.com/w3c/ppa/issues/132](https://github.com/w3c/ppa/issues/132)

Martin: X-Pub measurement - [https://github.com/w3c/ppa/issues/38](https://github.com/w3c/ppa/issues/38) - for F2F discussion. While we’re talking about match values - Andrew asked the question, do you want to be able to save multiple match values or query; see [https://github.com/w3c/ppa/issues/132](https://github.com/w3c/ppa/issues/132).

I imagined you’d be able to use that set to select a set of impressions. I’d like all impressions numbered 17, 27, etc… but not any other ones.

Vs saying this particular impression is this 3 different values.

Brian: Seems like it might create problems or opportunities for people to do things that aren’t above board.

Martin: I’m worried about people following up the impressions database.

Brian: Let’s start with one I think. Conversion sites are trying to select things from a specific campaign(s) for conversion.

Martin: You’d have a set or range of numbers that are in that campaign.

Brian: Those would equate to line items and that’s how you identify a match between the creative and the conversion for a campaign.

Martin: Yes. I think that’s workable.

Brian: two match values on one impression - I can’t really think of a case. At least not a good case.

Michael: You could always simulate by storing in different impressions with the different match values. I think that there’s no special new power from allowing multiple values in a single impression?

Martin: The only reason is if it was common and there was some efficiency. Fewer entries in the db or something like that. But I don’t see an efficiency gain.

Michael: We can always revisit this later if someone comes along with a use case. Until we have that justification though we should not bother doing this speculatively.

Brian: Agree.

Martin: Charlie’s comment - we’ve been coming at this sort of piecemeal from a querying perspective. We should figure out that longer term language for querying and what we want to make available. How rich to get, and what capabilities do we need. JS to run to filter these? Something more constrained? Some uncomfortable in between? ARA has JSON tree comparison I think and that’s how matching works. Kind of amazing, my understanding of the complex things you can do that way is limited.

Working through that is something we can take to the F2F in a breakout or something. Let’s get it in people’s heads.

Michael: Discussion of full level of complexity in ARA should involve the people there at the time and have the pile of use cases that led to us making the thing. Discussion should be rooted in the actual use cases we were forced to think about. F2F is a good place for that.


#### Topic 4: [https://github.com/w3c/ppa/issues/153](https://github.com/w3c/ppa/issues/153)

Martin: Other thing to think about is the database of conversions. Someone wants to query. How do we not leak info from the database which is a cross site leak. 2 parallel processes when this happens, one to do the work, the other to report. But if the timing doesn’t work then nothing goes out. This is awful, but thus far the only mechanism we’ve figured out to avoid the timing problems that otherwise manifest. If you have too high a complexity it just times out and maybe that’s ok. But we should figure it out. Brian pointed out different people have different computers at different speeds so what would the fixed timer be for timing out that is effective?

Brian: Could someone see the difference in CPU load?

Martin: There are ways but none are particularly perfect. We’d apply the same sort of properties we’d do on the web currently. Two tabs can be using the CPU and can link information to each other through that, but we could avoid.

Brian: Could we add noise in the way of random pauses?

Martin: We could add some padding but the signal is very large that we have to hide, if you have 1000 impressions in the DB or 2 will create a very visible signal if you can measure the CPU cost.

Brian: I was thinking about more granular signals.

Martin: Finer grained signals are easier to cover.

Brian: Don’t you get so many people who have low and high threshold impressions and that wouldn’t give much meaningful signal.

Martin: No this is about sending a message across domains and this could be used as a covert channel. If I want to identify Brian and I know who he is on one website I could send 10million impressions and the other website could look at a measurement process that sees there are 10million impressions than I know it is Brian.

Brian: Conversion sites are particularly problematic because they are reading the database and have incentive to do impression-site identification.

Martin: Yes. It’s tricky.

Michael: Rate limiting argument here.

Brian: Some limit needed.

Martin: Not sure that this helps. Computers can take a lot of this type of data.

Sean: Let’s close. No real action items other than noting some important issues to follow back up on.


#### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

None


### AOB

None


### Future Meetings Information & Logistics

June - F2F - Fill out [form](https://forms.gle/LSs5xoL5NzFwneAA9)!

Sean: I’ll make sure to get people to sign up on the form so the building can get people in. Hopefully two weeks before we can lock that list of people in.

Sean: We need to cancel the PATCG meeting that follows the June meeting b/c most of us will be in the air or in other meetings in that area. I’ll propose.

Michael: Let’s do CG focused time?

Sean: We could do it. CG people would call in?

Martin: Later is better for folks in the US.

Aram: Maybe we do a few hours of CG there, at the end of the day. Let’s make it known that we want that schedule locked in in advance so we can carve the time out of the WG schedule.


### Review of Action Items From This Meeting

No new Action items.

## Participants



1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Tara Whalen (W3C)
4. Martin Thomson (Mozilla)
5. Michael Kleber (Google Chrome)
6. Brian May (unaffiliated)
7. Arpana Hosabettu (Google Chrome)
8. Aloïs Bissuel (Criteo)
