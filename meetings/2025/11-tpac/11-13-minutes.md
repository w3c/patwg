# Private Advertising Technology WG - 2025-11-14

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Michael Kleber (Google Chrome)
5. Aloïs Bissuel (Criteo)
6. Charlie Harrison (Google Chrome)
7. Andrew Paseltiner (Google Chrome)
8. Matthew Finkel (Apple WebKit)
9. Benjamin Case (Meta)
10. Maxime Vono (Criteo)
11. Thomas Prieur (Criteo)
12. Tara Whalen (W3C)
13. John Wilander (Apple WebKit)
14. Ehsan Toreini (Samsung, observer)
15. Alex Turner (Google Chrome)
16. Vinod Panicker (Amazon Ads; observer)
17. Martin Thomson (Mozilla)
18. Erik Anderson (Microsoft Edge)
19. Nour Nabil (Google Chrome)
20. Takaaki Nishioka(LY Corporation)
21. Jay Kishigami (WCAP)
22. Isaac Foster (Microsoft Ads)
23. Phillipp Schoppmann (Google)
24. Isaac Z Foster (MSFT Ads)
25. Roxana Geambasu (Columbia University)
26. Tatsuya HAYASHI (Digital Agency, Government of Japan)

## Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes
2. Review of Action Items from Previous Meeting(s)
3. A DSP view on Attribution API [\#64](https://github.com/w3c/patwg/issues/64) @tprieur @alois-bissuel \- 45m
4. Finalizing CR Blockers [\#66](https://github.com/w3c/patwg/issues/66) \- @martinthomson \- 1h 30m (expected)
5. *BUMPED TO CG*: Next Steps to Level 2 [\#67](https://github.com/w3c/patwg/issues/67) \- @bmcase \- 1h (expected)
6. PPA Discussion Topics
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)
      1. POSTPONED: [Optimization Query Evaluation Framework](https://github.com/w3c/patwg/issues/60)
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)
7. AOB
8. Future Meetings Information & Logistics
9. Review of Action Items From This Meeting

## Hellos, Intros, Policies, & Call for Scribes

Scribes: Matthew Finkel & Erik Anderson

Policies: See WG Read All About It Slides.

## Review of Action Items

- [ ] Aram [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
    * Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
    * Will keep open for now \- tracking via [PATCG \#227](https://github.com/patcg/meetings/issues/227)
- [ ] Develop Web Platform Tests in PPA (repo).
    * Aram: Create issue for people to document their important flows for future test coverage.
    * Will keep open for now.
- [x] ~~Brian and Miguel to provide feedback on [Issue \#204](https://github.com/w3c/attribution/issues/204)~~
    * Leaving for Level 2, but need to decide if we need this functionality. Suggestion is to delay this until people start asking for it. This involves some significant trade-offs with the additional complexity that it requires.
    * \[x \] Editors should label it as Level 2.
- [x] ~~Brian and Miguel  to provide feedback on [Issue \#222](https://github.com/w3c/attribution/issues/222%20)~~  
    * Miguel couldn't join today, but will add some references for this to a recent IAB Tech Lab document on "IAB Tech Lab Attribution Use Cases:" [https://iabtechlab.com/identity-addressability-privacy/attribution-use-cases/](https://iabtechlab.com/identity-addressability-privacy/attribution-use-cases/)
    * Merged now.
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [Issue \#256](https://github.com/w3c/attribution/issues/256) should be reviewed by the Community Group!
    * What issue was this intended to refer to — [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)?  We did tag it as a blocker.
    * [\[For PPM\] PPA queries in DAP](https://docs.google.com/document/d/1-BOnlUus2LtFk-Oesauijrb5PFaUeyBqVwhTfeEIFcQ/edit?tab=t.0#heading=h.q0k70yp654td) — what we should test for, maybe this is what we're looking for feedback on? This should be easy.
    * We will try to bring it up for discussion in the CG meeting in two days.
    * Was discussed at CG.
    * Revisit at TPAC CG session.

## A DSP view on Attribution API [\#64](https://github.com/w3c/patwg/issues/64) @tprieur @alois-bissuel \- 45m

Slides: [https://github.com/user-attachments/files/23512583/TPAC.2025.presentation.-.A.DSP.view.on.attribution.API.pdf](https://github.com/user-attachments/files/23512583/TPAC.2025.presentation.-.A.DSP.view.on.attribution.API.pdf)

Similar presentation to last TPAC. View of ARA (unfortunately recently deprecated) and Attribution API from Criteo’s perspective, and how Attribution API can be improved. As a DSP, they are measuring an ad impression and learning what is a “good” impression. On the conversion-side, they don’t care about good impressions, they only care about which channel provided the conversion. They don’t need a cross-site identifier or link but they need a URL param to attribute to a channel. For training machine learning they do need to be able to attribute to a specific impression. Google Analytics is used for a large percentage of measurement. In order to get a good ROI, the measurement company needs to be able to link the conversation to an impression.

Tested ARA for 4-5 years, enabled on all traffic. Leverage learnings from ARA to improve Attribution API. ARA was good for basic reporting, and post-click at the campaign level. API dedicated to ML with good information on the impression-side. ARA provided cross-environment attribution, which was of high value to the industry (impression on an Android app, conversion in Chrome). ARA allowed for requerying and reprocessing the data multiple times. The median conversion events per campaign were 1-2 per day per channel, for most advertisers. ARA didn’t handle omni-channel, cross-device, etc., and these are some open questions for Attribution API.

Q: Vinod Panicker: What were the types of conversion events that were available for this?

A: View through or click through. When you assign per channel per day it just isn’t that many conversions.

Attribution API: Pros and Cons

- Criteo likes the simple API structure and primitives and having no constraints on event types. Attribution API when used at scale could exhibit privacy budget depletion by other adtechs (intentionally or unintentionally). This requires clear coordination.
- Attribution API doesn’t have native ML support.  
- No standard epsilon / noise.
- No cross-advertiser aggregation, long tail of small clients with conversions are too small for aggregation.

> John W.: What would you do with the long tail?

> Alois: Aggregate on the large advertisers, use first party data. Or aggregate on a larger window, then larger aggregation with all long-tail clients.

> John W.: Has this larger aggregation been discussed before?

> MT: Yes, need to make sure there is budget available intended to work on cross-advertiser aggregation as a feature for this approach.

> Roxana: We have been working on the depletion issue, how to handle smaller advertisers, and support for ML training.

> Issac: Currently the advertiser’s relationship with adtech is multi-layered and complex.

- Limited way of integrating data from the conversion in the histogram. Put as much information in the histogram as possible. Using filters with AA is possible, but it gets quickly becomes interesting on the display-side.

> Charlie: You want to break up the histogram by the conversion key, as well? Is it just the function of the conversion context or also impression context?

> Alois: Just coming from the conversion context is okay should be sufficient.

> Charlie: Unknown whether this is already supported. Depends on how the privacy budget server is supported. Every encrypted report contains a set of keys. Requires that a single report can’t be queried more than once, but it could be okay. Feature request for the privacy budgeting service that is just not specified yet.

- Don’t know: impact of the “new” user-level privacy granularity on the utility. ARA is per-click, Attribution API is per-user per-window. Unknown how it will play out in the data.

ML on aggregated data

Need to support at least GLM (incl. Logistic regression \- generalized linear models). Use an aggregation service for outsourcing gradient calculation. The gradient can be expressed as a sum.

They considered learning from a noisy label proportions by taking noisy CTRs. They build a production-ready (but not used in production) using the Privacy Sandbox Aggregation service. They found not-ideal, but acceptable performance with epsilon greater than 10 on a weekly window.

They didn’t use user-level DP. Showing the two graphs. Top shows the training-loss function with DP, and the bottom is without DP. In order to perform aggregation, ... . The gradient converged within \~2 week with DP, but without DP it too much longer due to the additional variance.

MT: Histogram size: because it’s aggregated, how many features are you using?

Alois: Many optimisations. Many features including categorical. In order to balance with privacy, must reduce space. What we found, losing 1% per power-of-2.

MT: double the size of the histogram, get 1% back?

Alois: Yes

Ben Case:

Michael: Are you using summation? That is a very inefficient use of epsilon. If we build dedicated ML training infrastructure for this, then epsilon could be much lower.

Alois:

Issac: \+1 to training in the TEE. Were you taking a subset of campaigns? 

Alois: Just to capture the displays they have, calculated how close they could get.

Charlie: Let’s say we don’t make any changes, and we ship what we have now? Would you adopt it?

Alois: No cross-advertiser aggregator is a hard problem, and may prevent adopting. Privacy budget depletion is a large concern, too. We may need some view-through with ML support.

Vinod: Invalid conversion events. What happens to those? (e.g., what happens if a user double-clicks?)

MT: This api doesn’t really help, it doesn’t track clicks. That would be your responsibility. Adtech on the conversation-side would be responsible for measuring the conversion.

Aram: Which piece of this flow are you concerned? Amazon owns most parts.

Vinod: DSP.

Aram: The code would decide how to handle the second click.

Charlie: IVT filtering. At the point the impression or click happens, you are responsible for deciding whether the click should be registered. There is another notion of offline filtering for after-the-fact. We could maybe add support for this, but it isn’t currently supported.

Vinod:

MT: Is the measurement provider that would generally call this API.

Vinod: So it is the measurement provider that must decide if the click was valid?

MT: Yes.

Vinod: It would be good to mention invalid clicks in a considerations section.

Issac:

Vinod: Concern that the measurement code could be suppressed because publishers are incentivised to inflate the numbers.

Issac: Multi-advertiser case: there are some use cases for filtering on multi-advertisers but not group by multi-advertisers. Does increasing the grouping have problems?

MT: The API is just noisy aggregates.

Issac: For optimization, it’s helpful to aggregate with different sets. Of course if you are going to bill by advertiser you need to filter by advertiser.

## Finalizing CR Blockers [\#66](https://github.com/w3c/patwg/issues/66) \- @martinthomson \- 1h 30m (expected) {#finalizing-cr-blockers-#66---@martinthomson---1h-30m-(expected)}

What is needed to move to CR? Goal is to:

1. decide it’s not a CR blocker,
2. close it, or
3. assign an owner.

### [153 – Side Channels for measureConversion Operation](https://github.com/w3c/attribution/issues/153) 

Andrew: The primary output of the measureConversion operation is an encrypted report. The side-channels from the operation could leak too much information. There are a few approaches we can take. Leakage: timing, CPU usage, memory usage, etc.

MT: Baseline expectation of isolation in browsers. In the ideal case, there isn’t any information that will leak across context. We don’t want to make the situation worse. Initial thinking is that we’d address the time-issue directly. Use a timer to create a fixed-time operation. This may result in a useful report, but there are other channels that may be measurable such as CPU usage during that time.

John W.: Is it sync, async? Can we delay for an indeterminate amount of time?

Charlie: We do need to resolve within a reasonable amount of time. The longer we delay, the higher loss we’ll see.

John: Could we have a “send the report here”?

Charlie: Yes, but that will require a lot more infrastructure.

MT: That’s still accessing shared resources.

John: Yes, but it sites don’t know when it will happen.

MT: We’re probably talking about \~1 ms, but we just need to figure out how to do it. We can look at the \~99th percentile and set the cutoff there. How far do we want to go?

John W: For Level 2, maybe add the ability to upload a report “eventually”.

MT: We could. The privacy properties change a bit, so we need to figure those out (e.g., leak client IP address to adtech).

Michael: \<summarizes MT’s proposal in \#153\>. Simple and very successful protection for preventing the leak of information on the number of entries in the database. Let’s do this for Level 1 and think harder for Level 2\.

Issac: How changeable is this later?

MT: It’s going to depend on the type of change that is proposed

Issac: How stable must this be when it goes to CR?

Aram: It needs to be relatively stable.

MT: We do have some nobs, like increasing the timer.

Charlie: What is a blocker for CR? It’s okay to go to CR and get feedback and make changes, and that’s expected. CR Blocker: “Is this required for presenting to more people, get horizontal review, industry feedback”.

Andrew: Mostly echo Charlie/MT. We can make a note that implementation should attempt to avoid side-channel leakage.

Aram: Consensus that current proposal is sufficient for moving on to CR. No objections noted.

Of the 3 options, the WG went for \# … 3 (?). Add the delay/timer as a mitigation.

### [TEE-based aggregation integration](https://github.com/w3c/attribution/issues/130)

Michael: \#130: we should remove the label. We have sent an intent to deprecate and remove ARA to blink-dev last week. We’re going to be turning down that infrastructure. Not sure what will happen with TEEs and this API in the future, but certainly not in CR-blocking stage anymore.

\[various folks\] We should merge two: [\#69](https://github.com/w3c/attribution/issues/69) & [\#237](https://github.com/w3c/attribution/issues/237)

### [69 – Safety Limits](https://github.com/w3c/attribution/issues/69)

Presented by Benjamin Case

Ben: This is related to the first of the points that the Criteo folks made. How does privacy budget get shared across parties. Roxana and folks have a paper out.

Ben: High level options for adding safety limits to Attribution Level 1\. First option, no safety limits; remove as CR blocker. Leaves us vulnerable to privacy attacks but not DoS. Option 2 is a global limit, a partial implementation of our proposal. And then full proposed algorithm. The hard part of the full algorithm is how to define user action so that we can define how the quotas are used to link user action.

Ben: We probably do want to include safety limits as a CR blocker. We want the spec to be in a state where when people review it, they will give us good feedback. This is on the order of how we manage the privacy, preserve the guarantees of the system, and fully fleshed out for people to give feedback.

Ben: Overview of Big Bird algorithm– Roxana in Paris went over this a good bit. First component: global privacy limit shared across all sites that use the API. This is a backstop. A shared resource with a larger budget. Backstop against major attacks against the API. But introduces denial of service attacks– what if the budget is exhausted? Quotas can limit access to global privacy limit and other privacy properties on their own right. Impression site quota, how much can be enabled by this site. On the conversion site you can also have a quota; for this conversion site, how much budget can be spent through the site? In level 1, could do something similar for conversion site– same as impression site quota. Could consider larger conversion site quota for supporting intermediaries, many measuring with their own budget. In a level 2 design, might support ad tech mediary queries. Third factor is the limit on the number of quotas. The user has done something– navigated to a site; how many quotas should there be? Limit on quota actions is not a limit on number of times the API can be called; you can call it as many times as you want, register as many impressions as you want, but don’t redirect through many sites that all log– that depletes the global budget.

MartinT: This is protection against bounce redirects exhausting the budget.

Ben: Big Bird approach: Global budget per epoch across all sites. Impression site quota, take into account “how many advertisers are measuring impressions.” Need to support multiple advertisers. Get benefit of on-device individual privacy budgeting— only pay for what you use. Register impressions for 100 advertisers, expect 10 to have conversions; only pay for 10\. Impression site quota only needs to support 10 advertisers.

Ben: Conversion site quota can set equal to per-site budget. In level 2 increased to provide budget to intermediaries.

Ben: Quota use limit, limit redirection to some legitimate use cases. The difficult part is defining what this is.

Ben: Limit on quota used per user action. Single action measured via a limited number of quotas. Not just a mechanism to reduce exhaustion but denial of service motivation is a substantial one as well. Don’t want chain of redirections to deplete budget.

Ben: What is user action here? Need discussion. Maybe navigating to a site for some period of time, indicating you intended to go to it. Maybe clicking something on the site. When your quota is in scope, no limit on the number of API calls, impressions that can be registered– but need to address what happens if the user actino expires. If that happens and the user is still on the page, somewhat still using it, do you need another user action to use the API again? Charlie gave the example of “someone listening to music on a tab, getting audio ads.” How do we know after some time that the user action of navigating to page has expired, but they’re still listening and still getting ads, how do we keep registering impressions? Need to think about this carefully.

Ben: Here’s the algorithm. In terms of options from the beginning– if we can align on what it means to have a user action, my recommendation is to put the full Big Bird algorithm into the spec. Work to spec it, but gives benefits– full set of protections and it makes it easier when we go to CR stage and want to explain to people, we can point them to the paper and how the method works so it can be reviewed. Option 2 is still an option, a partial implementation of safety limits, but need to clearly explain what we get and the properties of the system. But recommend we align on user action so we can get full algo.

Ben: User quotas is another piece. We need them if we use this “count quotas” which also covers conversion site quotas. Conversion site quotas configured but set to something reasonable for level 1\. Would make it easier to add new things in level 2\.

Isaac: I’m sure there’s a reason, but we’re asserting the trigger is browser defined? Can’t be something dropped on the page to let advertiser choose what it is? Is that right?

Ben: You’re asking about if the user action is browser defined?

Isaac: Today, whoever says conversion happened controls saying “it happened now.” They choose the user action they care about.

Ben: The threat model is the site trying to use the API. The browser needs to define it. The browser notices the user has taken some action. Can’t delegate that to the site calling the API.

Isaac: Even just saying the API choosing what click….

Ben: The site chooses to call the API when it wants to. We will always respond in the same way when they try to call the API. What this is saying is, “suppose some attack is trying to be done by the site, redirected to multiple properties– all trying to log a conversion– we’ll say, ‘you hit the limit on quotas that can be used in this scope here’; browser is making the determination that the user didn’t initiate that, the user didn’t remain on site long enough, so we won’t log the impression.”

Isaac: Does the user action need to be the same across all usages of the API? Or different per impression because interested in different events? Going back to audio event, “this is playing for 2 minutes.”

Ben: don’t need a single action definition, but probably want a short list of them. E.g. “user navigated to the page,” “user clicked on something,” “audio has been playing still for some amount of time,” etc. Hard part to define.

Martin: Navigation is an obvious one here. In the audio example, 30 seconds have passed with no proximal user interaction– but the navigation one still provides quota. But the question if if there are scenarios with redirects where you might lose access to these quotas. No need to deactivate the quota once you’re on the site. Automatic redirect could trigger deactivation.

Roxana: It’s about unique domains that can be claimed as measuring one thing that the user does. Only when you change domain. You can measure/do all sorts of things if you stay within the same domain. You’ve associated a single budget with the domain.

Ben: What if the tab is open in the background for a long time? Aram has mentioned this before– open in background a long time and get a huge number of impressions. Shouldn’t expire after some time?

Martin: Still spending from the same quota.

Roxana: exactly, same quota– doesn’t matter.

Martin: Tab in background redirecting, we might disable access to additional quotas at that point.

Isaac: The case you’re talking about is background tab, continuous refresh with impressions showing (no view check– good or bad reason), that is an additional source?

Ben: Would allow impression site to consume all of the impression site quota. But limited to the one quota.

Charlie: My concern with Big Bird is that I feel it is very complicated. I feel like, as a developer, I may not have a good understanding of how to optimize my privacy budgets under this kind of system. I wonder if we can achieve the privacy goals of these global limits with something much simpler. Maybe it’s not as fair, but maybe it’s fine– I’ll make a statement and people can respond: most people in a week do not visit 100 unique domains; can we say your 100th conversion measure call won’t do anything. We limit the number of domains that can call measure conversion in a single week. And then we don’t need to implement all of this. Doesn’t solve all of the abuse that Big Bird does, but addresses privacy concern is a simpler way. Big Bird feels like a lot of machinery to introduce– impression quota, conversion quota, these algorithms, user-gating which is potential source of bugs. Reminiscent of ARA, we added all of these limits… weren’t as buttoned up as Big Bird but… we added limits to achieve something stronger and it was very hard for people to debug if they were running into rate limit issues. I worry quota system will look similar. I wonder if something simpler would get us what we need.

Ben: Do we still need the user action so you can’t redirect through 100 sites?

Charlie: Something simple like “navigated from user gesture” and then it’s fine. I’m sure we can look through some of our logs we report in Chrome. I’m pretty sure if you set the number to like 100 (not sure if that sounds big or small to people), but I expect that will be fine as a hard limit for a level 1\.

Thomas: A campaign for us runs on 10s of thousands of web sites. I don’t know how we identify which web sites/domains were used within this limit.

Martin: this is conversions, though. How many times did the user convert?

Thomas: Not source of triggers? Okay.

Martin: Yes, a lot less of those.

Roxana: Baseline option 4 reduces privacy in some way but it does nothing on its own, but with user action-based thing it might.

Charlie: Correct.

Roxana: You can put a limit on the number of domains, but the global privacy budget is like any other budget. A lot better if you just have global budget tuned to whatever limit, e.g. number of domains, the privacy loss optimization we’ve baked into per-site budget… the consumption of the global budget with the per-site budget is why we have that. How you configure the global privacy budget is based on how many sites can call it. We need some sort of budget like this. But it won’t be better in terms of giving access to queriers the most privacy budget.

Charlie: You’re saying, Big Bird solves for privacy and DoS attack but also gives us better privacy utiltiy tradeoff w.r.t. The global budget.

Roxana: Yes, that was our focus. Our design aligns all of these things. Otherwise one limit will be reached and the others won’t. Key was to put everything in concert. Only when bad situations happen does the right limit get triggered.

Charlie: From my perspective, I’m imagining that it will be very hard to have some kind of a global epsilon compatible with any sense of utility. Do you disagree with that? We can have a meaningful epsilon? I was imagining this as a backstop, not meaningful. Do you think it’s actually worthwhile to optimize this thing to get something super meaningful and can advertise as doing the real thing?

Roxana: Good question. I don’t have the data to know how to configure these things, so I don’t know how to answer this. In the Criteo data set, which underestimates the capacity of these things, but we gave a generous budget. I can’t tell you this, but here’s how I’m thinking. Global budget, in my opinion, at least in the way we designed Big Bird, is to set global budget based on per-site budget and some sort of parameters that describe the number of players/parties to support the workload you want to support. It’s not focused on privacy but more focused on utility first. You anticipate a workload to support, set the global budget. No good justification to say we’ll just have extremely loose, beyond what we have… why wouldn’t we do the best? An actual DP guarantee.

Charlie: Good point, no rebuttal.

Roxana: We tried really hard to keep this as simple as we could. Aside from the user action piece, it’s really hard. It’s exactly the thing you have in fact… not exactly the same, small changes, as per-site budgets. Instantiate global budget from per-site budgets.

Charlie: Instantiating a quota is going to be confusing. I myself have trouble wrapping my head around it. Not sure if for other folks it’s easy.

Roxana: What’s hard is this user action constraint on the number of quotas. That’s the harder part. But mechanics of quotas is the same as the per-site budget, all aligned/the same.

Aram: Interested to see from Criteo thoughts from scope of what we look at. Ad click typically gets redirected through a lot of things. Don’t know if users are not clicking that often on an ad. But when they do finally click on it, they get redirected through a lot of stuff. What happens if I’m in the metrics business and am one of those redirects, how do I get limited in that process?

Ben: Option 3 would consume a lot more than option 4 when redirecting through sites. In some sense you might suggest that the API isn’t called by the first of the possible ones, but actual conversion site when you ran that. Maybe you can say the user hasn’t spent time on these sites yet, so don’t make API available. But these parties would probably come here and say “it could be useful for some measurement we do on behalf of the advertiser.” Good question about how much redirection currently happens.

Aram: It’s a lot. We have little control over what a redirection agent does. We should act on the assumption that every entity passes through a redirection chain will attempt to get as much data as it can get when this API goes live.

Roxana: You answered my question… these are all top level domains and they all call the API. That is the pattern we need to limit.

Charlie: From our experience with ARA, this mostly happened with HTTP redirects, not full page redirects. HTTP-based redirects supported each one having an HTTP header they could use to register. That was designed to attribute the conversions to the publisher that had the ad. Didn’t have that many use cases where CNN redirects to random-domain.example and they want to register an ad to that site random-intermediary-domain.example.

Aram: They will want to measure as a conversion for them.

Charlie: I think that is supported.

Roxana: Would have one single top-level domain you can refer to.

Charlie: I claim this is a non-issue.

Roxana: When there is an impression on the destination site, conversion or impression… maybe you can already tell this is a single navigation.

Aram: I think there are two versions of it. You raise a separate flow: I am only a redirect agent for registering this in my service request logs. In the situation where you open an email, a common tendency in the marketplace, e.g. Substack, you get a Substack link in your email client. Substack takes you to Substack, wants to register conversion as a publisher. Does a non-HTTP-redirect to final link. Bluesky also does this. Allows it to take credit for being the referrer. Also helpful for ads as the publisher in this context. That’s not an HTTP redirect. Code that’s running.

Isaac: Ultimate target URL is not embedded in the click you’re clicking on. Determined in best hop.

Aram: Bit.ly does this. If you know the ultimate target…

Isaac: If you could embed something on the page when you call the API, this is the target…

Charlie: We solved this in ARA, and we hoped we wouldn’t need to do this in Attribution API. The actual navigation could register for the API. If you click on [bit.ly](http://bit.ly) link, it sets the headers as it wanted. Main thing is it still attributed to the publisher. I don’t know if this should be in scope for the API. What is being clicked on is not necessarily an ad. It’s a tracking link that wants tracking but not for ad measurement. Are they ads?

Aram: Substack is a better example. If I’m a client of Amazon’s partner program. I sent a substack email with partner program links to products, someone clicks on that partner program link. Substack wants to register the impression. True of any email newsletter process where the newsletter has attribution-based sales. They land on Amazon… Substack is who gets credit for it. I think that’s a distinctive use case in the wild. (EDIT: I’ll add this is also a common flow for podcast tracking)

Isaac: [Bit.ly](http://Bit.ly) or any URL shortening is a thing that extends things (IZF: the ultimate target is dynamic and not clear to the API caller at call time). If the thing calling the API says this is the ultimate place to register, but having a query param that gets passed through you can do that.

Aram: How do you know you have a user action? If I’m the developer how do I know I’ve ‘missed’ the user action?

Erik: Email security providers also do a non-HTTP redirect first hop.

John Wilander: Re: the music scenario/audio ads scenario before. Also thinking of Picture-in-Picture there. Should think through those. Re: user action. Could be “has interacted with this site in the past few days” so we know it’s something the user interacts with. Or “user gesture in this tab” but then continue on; if you had pressed ‘play’ maybe it’s good enough now. I don’t think we need any special requirements on code execution. If they’ve been suspended, that should automatically be handled (can’t call it). I think this user interaction requirement gives us one more thing, which I have typically called a “rare event” to push back on abuse of these data-limited APIs. If there’s a rare event requirement, the ability to pump data is tied to the scarcity of the thing. Also helps us fight fraudulent impressions as well, people trying to register impression– if they need a rare event– then it will limit how much they can register fraudulent impressions.

Isaac: Impressions or conversions.

John: Both. And then one more thing… for non-ads things, we have influencer attribution. We got feedback when we stopped tracking… influencers are on top of social media platforms. The platforms don’t want them to take the attribution, they try to put an intermediary hop on it so they can take the credit.

Aram: Similar to substack flow.

Michael: In my experience those influencer systems do try to give you an entire platform to give these people a direct level of support for attribution and affiliate payments.

Aram: I think youtube is pretty unique.

John: We have heard from others using other platforms that it is adversarial.

Ben: At Meta I believe we have something similar to Youtube.

Martin: That’s all impression side. We’re talking about conversion side activation. I don’t know if we can tie that to user activation in the same way.

John: Activation applies to conversions too.

Martin: absolutely, usually want that to make sure the conversion is real. Otherwise people feed you visitors not actually interested in your website and they just leave. From my perspective option 3 is probably the most viable of these. Option 4 is a little more difficult because you have the same user activation requirements if you want to avoid the background redirect loop, which I think we need to avoid–otherwise someone will exhaust all of Criteo’s budget. We need something to protect against that. 4 helps to a degree, but you need the activation requirement. Browsers track navigation and if it was user-activated or not; should be easy to attach to window and if someone calls the API bring it down to half mast, then on redirect take it down.

Aram: half mast?

Martin: API is disabled, then user clicks something and API is active, maybe redirects happen. Third state is someone has called the API and it’s available to them until a non-user activated navigation occurs. Redirect agent wouldn’t have access. I think this is implementable.

Roxana: Can’t deactivate the API because it leaks.

Martin: It still looks like it works but you get nothing.

John: “Defang”?

Martin: Yeah…

Thomas: A limit around 100 at first look is reasonable. Might need to be refined.

Ben: To wrap– user activation is something we want. Maybe not hard to get agreement. Can follow up for level 1 between options 3 and 4\. Or maybe we convince Charlie the scary algorithm is only incrementally more complex.

John: I was surprised by Charlie’s comment. Engineers love specs they can write against, write test cases for, etc. This is concrete\!

Charlie: To be clear, I am not concerned about browser implementation. It’s about developer understanding. My strawman for secret option 4 is to push that we should explore a simpler option. But Roxana has convinced me.

Roxana: The algorithm is actually a bit tricky because of this user activation tie-in. But for the implementer. I say this because the way you should configure the parameters, including the user activation count, if you configure it reasonably, e.g. 2-3, whatever you expect, it should hide any normal behavior that doesn’t redirect through many domains consuming budget. Shouldn’t affect normal behavior if you configure it reasonably. I think I’m hearing \~2. Only attacker will be bounded/affected by this. Unlike the quota budgets… which is a bit different and you need to hide it for multiple reasons… it’s aligned. In all of this API you can’t tell when you run out of *any* of the budgets; you will need to grapple with it no matter how you define the budgets.

User activation: [https://html.spec.whatwg.org/\#tracking-user-activation](https://html.spec.whatwg.org/#tracking-user-activation)

Ben: count after user activation we can probably align on. For other budgets we should be pretty loose until we know how the API will be used.

Sean: For resolution, I think we’re getting to “assign an owner.”

Aram: Document very clearly what the user action is. Many people will be engaged with that, the less confused they are when they see it the better.

Ben: Happy to help.

## Issue grooming

### [\-256 Prio benchmarks and communication cost](https://github.com/w3c/attribution/issues/256)

**https://github.com/w3c/attribution/issues/256**

Martin: I propose we remove blocker on [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256). I think we have enough of an understanding that we can ship a thing. Options that we can explore to improve those costs.

Charlie: Did we document anywhere? Let’s close it out when it’s documented.

Martin: Agree, but I want to remove the CR blocker tag.

Charlie: Sure, that’s fine.

**https://github.com/w3c/attribution/issues/173**

Martin: I’ll take [https://github.com/w3c/attribution/issues/174](https://github.com/w3c/attribution/issues/174).

**https://github.com/w3c/attribution/issues/127**

Martin: I think we need to fix the Storage Access API one: [https://github.com/w3c/attribution/issues/127](https://github.com/w3c/attribution/issues/127). I’ll take it.

Martin: Anti-replay is to make sure the measurement service doesn’t enable measuring multiple times.

Michael: Looking to define it, or just state expectations?

Multiple folks: Just the latter.

**https://github.com/w3c/attribution/issues/69**

Charlie: We should close the safety limits one (\#69).

Martin: We assigned it to Ben just now…

Charlie: We should close it as a dupe now, since the other one is the plan of record now.

\<duped \#69 to \#237\>

Martin: On HTTP API conversion side, we talked about including that in scope last time. We need a plan for that.

Charlie: Is it pressing or can we remove the tag?

Martin: We have save impression, but not measure conversion. How comfortable are we with conversion side needing to run JavaScript.

John: Salesforce told us that they have pixels which, if they need JS access on destination sites, so many have policies against deploying JS and they get nowhere.

Charlie: We’re well aware of these issues, the question is– and we see this with banks that don’t want JS… or for legacy reasons they use a pixel–can we accept the 80-90% solution for the candidate recommendation while we work on this in parallel? To me, that’s fine. If people say they want it, we’re working on it.

John: Risk of hanging pages with JS that assumes JS will run.

Matthew: Just about level 1, not being a CR blocker.

Charlie: What did you mean by “hanging the page”?

Aram: Seems less likely to happen. People get guidance to not blindly try to invoke a function. But it could happen, but they probably have broader problems than just calling this API correctly. I agree the header should be in level 1, but shouldn’t be blocker.

Martin: This is probably the first thing after a CR we should do. How the browser gets the answer to the web site is the really hard part– fetch integration, what headers to use, how to use them, how many times you can measure per request, etc.

Michael: John proposed something a bit ago, deliver it async over HTTP.

Martin: We have a sketch of the first, easy half. Second half will be hard but not completely unreasonable. We’ll get this done when we get it done, but won’t block on it.

Matthew: Do we need a new label?

Martin: Use the level-2 label. Level 2 means “not now” but not a precise priority for planning.

Martin: As far as issues review right now goes, I think we’re good.

### Context on next set of meetings

Sean: December meeting, will start planning for 2026\. Will try to have another F2F. Since TPAC is in October in Dublin, will probably shoot for elsewhere. March/April timeframe. Will contact people for locations with conference room infrastructure. Potentially not in the US.

Aram: Do people think they can make a Dec. 9th meeting? Holiday stuff falls fairly early in the calendar.

Michael: will be 2am east coast US time.

Sean: OK, we’ll set things up with same frequency.

Michael: I'd like to propose a toast: to 2026, the year of moving this spec to CR and also implementing and shipping Level 1 in Chrome and Firefox and Safari and Edge.

## Bumped to CG: Next Steps to Level 2 [\#67](https://github.com/w3c/patwg/issues/67) \- @bmcase \- 1h (expected)

## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

* [See CR Blocker discussion.](#finalizing-cr-blockers-#66---@martinthomson---1h-30m-\(expected\))

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

## AOB

None.

## Future Meetings Information & Logistics

- [ ] 09 December 2025 (Virtual)  
- [ ] 2026 Schedule  
    * Do we want a face-to-face meeting e.g. in March or April?  
    * TPAC'26 is October 26-30, shoot for part way in-between
