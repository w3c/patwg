# PAT WG Minutes - 2026-02 Meeting
## Scribes

* kleber

## Proposed Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes
2. Review of Action Items from Previous Meeting(s)
3. PPA Discussion Topics
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)
4. AOB
5. Future Meetings Information & Logistics
6. Review of Action Items From This Meeting

## Introductions, Code of Conduct, Minutes Document, Scribes

*Very positive "We're a lot closer than we were last time" vibes in the general pre-meeting chatter*

## Review of Action Items

No change to the first 3 AIs.

- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
	  - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
			- [ ] Will keep open for now \- tracking via [PATCG \#227](https://github.com/patcg/meetings/issues/227)
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com)Develop Web Platform Tests in PPA (repo)
	  * Aram: Create issue for people to document their important flows for future test coverage
	  * Will keep open for now
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [Issue \#256](https://github.com/w3c/attribution/issues/256) should be reviewed by the Community Group
	  * What issue was this intended to refer to — [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)?  We did tag it as a blocker.
	  * Revisit at TPAC CG session.
- [x] ~~[Sean Turner](mailto:sean@sn3rd.com) Add “Pinned: “ to beginning of Issue [\#2](https://github.com/w3c/patwg/issues/2), [\#3](https://github.com/w3c/patwg/issues/3), [\#23](https://github.com/w3c/patwg/issues/23)~~

## PPA Discussion Topics

###

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

[https://github.com/w3c/attribution/issues/237](https://github.com/w3c/attribution/issues/237) and the work in [https://github.com/w3c/attribution/pull/309](https://github.com/w3c/attribution/pull/309)
Martin: This looks like a huge thing but really it's just putting the work in to change the methodology we've talked about into a spec
Ben Case: Clearing budget state, resetting user activation flag, etc. Issues set up tracking each of these.
Martin: If someone disables the API, the website is not supposed to know, even giving back fake reports. Ben is implementing the user activation thing, where someone has to click on the webpage for the API to become available to the website.  The first website calls into the API, at the window level, which makes the API available thereafter.  Once someone navigates away, the flag gets reset, API becomes unavailable until the user clicks again.  Current implementation is effectively the same as disabling the API.  Martin suggests that the website knows whether or not there's a click, so there's no harm in our letting the website know, in this case.
Ben: But could a website use that to learn whether or not the user has opted out?
Martin: Behavior is same whether you've turned the API on or off, so there's no problem.  I suggest we throw the exception at the same time as you do the input checking, much earlier than you check whether the API is disabled.
Ben: What exception do we throw?  Then they know something…
Martin: As long as we do all the same things before checking the API activation status, we can't possibly leak anything.
Martin: Don't want to generate fake conversion reports in the non-click situation either.
Michael Kleber: I've be If you are talking about a conversion time event in which the user has added to a cart and then the last thing they do is click “purchase”, the click on the purchase button causes a navigation, the thing that is recorded as a conversion is the purchase success, which might happen on the next page, then is that flow compatible with this?  Or do we run into a case where the click happens, navigation happens, and there is no way to record that on page 2\.
Ben: That navigation will activate on the destination page.
Michael: That new page will have user activation on?
Aram: It lasts for a period.
Martin: We discussed having multiple conversions for the same activation, but right now it is just one page that gets to use the API.
Aram: [https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/User\_activation](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/User_activation)
Michael: OK, as long as this doesn’t break the case that is commonly used.
Aram: The cross-domain thing can be a problem where top of funnel opens a new domain faster than an event occurs, but most, if not all, will involve opening a new page.
Martin: There also might be clicks on the destination page, which gives them opportunities in a multi-site flow.  Bouncing through many intermediaries, each of whom can access the API, is what would be a problem.
Roxana: Need to bound the number of different per-site quotas following a single user action.  As long as we bound that, it's fine with a little accounting; we could change to max=2 instead of max=1 — would make sense if this is a common pattern.  The accounting would have to be done by retaining the set of domain names as part of this user action context, though.  Can we retain this across a transition?
Martin: What matters is just the top-level page that the user is on when the API is activated; it does not matter to us what party calls the API on the page.  The new page could lose that flag if it's a problem.  It's about the point at which you attach the boolean; if the window goes away then the flag goes away.  That's easier to reason about than a complicated across-the-browser state, which would need to get into whether the flag is cleared at things like switching tabs.
Martin: This is pretty clear now; there is a little more work to be done, e.g. the locking stuff, but nearly done.
Andrew: Working on translating this from the spec into the simulator (and therefore finding type errors), will keep going.
Martin: We need a set of recommendations for the budgets that apply.  These new quotas that apply on the global scale (which is a budget) and the one on the per-site scale.  Currently no floor or ceiling on the per-site quota.  Ben has made some recommendation that the quota is at least some multiple of the unspecified number.
Ben: Putting the user-agent-specified parameters in a separate section.  Have an impression site quota, which is some multiple of the budget, roughly the number of advertisers you expect to use the API, say 10ish?  Then there's the global budget which needs to stretch over all sites consuming the API.  For the spec, should we recommend these both be set as a multiple of the per-site?  Or just let implementers choose all three?
Martin: In Roxana's work, I think it became clear that it's important to understand them relative to one another, so probably as multiples is a good idea and we should talk about the numbers.  Initial had a 10x multiplier, so any impression site could drive conversions on 10 sites.
Ben: I think 5x-10x is roughly what you see in data, but depends on your epoch length — need a larger multiplier for a weekly epoch than for daily.
Martin: Impression site quota is not active in making a privacy benefit, it's really about abuse mitigation.  The global one was originally recommended as 100x, i.e. if there are 100 sites a user could convert on.  But if you're looking at epsilon=10, then a global epsilon of 1000 is effectively meaningless.  That's why I suggested we talk about it.
Roxana: We will update the arxiv version of the paper soon.  We talk about methodology for setting parameters.  I think you should consider tuning these quotas based on benign workload parameters plus some extra to buffer for adversarial behavior — to make it resilient rather than make it fall down as soon as there is adversarial usage.  The paper only addresses the benign use case, but we could capture this also, if necessary.
Martin: That would let the API to function for some amount of time even under an attack.  Perhaps we don't need to say anything in the first version of the pull request, come back to it in a second version — start with the benign workload version, say that implementations can choose more to allow additional flexibility or to make it more robust to an attack.  Seems reasonable to add.

[https://github.com/w3c/attribution/issues/190](https://github.com/w3c/attribution/issues/190)

Martin: Might be interesting for a website to be able to query the impression database and remove ones that it no longer thinks are relevant.  (Change in partners, recover from error, etc.)  Also, if a website asks to store 1 million impressions, browser needs to say 'no' eventually.  Should we give guidance about what to do?  Hard-code instagram users will eventually see an awful lot of impressions recorded; we probably don't want to interfere with that. But at some point the browser needs to say something, and should decide when and how.
Martin: Wrote some cases for why we need to step in. User leaves right after coming to site with no interaction? Storage limits? Side channel risk? User preference? Many sites trying to store impressions and too much in the system as a whole?
Aram: Even sites outside of social feed could lead to thousands per user in a single session. Could certainly see multiple thousands for our heavy users of our games pages. Ad industry has standards for number of ads on a single pageview and amount of time the ads are shown — e.g. only 5 ads in the viewport and refresh them every 30 seconds (there is talk about dropping to 20sec); these give you a sense of how to get a limit, e.g. "only as many impressions as you could get from 10 ads per 15 seconds".
Martin: Ultimately this is complicated, and we should probably give guidance rather than set hard limits.
Ben: How would it work for site that the ads are on vs the intermediate whose job is showing the ad?
Martin: Must be on a per-site basis.  Don't want to punish an intermediary for being on a lot of different sites.
Ben: If there's a limit for an impression site, then multiple intermediaries competing for the same cap budget of impressions
Martin: Probably shouldn't be a hard cap, should drop some eventually, like the oldest ones, ones from high ratio of impressions-to-time-on-page, etc.
Aram: Another factor we could consider is that known users are more valuable than unknown users, so if we knew that the user was "logged on", those impressions would probably be more worth retaining.
Martin: That works right until someone decides it's beneficial to fake it, so not clear that there is infrastructure to make that work
Ben: Could let sites set priority themselves?
Aram: Sites do have a better sense of what makes a user a good user, so could set higher priority
Michael: Risks an arms race of different intermediary ad networks setting higher priority than one another to make their ads retained rather than others.
Andrew: Already an issue in a different way for the various impressions targeting the same conversion
Martin: But those already need to work with one another.  Ads that just happen to be on the same page have no reason to want to cooperate, so adversarial assumptions needed here.

Q: Sean: Are any of these new issues CR-BLOCKERS?  They aren't tagged as such.
Martin: Setting parameters is not, most of the other new ones probably are.  Hmm, global attribution flag probably is not cr-blocker actually.  Atomicity and clear-history do need to be sorted out in some way.
Roxana: Atomicity one?
Martin: Need to check multiple budgets, then change multiple ones.  Need to do that in a single transaction.  Solved by acquiring mutex during the read-modify-write,
Roxana: We do that in the current paper.  We were thinking about relaxing it.  Could be looser with the quotas, the quotas would still provide the resilience properties.
Martin: Why is there any need to relax the atomicity?  There's not much cost to consider here; probably don't need to worry about it, unless there is some privacy leak risk here.
Roxana: If there are multiple sites that could have shown impressions, and one of them is out of quota, then you won't return impressions in the other one either, because of atomicity.  But from the resilience-to-attacks perspective, we should be more careful.
Martin: Ah, that is a different type of atomicity.  We should insulate the rest of the process from the one site that has burned its budget.  Should file its own issue for this situation.  Would you just drop impressions from the site that has used its quota?
Roxana: Not in the paper yet, not as simple as eliminating I think because it would cause some budget problem.  Needs to be done carefully; we will address it in a future revision of the paper, don't have the algorithm yet.  Mostly wanted to ask if it's relevant to work on.
Martin: \<will open a separate (not-cr-blocker) issue.\>

Q: Ben: Is there an issue on the DAP change?
Martin: Overview from last meeting is pretty much spot-on: aside from a minor thing, we need task-level separation, where a "task" has all the same metadata.  Within a task you can have individual aggregation jobs.  Core design binds the identity of the website and intermediate into the task.  Reports from one website/intermediary can't be mixed into a single task.  Then get a report, and information about how much budget might have been spent.  Folks working on DAP need some shuffling between documents to make this all work, in progress, DAP will end up better as a result.  Everyone is happy with it, just work still in progress.

## AOB

## Future Meetings Information & Logistics

[Sean Turner](mailto:sean@sn3rd.com) Confirm with Martin that we are still on track for Berlin in May.
Martin: All looks good so far, will let you know once it's official.

## Review of Action Items From This Meeting

- [ ] Keep old ones
- [ ] Nothing new to add

## Participants

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Andrew Paseltiner (Google Chrome)
4. Tara Whalen (W3C)
5. Alex Turner (Google Chrome)
6. Michael Kleber (Google Chrome)
7. Martin Thomson (Mozilla Firefox)
8. Benjamin Case (Meta)
9. Roxana Geambasu (Columbia and part-time Google, in Columbia capacity here)
10.

Regrets: Charlie Harrison
