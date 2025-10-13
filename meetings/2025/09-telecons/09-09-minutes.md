# PAT WG Minutes - 2025-09 Meeting

## Joining Instructions

See Web Calendar: [https://www.w3.org/groups/wg/pat/calendar/](https://www.w3.org/groups/wg/pat/calendar/)  
Slack invite: [https://www.w3.org/slack-w3ccommunity-invite](https://www.w3.org/slack-w3ccommunity-invite)  
Our group is called [“patwg”](https://app.slack.com/client/T010EGK9PQE/C07LUM00Z6E)  
Zoom: [https://w3c.zoom.us/j/89760362590?pwd=bQxy6olLu4sfbZtq2oBsSU5Hv74okF.1](https://w3c.zoom.us/j/89760362590?pwd=bQxy6olLu4sfbZtq2oBsSU5Hv74okF.1) 

## Scribes

* kleber  
* 

## Proposed Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes  
2. Review of Action Items from Previous Meeting(s)  
3. PPA Discussion Topics  
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)  
      1. TBD   
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)  
4. AOB  
5. Future Meetings Information & Logistics  
6. Review of Action Items From This Meeting

## Introductions, Code of Conduct, Minutes Document, Scribes

Reading of the riot act, rules of the road, etc…

## Review of Action Items

- [ ] Aram [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
      - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (repo)  
      * Aram: Create issue for people to document their important flows for future test coverage  
- [x] ~~Aram (following Brian May’s write-up): Submit issue to *CG* agenda around definition of last-touch and if we need more in the enum for level 1 logic options.~~  
- [ ] Brian and Miguel to provide feedback on [Issue \#204](https://github.com/w3c/attribution/issues/204)  
- [ ] Brian and Miguel  to provide feedback on [Issue \#222](https://github.com/w3c/attribution/issues/222%20)  
      * Miguel couldn't join today, but will add some references for this to a recent IAB Tech Lab document on "IAB Tech Lab Attribution Use Cases:" https://iabtechlab.com/identity-addressability-privacy/attribution-use-cases/

## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

#### [https://github.com/w3c/attribution/issues/44](https://github.com/w3c/attribution/issues/44) on multi-touch attribution

Proposal: simple API change: you sort the impressions that might get credit (e.g. sorted by recency and by click vs view), and you add an array of weights, along with the max-contrib and current-contrib.  If your array of weights has length n then you're allocating value among the top n impressions from your sorted list, and the weights let you allocate to those impressions all the same weight (i.e. \[1,1,1\] would weight the best 3 all equally), or could allocate differently.

Brian: People are going to want to generate feedback on impressions from multiple campaigns simultaneously, to ask questions like "which campaign is producing results?" or "which ones are overlapping?".  This API doesn't address this use case.

Martin: It would be very helpful to write down a use case that we're not addressing, so that we can try to understand it deeply.

Aram: Question for Brian: The idea of tracking overlapping campaigns and understanding where they overlap may have privacy challenges.  Are there ways to address that goal without that problem?

Brian: I think one of the primary uses for attribution reporting is to evaluate different strategies against each other, and if you can't do that then our work isn't going to be useful.  Strategy means two different kinds of targeting rules, for example, and I need to know which rule is working, which is performing, how much do they overlap?

Aram: Can that be effective without understanding where specifically they overlap, just learning the fact that they overlap?  If I'm running "people interested in boats" and "35-y-o males", the fact that they have independent records means they can still be useful even if you don't know where they overlapped.  But if you do know where they overlapped then you have a privacy issue.  You have two treatments: a1/a2/a3 ande b1/b2/b3.  a2 and b2 are the same impression at the same time.  Is that required to understand effectiveness of a/b?  I understand the fact that they are duplicative is a problem, there may be waste.  But is it possible to get effective results from the A and B campaign information separately without revealing the overlap?

Brian: As long as I'm getting full information, yes — as long as when I get no B conversions I know it's because the B campaign did not show to this converting user, then it's OK

Martin: We've talked about combinations of impressions where we run whole programs that process all of the impressions.  That's out of scope for now, there are technical challenges we need to overcome.  I think the basic one that we're talking about here will be enough to give you your measurements — if all you need is three different treatments and know whether each one performs, then you're good.  You do need to worry about the distribution of your epsilon budget to each of them, but otherwise you should be fine.  No precise cross-tabs, but can measure each individually, all at the same time.

Brian: So you're suggestions treat each campaign as an independent conversion, from the conversion call point of view?

Martin: Yes.  In a perfect world they would each be completely independent.  If you have cross-talk and people who see ad A1 are less likely to be moved by ad B3, you won't pick that up. 

Brian: If I can see all the impressions and I can apply it to all the campaigns, that's what I want.  If I only see some of my impressions, I'm losing information.

Martin: At conversion time you get to query over all the conversions, you just make very limited queries over that set (sorting and top-n).  You are constrained to putting values in histograms.

Brian: Yes, I understand

Martin: Multi-touch is the minimal step up over last-touch.

Brian: I was under the impression that there were limits on the number of impressions you could interact with when you apply attribution.

Martin: Just a question of how thin you want to smear the credit.

Charlie: The other thing is that if you have a lot of impressions and you're just doing last-touch, then in this proposal you have to specify exactly how many impressions you want to capture.  If you want to capture up to 100 impressions, then you need to pass in an array of size 100\.  Then if you have 101 impressions, you will drop the lowest-priority one.

Brian: I'll have to look.  If I pass in 100 weights and I only have 1 impression, does that skew things?

Charlie: Current implementation: We would give that 1 impression all the credit — you do proportional allocation of the value.  If you have a conversion of value 5, you spread it across the histogram, so that the contribution total is 5 across the whole histogram.

Brian: I will review and write something up.

Charlie: We have a demo that Andrew put together, maybe you can use that for testing the API.  It's serving live on [https://w3c.github.io/attribution/simulator.html](https://w3c.github.io/attribution/simulator.html).  Put in all the impression and conversion information, and you get out what the API will spit out, before the encryption and aggregation steps — what would be in the report that gets sent to the aggregation machinery.

Martin: Tells you "You've got 14 zero values, and a one in this bucket."  It's basically complete except for some budgeting.  Play with it and have a look, it's a little cumbersome to fill out fields and set things up, but gives the results.

Brian: Who do I ping on slack for questions about it?

Andrew Paseltiner: You can ping me

Charlie: Just ping the whole group and we will take care of you.

Martin: It should map directly to API calls, if you care to dig in.  We can improve the UX.

#### [https://github.com/w3c/attribution/pull/271](https://github.com/w3c/attribution/pull/271)

Ben Case: There are some places in the spec where we're describing what we can prove formally, in an ideal model.  The per-side guarantees rest on some assumptions, which are necessary for the proofs to hold up.

Assumption 1: There is no cross-site data adaptivity of queries being made.  
Assumption 2: The cross-site limits (safety or rate limits) can introduce more cross-site dependencies, which will keep us from proving per-site guarantees.

First assumption:  If we're dealing with multiple sites that all influence the same user's behavior over time, that could well happen in the real world.  But that means that one site can have its outcomes influenced by a second site.  Second one: just a consequence of having safety limits.

Analysis of the budget that holds globally, across sites, doesn't depend on these assumptions at all.  But the per-site limits, which is what we think is important in practice, do have these assumptions built into them.  Not clear that these introduce any kind of channel that can be exploited, just want to make the assumptions clear for the proof.

Brian: Use of "site" is ambiguous, can you clarify what role the site is playing?

Martin: Site is precise here, might make sense to talk about impression and conversion sites.  Only information is revealed to conversion sites.

Ben: It's anything that has a per-site budget being tracked, which in this case is conversion sites

Brian: Only obvious to people who have been doing this for a long time, let's make it explicit

Ben: Paper will be in a more updated state on the arXiv in a week or two.  Speak up if there are any thoughts about us adding this to the spec.

\[Martin: On that second point, shared resources create some privacy exposure risk, see [https://brave.com/privacy-updates/13-pool-party-side-channels/](https://brave.com/privacy-updates/13-pool-party-side-channels/)\]

#### [https://github.com/w3c/attribution/issues/273](https://github.com/w3c/attribution/issues/273)

Martin: What's left to do?

* The safety limits work that Ben was just talking about  
* TEE implementation  
* side-channel discussion

I think maybe get to CR snapshot sometime this year.  Does that seem right to everyone?

Aram: Seems plausible.  Tests are going to be a big thing.

Charlie: Can you give folks a reminder about what CR snapshot means, from a Process POV?

Sean: The fact that it's testable and you have that thing live, and you test against the API to get platform tests operational, is a big deal.

Martin: Part of the exercise might be going through the list of issues and labeling the ones that are blocking?  

Charlie: Yes, let's add a "blocking" label to issues in github, so that we can be clear.  I raised a few issues that I thought might qualify — small things that might be deal-breakers but are not hard, just haven't gotten the little bit of attention they need

Sean: Will make one

Martin: I just did

Aram: We chose "Continuous Release" process model in our charter (we think?).  [https://www.w3.org/policies/process/\#transition-reqs](https://www.w3.org/policies/process/#transition-reqs) "Working Draft" is where we are now.  In order to reach Rec status, which is our goal, we would go through a few processes: Working Draft, then becomes a Candidate Rec — satisfies the technical requirements, consider this version of the doc ready for technical review from the wider community.  The Snapshot, and then Draft, then transition request, to indicate the doc is at a greater level of maturity and ready for review by a wider community.  Then if the W3C approves, goes to Candidate Rec stage.  Then goes through horizontal review process, then by the TAG.  (Martin: Privacy, Accessibility, Security, and TAG are basically all horizontal review groups)  We may need to make additional changes, if there are substantial changes might need to get reviewed again.  Then goes to Recommendation status.

The continuous review model takes the idea that there is a "sliding window" — as we get this particular version of the rec moving forward along that track, we can also work on the next version with new features at the same time.  

Sean: Charter recommends 3 months before CR process.

Aram: Everything on the page, but not the tests ready yet, is a good time to kick off the transition request process, knowing it will be 3 months.  There's also [https://www.w3.org/wiki/Evergreen\_Standards](https://www.w3.org/wiki/Evergreen_Standards) to read, same idea.  Any questions?

Martin: The process document has a detailed list of conditions for transitioning to CR, [https://www.w3.org/policies/process/\#transition-cr](https://www.w3.org/policies/process/#transition-cr)

Aram: Also in chart form in [https://www.w3.org/2020/Talks/TPAC/W3C2020/?full\#7](https://www.w3.org/2020/Talks/TPAC/W3C2020/?full#7) though it's slightly outdated.  The tricky part of transitioning to Advisory Committee Review is that we do need to show implementation experience, so need to point to the fact that browsers are implementing and people are using it.  How people would implement it needs to be documented.  Needs stakeholder review.

Martin: May make sense to send to horizontal review teams at this stage anyway.

Aram: Once we show wide review, we may mark some things at risk of being removed.  For privacy review, we should mark which parts of the document they should pay particular attention to.  And show that it might be adopted, then we can make a transition request.  Sorry if we're missing anything; our first time running through the process

Martin: Doesn't technically require Web Platform Tests, but we should seriously consider working on those too.

Tara: [https://www.w3.org/policies/process/\#implementation-experience](https://www.w3.org/policies/process/#implementation-experience) need to show "adequate implementation experience".

Aram: Web platform tests help show this mechanically.

Brian: What is our strategy for Wide Review?

Aram: We say we're looking to move it, put it to the CG, reach out to stakeholders that we know, ask them to chime in, positively or negatively.  Wide Review includes that list of internal groups but also external stakeholders.

Brian: Maybe IAB Tech Lab, Prebid, and other industry groups where ad techs tend to congregate.

Aram: Our charter also lists some such groups.  IAB Tech Lab and Prebid are probably wise ones to go to since they would probably be involved on a technical level at implementation time.  Publishers would be useful too, but not sure what good environment there is, I can find some people to point it out to that I know.

Aram: Some issues to resolve, get a start on the tests but that's not necessary to start engaging.

Charlie: Regarding tests: API is complex enough that maybe can't work on tests before we have any implementation in a browser, the API has many moving pieces that we might miss if we write the tests without an implementation.

Martin: The thing I'm more worried about is how to deal with the encrypted blob that comes out of the API.  Maybe something where encryption is a no-op and we specify some kind of test aggregation service in the browser?

Charlie: But also there are keys and time etc.  I feel like we should potentially write tests at a higher level, of what we have in the simulator, e.g. with the timestamps.  But I don't see much value in writing web platform tests before we have any impl in a browser.  Until then we are sort of guessing or doing best-effort, and will need to rewrite the whole test harness afterwards.

Brian: Write an outline of what we want to test?

Charlie: That's what I was suggesting.  The level the simulator works at — the properties of an impression, properties of a conversion, histogram output.  Doesn't require any weird things in the browser (like "fast forward time by two weeks").

Martin: I'd like to have that be the input format for WPTs as well.

Charlie: If we write the WPT harness right, then maybe we can convert them in the future.  Maybe writing WPT ahead of time might be helpful, but I think writing them concurrent with implementation would work well enough.

Martin: I'd like to be able to feed the corpus of test cases to a browser, workable but we will need to do work.

Aram: This question, and the tests, aren't required for advancing to the next step

Martin: But just working on the implementation in the simulator revealed that there are problem.  The tests really are helpful.  I'm most concerned about getting the draft to the state where we're confident that it's complete, that's the next target.  The requirements for that match what I think we need to be doing anyway.  Go through issues, flag any that you thing need to be addressed before we move on.

Brian: Should we write things up in a way that a tool can generate tests for us?

Charlie: That's what I was suggesting, we have existing tests in /e2e-tests, have JSON format tests showing inputs and expected responses.  In theory that could be converted to WPT that could be run automatically.  I think it's the most natural way to write a test — just logging all the events that the browser is receiving.  Only concern with WPT is mocking time, making them run in a reasonable amount of time, etc.

Brian: Any narrative that goes along with it, to explain all the JSON to people?

Charlie: I think comment fields are supported, but there's always room for improvement.

Andrew: There are links to spec steps for tests that are targeting specific things

Martin: They are pretty self-explanatory.  Feed it to the API and it should work.  Using a fake aggregation service in the tests.  Ultimate goal would be to build up a corpus of tests in JSON format, build a driver, run all those tests in a real browser against a real implementation.  We'll need hooks to reset privacy budgets and things like that, and in a real implementation the outputs would be encrypted to go an aggregation service.

Brian: Agg Service would need to be tested independently though.  Will that be implemented by clock time?

Martin: The agg svc people can take care of that, they do need to think about it but it will be separate.

Brian: Anything besides time that isn't embedded in the data but would affect tests?

Andrew: The implementation gets to pick a bunch of configuration parameters like budget limits, etc.

Martin: Not trivial, asking browsers to change all of these things, but it will be very valuable to have interoperable tests even though browsers might make different param choices.  Configuration that we use in these tests might not match what browsers are using for actual implementation.

#### Picking which issues should be CR blockers (doing \#273)

Going through open issue list [https://github.com/w3c/attribution/issues](https://github.com/w3c/attribution/issues), oldest-to-newest, applying label "cr-blocker" when needed

[https://github.com/w3c/attribution/issues/25](https://github.com/w3c/attribution/issues/25) \- closed  
[https://github.com/w3c/attribution/issues/26](https://github.com/w3c/attribution/issues/26) \- editorial no need to block on it  
[https://github.com/w3c/attribution/issues/31](https://github.com/w3c/attribution/issues/31) \- might be hard to test \- is it a blocker?

* Already did this for impressions which maybe was the lion's share of importance; do we need it for conversions also?  
* Chrome's experience is that there are indeed advertisers unwilling to host 3p JS, so we will need it at some point, just a question of whether we need it now or could do it as a fast-follow?  
* Aram: I do have concerns — once JS is on a page, hard to get it off.  We're a privacy-based API, we should go out of our way to cater to the needs of privacy-conscious sites.  Would have a lot of value, if it would be additional years of work then maybe could put it off but unless that's the story, I think we should have it now  
* Brian: Agreed, and also I think this would be good to show during wide review.  
* Martin: This is big-ish, maybe the difference between finishing this year vs next year  
* Charlie: I think it's not the end of the world if we want to block on it  
* Brian: Label to block on it for the time being  
* Aram: This is worthwhile enough that we should delay on it if it would be a month, irrespective of any end-of-year kind of hoped-for deadline.  This will be key in some ways  
* Brian: Also think about the blog posts tearing into us for forcing JS  
* Aram: We're going to get attack block posts anyway\!  
* label added

[https://github.com/w3c/attribution/issues/32](https://github.com/w3c/attribution/issues/32) \- Discussed numerous times. Not going to happen in first go. Lots of other delegation related issues.

[https://github.com/w3c/attribution/issues/33](https://github.com/w3c/attribution/issues/33) \- Closed\!  Martin did this

\#34 also on delegation, also not happening now.

\#35 would need to be fixed in DAP spec, keep it open to track, nothing to block on 

\#36 closed.

\#38, \#41: delegation as well, skip

\#42: Closing, addressed by Charlie's recent work on priority values.

\#43: DAP spec is clear on this, but we need a clear statement that we don't like replays, which we don't yet have.  Martin thinks this is a blocker, but just an editorial fix, not challenging.

\#44: Closing, as addressed by array-of-weights simple version, good enough for now.

\#47, \#48: more delegation; skip

\#51: App-to-web: Have not done anything on this; would need Apple folks to talk about it.  Aram: Should add some thoughts on what work-arounds might exist (link-forwarding agent)?  Martin: Maybe more complicated.  We should defer this.

\#52: this is easy

\#58: Integers?  BigNum?  Do we need to decide now?

* Andrew: Don't need to decide now, related to questions about worklets in the future.    
* Charlie: My feeling is we might want to bundle all the declarative API surface into one Issue; whether or not that issue blocks CR Level 1 is still TBD — the question is whether you can do anything useful with the declarative API.  Multiple histogram buckets?  More flexible match value?  Level 1 should enable some utility with the simplest API you can think of, and in prev meetings the JS API is the right path forward there.  Are advertisers going to oppose JS that runs in an isolated environment with no access to page contents?  Need to validate assumptions on JS worklet thing, which is safer than JS-in-iframe, but who knows what the security positions of different financial institutions are.   
* Martin: We can improve the declarative stuff further, but gets increasingly difficult.  For this we have explicit limits, 2^52 and 999,999,999,999,999.  
* Charlie: We should have in spec that the maximum is the max.  "We have a system that is filtering based on string, and would be nice to keep pretending that, so just hash the string" — 32-bid hash probably not enough, 52 is much better even if not cryptographically secure.  
* Not CR-blocking, but gives us something to think about for the future.

\#69: blocker defining global limits based on Roxana's report

\#127: This is hard, need an expert to walk us through it.  Sean: Who?  Martin: We have folks in-house.  Label as a blocker, figure it out, then can decide based on that whether we can remove the label or need a fix.

\#130: Technically we could go to CR without this, but wouldn't do any favors to people who want to use a TEE.

* Brian: Get feedback from community?    
* Charlie: Issue exists on performance characteristics of MPC implementation, which will affect priority of TEE impl.    
* Martin: Histogram size is a distinct factor.  I'm inclined to keep this as not-a-blocker for now, but we should listen to voices that say otherwise.    
* Brian: I suggest we *do* label as a blocker to force us to come back do it.    
* Martin: could label as "at-risk" in the spec?  Then if implementations don't show up, it doesn't make it into a full Rec.  Don't think we're at that point yet.

\#146: Currently don't care about hostname, [https://localhost](https://localhost) would be fine, [http://localhost](http://localhost) would not.  

* Aram: I think this is fine, https for local testing is not an issue any more, we shouldn't even think we need to solve this.    
* Charlie: for powerful feature we gate them on the potentially-trustworthy check, lots of things in web platform work that way, so my preference is to just align rather than rolling our own check.  If we want to change recommendation, do it at the potentially-trustworthy layer, not in our API  
* Aram: throw some sort of warning if it comes up, to avoid silently failing and confusing developers

\#151: editorial

\#153: side-channels should be a blocker, we're working on it.  Remove editor-ready label.

\#157: Already addressed

\#174: We should set minimums also, to ensure interop.  Blocker

\#189: blocker

\#190: we should do this at some point, per-site or global limit, but nobody feels it's a blocker

\#204: Hard, already agreed to defer

\#212: Nearly-but-not-quite fixed, editorial, definitely doesn't need to block anything

\#237: blocker, implement Roxana's work as applies to current spec.

\#256: 

* Martin: will be part of people's assessment of whether API is fit for purpose.   
* Charlie: CR is when we look for reviews outside our group, I would like this reviewed by our group first.    
* Martin: Is it being done a precondition for getting public review?    
* Charlie: Worst case scenario is it's so bad we get laughed out of the room; that seems blocking.  Then we have to do work to improve crypto side.    
* Ben: This is certainly the first thing people ask.  So maybe at least have numbers?    
* Charlie: prepare two slides and present to CG, gauge reactions when potential users are in the room.    
* Martin: Won't cost us much, the divvi-up folks have run performance tests  
* Aram: Added agenda item to talk about this to CG

\#270: blocker, can't be off by a factor of 2

\#272: ugh, let's put this off

### [~~"Other" Issues~~](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

~~Aram: Proposed \- We should prob discuss waste as a separate use case~~

## ~~AOB~~

~~TBD~~

## Future Meetings Information & Logistics

- [ ] 14 October 2025 (Virtual)  
- [ ] TPAC 2025 (November, Kobe Japan).

## Review of Action Items From This Meeting

- [ ] Develop Web Platform Tests in PPA (repo)  
      * Aram: Create issue for people to document their important flows for future test coverage  
- [ ] Brian and Miguel to provide feedback on [Issue \#204](https://github.com/w3c/attribution/issues/204)  
- [ ] Brian and Miguel  to provide feedback on [Issue \#222](https://github.com/w3c/attribution/issues/222%20)  
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) \#256 should be reviewed by the Community Group

## Participants {#participants}

1. Sean Turner (sn3rd)  
2. Aram Zucker-Scharff (The Washington Post)  
3. Brian May (unaffiliated)   
4. Andrew Paseltiner (Google Chrome)  
5. Martin Thomson (Mozilla)  
6. Benjamin Case (Meta)  
7. Tara Whalen (W3C)   
8. Charlie Harrison (Google Chrome)  
9. Michael Kleber (Google Chrome)  
10. Roxana Geambasu(Columbia)  
11. Arpana Hosabettu (Google)  
12. Thomas Prieur (Criteo)
