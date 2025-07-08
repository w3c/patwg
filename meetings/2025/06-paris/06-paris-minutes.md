# Private Advertising Technology Working Group / Community Group Minutes - 2025-06 Meeting


## Agenda



* Day 1
    * Introductions, Code of Conduct, Minutes Document, Scribes
    * Review of Action Items from Previous Meeting(s)
    * 45m - Proposed Methods for Managing Global Safety Limits in PPA
    * 45m - Per-Site Budgeting: Support for Adtech Queries, Optimizations, and Semantic
    * PPA Discussion Topics
        * "Discuss" Issues
            * Intermediaries using the Level 1 API
        * "Other" Issues
* Day 2
    * PPA Discussion Topics
        * "Discuss" Issues
            * Intermediaries using the Level 1 API
    * "Other" Issues
* AOB
* Future Meetings Information & Logistics
    * TPAC
* Review of Action Items From This Meeting


#### IRC and Slack

Use Slack for the backchannel please. Do not use Zoom chat. \
 \
[https://w3ccommunity.slack.com/](https://w3ccommunity.slack.com/)

[https://www.w3.org/wiki/Slack](https://www.w3.org/wiki/Slack)


#### W3C Read All About It!

[Policy Slides](https://github.com/patcg/meetings/blob/main/2023/06/14-london/W3C%20Read%20All%20About%20It!.pdf)


## Day 1: PATWG


### &lt;= 30m: [Hellos, Intros, Policies, Google Doc, Call for Scribes](https://github.com/patcg/meetings/blob/main/W3C%20Read%20All%20About%20It!.pdf)


### Proposed Methods for Managing Global Safety Limits in PPA ([agenda issue](https://github.com/w3c/patwg/issues/48)) ([slides](https://docs.google.com/presentation/d/1BtWj89kHunJIEMPEC6xVYYsfkFeA_AJ5kdEZy4Um2m0/edit?usp=sharing))

Per-site budgets has been the approach thus far.  Privacy loss is limited toward specific sites. But attackers can make new sites to avoid these caps.  Global limits protect against loss of privacy.  But there isn’t any detail about how to structure this budget.  In particular, there are potential DoS attacks against these limits. Mostly concerned about Sybil attacks that aim to deplete budget (like DDoS attack).

Cartoon example with [x.ex](x.ex) which uses multiple redirects.  A single visit to the attacker can deplete the global budget. Discussion of weaker variants that would be harder to detect and block.

Treat this like DDoS: don’t block attacks completely, but aim for graceful degradation in the presence of an attack. Make it tunable to be able to support normal workloads under benign conditions.  Force the attacker to mount a wide scale attack, over many interactions and many sites. Avoid heuristics (as much as possible), because we don’t know how API usage will develop.

Overall budget management proposal – Big Bird. Picture of a member of the Privacy Sandbox naming committee (emeritus).

Assume user interaction is the costly resource.  Privacy is only consumed when there is a user interaction (impression) and conversion both happen.  Stock is created through user interaction.  Flow is triggered when a conversion occurs.  Only when both steps occur, does privacy loss occur.  Attacks seek to trigger both stock and flow.

Big Bird attempts to limit both stock and flow.  Impression quotas limit the amount that a site can contribute to the (stock) consumption of the global budget.  Conversion quotes do the same on the flow side.  These quotas do not serve any privacy purpose, but serve to protect the global privacy budget. Deductions happen similar to deductions to the global budget but for the site the quota applies.   They shield the global budget from excessive expenditure. The goal is to ensure that an attacker spends budget more like a regular site.

Algorithm:



* At measureConversion, we compute privacy loss.
* Deduct from all budgets and quotas: conversion site budget, global budget, per-action quota (counter), impression quota, conversion quota.
* Only spend the budget if all of these filters have capacity.
* Only send a non-zero report if budget is spent.

Q: Do the impression/conversion quotas apply per-site?

A: Yes.

Q: If there are multiple sites with impressions, do they each have budget deducted?

A: Yes.  These quotas are potentially at a different level to other limits.

  They are applied per-epoch as we do for the existing budgets.

Q: could we be deducting from several per-site quota at once?

A: Yes, all matching relevant impressions considered in assembling the response to the query

Q: does this work the same for ML style queries?

A: Not in Level 1, but yes the design generalizes to other queries such as could be used to support ML queries.

Q: for step 5, which steps create a new quota filter?

A: These filters are created on demand. The last step (avoid having too many new filters created as a result of a query) is not implemented in our library, yet. Some details need to be worked out.

Problem is in deciding how to set the quotas. We need to ensure that we set values for these based on our expectations of what a “normal” workload is. We set a few key parameters that can be estimated by how people use the web. These include the number of sites involved, of each type, plus a few other parameters.  Maybe we can take the 95th percentile across a typical usage profile and use those to guide how the different filters are set up.

The parameters can be padded to allow for some extra use, but that creates an exposure to the attack. The goal is to force attackers into the same profile as this “good” profile.

Graceful degradation: attackers ability scales with the number of explicit user actions they can elicit across their sybil domains.  To disrupt benign queries must exhaust the global budget across many devices (to impact query outputs substantially).

Balanced fair scheduler: static quotas can be hit by benign sites, especially for impression-side quotas.  Larger sites are more likely to hit this limit than smaller sites.  The idea is to have some amount of delay, reallocating budget at set intervals.  Right now, we have an immediate response, so we can’t use this.  No hope of delay in the present design.  Otherwise, you can batch and sort, where queued requests draw from a global budget.

Charlie Q: how long do you think delays would need to be?

A: we don’t have lots of data to support, but if epoch is a week, we’d suggest  daily batch delayed option

Charlie: depends how bad the online schedule is; we had delays in ARA, they bring lots of headaches – offline devices, other privacy leakages, lack of reliability.

Roxana: not pitching this hard, but this might work in other settings, like mobile apps.  Need an entity that receives delayed reports.

Charlie: if you can solve the other issues, would be very nice

Roxana: such as having a trusted backed to handle situations of devices offline

Arpana: (couldn’t hear): In the mobile context, you have less of the problems that the web has. Background tasks can be scheduled more reliably, etc..

Roxana:

Brian: can we see when null reports is going up and stop spending on the campaign

Roxana: we are working on a way to measure the bias introduced by these null reports but not in this paper.

Evaluation

On the new criteo dataset. Configure with p95 values. Caveat is that this dataset reflects a single ad tech’s view (not a browser level view); means that the load is low and budgets are unrealistically tight. Lots of users with very sparse usage (because it is one provider).  Would need looser budgets in practice, so attacks would likely be easier to mount than in our simulation.

We simulate some attacks on top of this data.

Evaluation under no attack: error (y) vs imp-quota capacity (x-axis). Reveals how this quota impacts the error. Compare three systems



1. Online big bird (new proposed approach)
2. PPA with global budget (comparison baseline)
3. PPA without global budget (ideal utility)

#2 and #3 perform the same in no attack, as that is what we chose the parameters to allow.  When we make the imp-quota very small do we see error impact.

Q (Charlie): Are you setting the conversion quota to what is recommended?

A: YES.  This is set to the same as the per-site quota we have currently (r = 0 in the paper)

Q (Charlie): What is the unit of the various quotas?  Are they measured in \epsilon

A: The value is 1 on the conversion side.

Q (Charlie): stock and flow are measured in terms of the same units we use for privacy loss?

A: Yes.

Q (Aram): does this adapt with changes in the workload?

A: You can, but I wouldn’t do this continuously, because an attacker might use that to stretch the parameters to allow more attack flexibility. This needs to work like anomaly detection in a way.  Parameters need to be tuned to what we understand to be a “normal” workload.  Browsers will need to monitor activity in order to see what fits, based on an assessment of what is normal.

Note that these measurements are based on queries and those involve a lot of users.  The quotas are applied on a per-device basis, but that will wash out in the aggregate.

Q (Brian): will this vary based on campaign types?

A: Sadly quotas can’t be negotiated on a per-vendor basis.  Maybe browsers can make some accommodation for certain providers.  The goal is to fix these on the whole. It might differ based on device type.  It’s not a campaign-specific choice.

Brian: Campaigns will have to fit within these limits?

Martin: we are setting things to support real use cases, so any one campaign is itself not going to trigger these quota limits. It would have to be a very abnormal campaign to trigger this

Q (Arpana): want to understand what the workload is, like do we have have a representation of the conversion rates? How do I relate this to the things I understand?

A: We’re using an actual dataset. I don’t recall what the statistics are for that dataset.  We could simulate these more with a micro benchmark.

Q: Charlie: Criteo downsampled at display level, do you account?

A: We followed the Criteo recommended transformation to account for this.

Roxana: but given the sparseness of the data, don’t suppose that 8 as suggested here would actually be enough budget in practice.

Addendum: From [https://arxiv.org/pdf/2502.12103](https://arxiv.org/pdf/2502.12103):

> We decided to sub-sample negative examples to en-

sure that the number of clicks remains sufficient for

achieving representative performances, leading to a

click-through rate CTR ≈ 0.36 and a conversion rate

CVR ≈ 0.02, where a conversion here means a pos-

itive sale event

Q: Erik: the error captures what? From DP noise or quotas?

A: it is from the DP noise, that part is controlled. We set epsilon of the request such that the noise won’t give us more than 5% error for each query. That part is fixed, the desired accuracy impacted by the DP noise.

Erik: and queries made sequentially in time so you can see when some become nulls?

A: yes, and you see error increase here as a result of that primarily

Erik: what is the epoch length?

A: the dataset has only stable user_id for 1 day, so epoch is less than one day

Charlie: what is a user action in the dataset?

A: we use impressions or conversion as actions on the sites they occur.

Charlie: so we are assuming that the dataset is correct.

A: yes, this is for the benign case. For the attacks we introduce sybils that replay the user actions, mirroring the volume of actions on a “real” site on a fake, adversary site.

Q: (can’t hear): Thomas: The MSRE errors seem high

A: The p99 error is at the tail.  We try to control the p50 with our parameters. A lot of the noise comes from DP noise. The source of error only happens to come from the bigbird filters when the parameters are too tight (we recommend parameter set 4, errors come in at 0.5).

Q Erik: What is the intuition for how to read the source of error graph.

A: The quota filters are just not getting hit once you get past 1.  Only the per-site filter is getting hit.

Martin: The per-site filter is just one site hitting the maximum number of conversions. It isn’t influenced by the quotas.

Martin: a site could spend half its budget, but that would drive up the baseline error; but then be able to capture data from more devices.

Erik: bias accuracy tradeoff

Under Attack slide 36

PPA - with a global budget - jumps up at p50 and more at p99, so the attack is effective

PPA – bigbird – is resistant to the attack.

Second  figure shows who is running out of error. Now it is the global budget that is impacting the error. As before, if the budgets are too small, there is error from the filters.  And a small amount that leaks into the slightly larger filters.

This makes us hopeful this method can be helpful.

Q (Charlie): If the adversary induces a click for every thing they want to measure, what then? Can an attacker match the orange line (the global budget, without bigbird). Should I imagine that the attacker can force people to click so that they can force the system into the pessimal mode. Some tunable parameter p of what probability you can get the user to click on some attack generating action.  What did you assume to generate the plot?

A: We should expect the real attack to be somewhere between the good and the pessimal line.  It could be that a more sophisticated attack might be able to push toward the global budget line.  We need better information to model this out better.

…Right now we just replicate the top ten sites with actions and attribute those duplicates to the attacker.  We then assume some number of redirections for each of those actions (7).  We don’t make assumptions about what an attacker could do, or expect that sites can exceed this limit.  If it is easy to get people to click on things.

Erik: People who click on ads might also click on other stuff.

Kleber: Two different sets of people seeking clicks in competition, maybe not a good measure.

Erik: Some people are clicky-er than others

Kleber: if you have a popup ad and trigger actions on the close button, you get clicks.

Brian: Some advertisers try to put things under the right thumb to generate accidental clicks.

Roxana: the design seeks to align with intention. The key is understanding how often these interactions occur.

Brian: Is this an anti-fraud mechanism?

Roxana: This would rely on other anti-fraud stuff.

Kleber: Those seeking to commit fraud will go after the money directly, not to abuse the system.

Q: [Alonso] what is the economic motivation for an attacker to try and conduct these DoS attacks?

A: This is a new browser API.  It is going to be misused, so we need inbuilt defense.  Not speaking to whether there is financial incentive to attack it, just that we need to try to anticipate abuse and mitigate.  API itself does create incentive on its own to follow these sorts of abuses, even just to gain information from multiple perspectives as a check on others.

Aram: Theorizing… I imagine if you provide services to advertisers and compete with people in that market, you have competition and you have incentive to attack each other, or to get more information over your competitors.  This might come down to optimization as well, which might integrate into this.

Kleber: The money looks legit, but the fraud happened in the ML training phase.  The loser might not even know that they are losing, because their rate of dummy reports is higher than it should be..

Thomas: Most of the fraud is done by publishers, who try to get extra ad revenue.  This doesn’t directly help, but it is a low-trust industry, we need to prevent this kind of thing.

Roxana: we need levers for this; we have some recommendations and some things to look for, but this isn’t comprehensive. One idea is to make user interactions more costly. Browsers need to be able to make engagement trickier.  This might involve heuristics.

…We had this idea about registering sites. [Note taker: Google had this].  Intermediaries do not appear here.  Registration of intermediaries.  Intermediaries don’t receive quotas in this analysis at all.  These mechanisms point to certain defenses, but it will be imperfect.

Q Charlie: raise controverial point, slide 36, suppose we don’t have any global budget but still had the impression/conversion quotas? Do these plots show that even without a global budget the users won’t experience too much privacy degradation without going to the extreme of the distribution?

A: Roxana, a slippery slope. Global budget is the privacy guardrail. If the attack is not too big then yes the global budget won’t be hit and is like it never existed.

Charlie: so in most cases either privacy or utility is not impacted too much. If there is an attack, we get to decide if it impacts the utility or the privacy.  Want to see which type of attacks would actually be practical? Sybil attack seems unrealistic in most cases, especially for already large sites.

Roxana: i’m concerned about sites that aren’t already large who would have an incentive to do this and be less likely to be detected

Charlie

Erik: The problem is more on the conversion side than the impression side.  A large retailer might section off their site based on their product lines or type of product.

Aram: Are we counting subdomains?

A: No, this is about sites: eTLD+1/registrable domain.

Charlie: ad-tech attack DoS seems more likely an attack than the sybil attack   IT won’t be detectable and will deliver value to the ad tech.. against privacy.  The DoS attack is much less visible that it is happening compared to the privacy attacks.   Want to suggest we consider which is more likely in how we are designing this.

Paul: Most retailers will use extra domains, which will look like an SSO flow.  That will happen.  Guarantee it.  Ad techs will tell retailers to do that.

Charlie: It’s not the worst case, but it does increase privacy loss.

Roxana: We just make some recommendations.  You can move some up and others down.  But giving a bit more means more exposure to attack.

Next session we’ll talk about limitations of per-site limits.  That might increase the need to have stronger limitations on these to protect the per-site semantics.

Q Isaac: The impression site quota: is this saying that there is a limit to the number of impressions.

A: There would be a limit to the privacy loss from impressions registered to a particular site. A site can register millions of impressions and only a handful of those result in conversions. That’s OK.  No limit to how the API is used, except for when there is privacy loss (attribution).  You need both conversion and impression.

Q: Charlie: to what extent can we consider a simpler proxy that could get at the same result here? Like if we had user activation, 5 seconds since user clicked for example. Could this empirically perform similarly?

A: that is a specific prevention for a specific attack, but there may be other less bursty attacks that don’t get prevented there.  Suggest still doing these heuristics as defense in depth but is  insufficient against all attacks. Core principle in OS is minimal mechanisms – it is good to have at the core just a few mechanisms; you can layer multiple heuristics on top but not at the core.

Q: Charlie – taking the straw man heuristic i suggested, where does it break? Register one impression and it can trigger many queries and spend. But in this quota system we limit how much it can spend in the future

Q: Erik (can’t hear):

A:

Martin: (can’t hear): Think of this like a department store: rather than dealing with a single entity,  you are dealing with N discrete entities (menswear, perfume, etc…):

Erik: attack where each conversion happens on a separate site; impressions are on average on the web shown across many sites through DSPs

Charlie: even if you take away these quotas there still is incentive to shared your conversion site to get more budget

Martin: a large site, like facebook, will want to have a need for a large parameter for the imp quota; that number will need to be larger to support the large sites.

Roxana: maybe it can be negotiated in advance under other guarantees

Brian: how to make it work for both large and small sites?

A: quotas are going to impact the large sites, that is why we had the batched mode that could support those better

Martin: This affects all sites equally.  Larger per-site limits do imply larger global budgets, which is why we want to keep them in check.

Roxana: quotas disadvantage larger sites, especially without batching.

Charlie: (can’t hear): what about impressions that are not click driven, notion of user action may be different, like an audio ad in spotify, if this is different than what we choose it will be those impacted

Roxana: this doesn’t limit the number of impressions, just the stock that might later turn into privacy budget expenditure

Charlie: when I visit a site, that is what creates some amount of stock; it depends on what creates that stock, if a click does that, that might change

Roxana: my view is that visiting a site is a “user action”.

Erik: We only rule out things like redirects.

Roxana: I visit [news.ex](news.ex), that is an explicit action.  The counter is set to 1.  From here, the site can register impressions (500 or 1, it doesn’t matter).  Within a couple of domains then you get to read the 500 impressions in a query.  You want to ensure that you don’t limit things until you draw on the flow part of the stock-and-flow setup.

Charlie: A user might only ever scroll on a site.  That might happen over avery long period of time

Kleber: We can recognize that lots of use of a site, even without clicking, as a user action and can create stock.

Roxana: you have to tie these to something that cannot be automated by an attacker, something that the user does (clicks, navigation), but this shows that there are other types of user interaction that might need to be defined in the context of the API.  user spending time,



?: Selenium





Roxana: That’s OK, because using selenium would be a user deliberately spending privacy budget.  That’s OK.

Aram: we have a large group of users who interact with our site by sitting on the home page for days, with LOTS of ad impressions.  The user would occasionally refresh.  Other sites automatically refresh, ours doesn’t.  MSN home page refreshes without navigation.

Roxana: we need to tie release to something that is scarce.

Matt: Browsers have been agnostic to advertising. There is no inherent signal that says that something is an impression or a conversion.  We need to build that in.

Roxana: we don’t need that.  The point is to tie release of things into the system that is not trivial automatable. No need to have browsers aware of impressions/conversions.

Charlie: There are a few other pieces of prior art in Chrome.  Autoplay policy.  Chrome has a media engagement index that measures trust/engagement with a site.  Maybe if the score increases, that might be the sort of action that we consider to release stock/flow.  Navigation is one way this happens.  I don’t know what I’m arguing for here.

Roxana: The question here is how many instances of privacy loss can be hit.

Aram: some analytics tools have a concept of a “session” which they define with a time limit (say 30 minutes) and they use some actions to extend that “session”.

Longer discussion about how to create stock that I missed because my battery died.

Resolution seems to be that the number of fresh quota that can be taken from a single site visit is 1 (not higher).  This is because the number is intended to reflect the number of redirects that might happen for a visit AND involve the registration of impressions.

Requiring some sort of engagement with your site is necessary to enable the quota count at all.

Some concern about the amount of stock that a single site might need to drive toward advertisers, especially for sites that do a lot of ad placements across many advertisers.


### Per-site budgeting (ad-tech queries, etc…) ([agenda issue](https://github.com/w3c/patwg/issues/49)) ([slides](https://docs.google.com/presentation/d/1BtWj89kHunJIEMPEC6xVYYsfkFeA_AJ5kdEZy4Um2m0/edit?usp=sharing))

Scribe: Charlie Harrison



* Needs semantic clarifications in PPA
* Cross-report / privacy budget optimizations / limitations
* All detail is in the paper
* Per-site budget is critical to PPA architecture – can be tightly configured, whereas global budgets will be looser (higher)
* *Validity* of per-site guarantee depends on some assumptions, e.g. how conversions are used
    * Key gap is that PPA lacks both explicit statements of assumptions + mechs to enforce them
* Semantic challenges of per-site
    * Global budget: privacy loss from DP visible to *any site*
    * Per-site budget **aims** to bound loss from DP results visible to a specific site
* In practice, PPA does not enforce strict siloing of conversion reports / DP results
    * Encrypted reports / DP results may leak across sites
    * Interpretation becomes murky
* Example of imperfect siloing
    * Conversion on [shoes.ex](shoes.ex) → PPA deducts from [shoes.ex](shoes.ex)
    * But it is [adtech.ex](adtech.ex) that processes / generates the report, intermediary on behalf of [shoes.ex](shoes.ex)
    * Nothing prevents [adtech.ex](adtech.ex) from taking some of [shoes.ex](shoes.ex) / other sites and running their own measurements / other queries (e.g. optimization)
    * **Enhancing outcomes** for a different advertiser
    * This causes semantics to break down, proofs break down
    * This only holds if sites are siloed
* Proposal
    * Strengthening per-site budgeting with an explicit `beneficiarySite` role
    * Define it as the site whose per-site budget is charged for a report
    * Enforce batch-level consistency – no mixing
    * Terms-of-use clause: DP results must not be shared across beneficiary sites to encourage siloing
* Support x-report opt for intermediary queries (intermediary issues query but beneficiary is the conversion site)
    * Can reduce incentives to violate siloing
* Erik: Can still prove the global bounds right?
* Roxana: Yes
* Erik: Worst case is the intermediary budget blows up by the # of top level sites?
* Roxana: Even without intermediary queries, yes it only requires intermediaries
    * Example: User has $50. Because Nike has an effective ad, it gets me to spend $50. When I see Adidas, I won’t spend. Adidas might have gotten that if Nike’s ad wasn’t so effective.
* Kleber: I’m not yet convinced this is a problem we need to solve. Certainly we need to ensure a cap on the user loss of privacy. Global budget. Per site limits are convenient in that they prevent one party from excessively draining budget
* Roxana: if you just view per-site as quotas, no problems. That is not my understanding. The reason for that is that we want tighter privacy. Global budget cap may be higher / less meaningful
* Typical DP works do not address this, do not address cross-interactions
* Kleber: I actually don’t think it’s a problem we need to solve
    * We still have the global guarantee from user POV
    * If you imagine pessimistically a world in which all the ad-techs in the world decide to cooperate + pool information, the global cap is the only one that matters. The thing these per-site caps get us some provable way in which even without the deliberate cooperation might learn more than we thought they might be able to. From a user POV, its not clear to me that the user cares about the privacy loss being per-site or multiple parties
* Ben Case: Great feedback and I agree w/ you. Beneficiary just gives this more clarity. Doesn’t say this assumption holds unconditionally
* Roxana: Also agree w/ Michael. We should make these things a bit clearer.
* Aram: In what situation would we want a beneficiary that’s not the conversion site
* Ben Case: Going beyond level one. This is how we could support an ad-tech query
* Charlie: But then there should be a party that is allowing its budget to be deducted — the top-level party or the request origin party or something
* Roxana: this ties to level-2 intermediary queries, which we will get to in the future.  In that case the beneficiary is clearly specified as the intermediary site
* Aram: Beneficiary in this situation *is *the intermediary
* Roxana: For level 1 we can just think of beneficiary as conversion site
* Paul: The example you gave: not only was this possible it was desirable. Grouping multiple advertisers. Economic motivation to make sense of the ecosystem.
* Roxana: if you want to do per-site accounting, then the right way to do this is to support intermediary queries.
    * It should be accounted for
* Charlie: Consider from the POV of what different scenarios are DP-adjacent to each other.  Take all the events that a user did for one advertiser.  Take them all away, and that's an adjacent data set that should be indistinguishable from the DP POV.  Now just imagining a world where one advertiser's events are removed does not really give you a neighboring dataset, because of Roxana's example of removing the Nike events affecting Adidas effects.
* Aram: Depends on which agent you are.  Some parties might be happy with cross-buyer benefit, some might not.  Buyer is making choices with that in mind — small advertisers choose to buy with FB exactly because they want to benefit from this kind of cross-buyer information benefit. Large buyers often choose to buy direct to avoid this data leakage, or choose particular strategies or configurations when they do buy on something like FB.
* Charlie: This comes up even without deliberate sharing.  The impact can be felt at bidding time.
* Erik: Not the browser’s position to make a judgement about business relationships of sites.
* Alonso: Not correct to even equate a site with a business entity. Lots of sites that do not represent a company
* Martin: They have a 3rd party.
* Roxana: Per-site, only accounting against 1 advertiser, but privacy loss spans across. Can’t have the cake and eat it too. Ad tech 1 reuses results. Right way is to ask for 2 results. Either always deduct from both - but clearer to support two types of reports.
* Brian: Having a hard time understanding a use case where an advertiser would want measurement results to also improve other ads.
* Aram: Kind of how it works now. There’s also a 4th party: ad agency. May incentivize them to buy measurement companies. Contract that prevents them is with the advertiser.
* Michael: Not a user privacy issue to solve here. Problem, if any, is the relation between different entities in the ecosystem. It’s reasonable for us as browsers to help build tools to help manage that. Allowing an advertiser to specify that they don’t want their results mixed is plausable. Taking the place of things managed by contracts.
* Charlie: Disagree there’s no privacy issue. We shouldn’t give up on per-site. Results in any meaningful privacy guarantee. Global is to high, even at 8. Likely could be higher. Value on trying to bound a tighter unit is something tangible we could provide to the user.
* Roxana: Agree with Charlie. All this is built from certain assumptions.
* Charlie: Not sure we need to go this far. Once you start allowing a random 3rd party to be a beneficiary, it makes the previous talk more complicated?
* Roxana: No.
* Charlie: We were pretty well aligned on one quota.
* Roxana: Intermediaries don’t appear in the quota system. They are third party, not meaningful to users.
* Brian: Limit to beneficiaries?
* Roxana: No only to top level domains users act against.
* Brian: How many can I include before I’m not getting any information?
* Roxana: Optimizations to not multi-count against the system.
* Brian: Doesn’t the amount of information per beneficiary decrease as you have more beneficiaries?
* Thomas: Open question, to list use cases and how they fit in.
* Brian: Consider a system that works for a significant subset, maybe not all use cases.
* Roxana: Per-site and global should go as planned. Should do things to move towards the assumptions. The cross site things should be accounted for, needs to pay it.
* Martin: What you haven’t addressed as why someone would us this? If I could not specify, I’d be better off not specifying.
* Charlie: You could get half the budget, but maybe 5x less noise.
* Kleber: Noise goes down in the square root. Aggregating over 100 advertisers, go down for a factor of 10 is great.
* Paul: Also getting a purchase stream, already enriching it.
* Martin: Can probably make a case because the total noise is lower, but not sure you can get that since they are getting diverse information and histograms don’t line up.
* Erik: Should we make abstract assumptions, and try to make them true, or make our assumptions match reality?
* Charlie: Somewhere in the middle. It would be good to have a strong statement if someone who only interacted with two sites.
* Aram: Knowing all the entities that are accessing my data is highly valuable. Someone like Propublica would want to be able to allow you to install an extension and show how it’s learning.
* Charlie: I think it’s fine not to support a dashboard as long as we can say something that’s almost accurate.
* Roxana: I’d much rather keep things as they are, but say what they are. Users will just get an interpretation, but also important for an expert to be able to go an analyze.
* Aram: (In Slack): I don’t like the name “beneficiary site.” Let’s discuss before we put this into anything permanently, but not now.
* Charlie: Want to check where we netted out. Not including a “beneficiary site” is cleaner, even if the assumptions are weaker.
* Paul: They are just going to pick the best option for the scenario.
* Matt: That’s just a bad API.
* Michael: The browser does not have any ToS / contract with websites. Browser puts a lot of limited in place in standards/heuristics. The idea of the browser would have some control of data once it’s left their hands, that’s entirely on the policy site. What we did in Chrome: in order to be allowed to use some of the Privacy Sandbox APIs (e.g., ARA) you are required to make a public declaration about how you’d use the data, and promise to the world as part of my privacy policy that I won’t try to use this data to track people across sites. This was not the browser enforcing a terms of use, the only enforcement was if they have a public privacy policy that say’s I will only do X and not Y, but they lied and did Y, then maybe some regulator would come along and punish you for that. That’s the only mechanism we had for enforcing. I would not recommend this, I don’t think it was a good idea. It was only meant to be a temporary mechanism. For this kind of purpose of a permenant long term way we want the web to work, I don’t see how a ToS has teeth to actually be enforced.
* Brian: Agree
* Martin: Couldn’t the aggregation service manage some of this?
* Kleber: Yes, could also have two budgets.
* Aram: Trying to get our attestation through legal was terrible. We don’t want any browser to dictate our ToS
* Roxana: Yes, we want to enforce batch-level consistency.
* Martin: Question if beneficiary or conversion site.
* Erik: The other option is assume the conversion site is going to share results b/c we’re not running two queries.
* Roxana: yes, you’ve got separate noise
* Brian: Assuming I’d want to get the most value, and share it with whom ever they can get value from it.
* Aram: Nothing stopping the intermediary from lying about if the query is for the intermediary or the advertiser site right?
* Charlie: In theory the intermediary level query is more powerful.
* Paul: The intermediary is incentivised to make the intermediary query then?
* Ben: Brian’s question: are we giving up the conversion budget for intermediaries? There will be more budget, you won’t be trading off between yourself and an intermediaries. Ad-tech doesn’t just want to re-aggregate, they want to use different breakdowns. Does it support the right type of query?
* Aram: In the world that this process envisions, there’s a lot less incentive to use multiple measurement providers in the way it’s standard today. For your conversion measurement, you’d have one intermediary. You might measure other things with other partners.
*
* Martin: It’s not clear to me where the intermediary gets their budget from. If they’re spending the budget from the top level site then the question Brian asked - why would I give any of my budget away? As an advertiser I’m saying they can make do with much less than what they want beyond what I am getting. If the intermediary gets their own budget across websites - then they can do what Ben was talking about where they can work across properties. Where does that budget comes from?
* Roxanna: That is it.
* Martin: We just give it to them? But then is there additional privacy loss?
* Charlie: Your privacy loss is the global privacy loss is what we’re saying I think.
* Roxanna: Then the global budget doesn’t care.
* Charlie: The way we handle these other queries is continue to have a tight per site accounting if the user hits only two sites. The intermediary sites as separate budgets makes less sense to me now. Maybe we keep going with what we have and the assumption on the per-site budgeting claim is weaker.
* Roxanna: If you only give out reports tied to the advertiser
* Charlie: Allow the intermediary to mix and match reports maybe. It makes the per-site claim cheaper.
* Charlie: We could allow heterogeneity
* Martin: throw everything in the same batch?
* Charlie: yeah. Even if by adding this complexity we still can’t make the per site work. We can still make a per site claim if the user only visits two sites or something like that.
* Brian: If I’m only doing conversion reporting for the one site then I can get the maximally rich budget and roll things up in various people and ways?
* Paul: Omnicon as an example might look to make an examination to build a model that is cross advertiser.
* Brian: whoever generates the detailed as possible report than generates the reports downstream of that.
* The API should be clear about what it is accomplishing.
* Martin: throw more sites in get more noise?
    * In Charlie’s model there’s 10 sites in a histogram but only one unit of noise.
    * If you account for it?
* Charlie: That’s fine, but the adaptivity is difficult.
* Paul: I think it is fine to pull the reports after the fact and then the aggregation company can pull them together how they want.
* Charlie: If people are going to be aggregating after the fact post query then aren’t we just closing our eyes?
* Roxanna: Making clear the assumptions in the UI.
* Paul: The ad tech firms are already doing enrichments.
* Brian: Easiest path and highest quality signal available.
* Roxanna: Isn’t the cleanest thing to do to declare this in the browser?
* Aram: If I can say that an aggregator is only feeding information to certain entities, but in practice, whenI use this, I will say that we can use this process even when a user has opted-out.  That is the position that some browsers are assuming as well.  The question about mixing data on behalf of the aggregator, is something that is forbidden, and people are opting out of that.  If we did allow an aggregator to mix the data from the design of the API, but if the API is designed in that way, my ability to run this API for an opted-out user goes to zero. Because if the API allows mixing of data, there are opt-out rules against that.  The publisher has no control over that, even if the aggregator does this.
* Brian: the 1st party has the relationship with the user, which deprives user of agency,
* Aram: We might try to blame the aggregator, but we will be at fault as a publisher.  Here, without the API we can require the aggregator not to do cross-advertiser internal to their own system.
* Charlie: this is just an argument for having a knob.  We could add that knob.
* Brian: who is going to set the knob?
* Aram: If the knob exists, we’d set it to off. If we could have a knob, we’d want one.
* Charlie: we’re setting priorities between two use cases.  These are in tension.  I’,m suggesting that we just support all options.
* Paul: I don’t see a use case for aggregator queries, at least within the system.
* Charlie: I’m fine with that.
* Aram: just speaking as a potential adopter
* Kleber: the simplest possible answer is: having reports that have only a single conversion site takes care of most problems.  You have homogeneity etc…  You only miss is some hypothetical use case for combination BEFORE aggregation.
* Ben: No hypothetical.  We documented it.  Take for example, logistic regression that Criteo implemented.  This is done for optimization.  Level 2 stuff.  For that you need to aggregate across all advertisers.
* Kleber: How much worse is it?
* Ben: There is going to be no signal unless you can combine the signal.
* Brian: Is it meaningful to concentrate on reporting first.
* Ben: Of course, but we want the level 2 stuff.  We can put the large scale optimization on hold for now.  Event-level reports can be used for training, but there isn’t much appetite from that.
* Charlie: might look at learning from labeled proportions.  Maybe we can aggregate then. There might be something that isn’t fully event-level.
* Brian: Base level might let people find what they need.
* Kleber: When we looked at learning from aggregates.  Data -> learn stuff.  Also, here is a partly trained model, please tune that model based on the additional aggregate data.  These were the two operations.  In the version where it is OK to combine aggregates post hoc, you would then iteratively train the model based on new information from each advertiser in turn.  I’m willing to believe you if you were to claim that is much worse than a single batch, but it’s not obvious that it is totally unviable.
* Ben: Might need to compare approaches again.  My takeaway was that you could do pretty good with a single low epsilon for a single batch.
* Charlie: This doesn’t feel like changing our mind is going to be an earth shattering different stance.  My preference would be to stay conservative for now.  We can talk about model training and revisit this decision when we have more information.  Maybe we decide that we need it and we need justification, or new privacy limits.  Especially if model training is the main use case, that’s OK.
* Brian: Explainability of the simpler model means that we can use that as a foundation to build on.
* Aram: What do we want from level 1 here? Do we want a property for some future process or not?  We previously discussed the idea of some delegation.  The idea here is instead of waiting, we create that generic property that helps at level 1, but then hope that it works at level 2.
* Brian: preference not to complicate level 1, unless we understand this better
* Charlie: don’t want to be over-specific in the name.
* Martin: It’s currently “site”
* Ben: needed for replay, call it “replay granularity”
* Aram: the answer seems to be “no”, we’ll have one property, but it will be more generic.
* Alonso: what if the site has more than one client?
* Charlie: for level 1 we are ignoring all of that; beneficiary site == conversion site.
* Matt: The privacy loss needs to take into account the combination of data across sites


### PPA Discussion Topics


#### "Discuss" Issues


##### Intermediaries using the Level 1 API

[scribe: kleber]

Martin on [https://github.com/w3c/patwg/issues/44](https://github.com/w3c/patwg/issues/44):

slide deck at [https://docs.google.com/presentation/d/1dvuWG8zGj_95Mu2yEWhmAVhnvIqmDZ0-7B51KiU1ePE/edit?usp=sharing](https://docs.google.com/presentation/d/1dvuWG8zGj_95Mu2yEWhmAVhnvIqmDZ0-7B51KiU1ePE/edit?usp=sharing)



* In our over-simplified model, one site registers impressions, another one registers conversions
* In the real world, there are intermediaries on one or both of the impression site and the conversion site.  Maybe multiple intermediaries on either side, may or may not match
* Spoiler alert: there is no good solution
* "Permissions policy" controls access to the API, but this isn't a useful limitation: From the browser's POV, a 3rd-party script that is running in the context of the top-level site has all access to that site's capabilities.  Result: The intermediary spends budget on behalf of the conversion site.
    * saveImpression(): we can save the intermediary, not much point in doing anything more than that.
    * measureConversion(): Might be better with some added controls, but it gets messy very quickly
        * Option 1: An intermediary can query over impressions saved by that same intermediary, but can opt into a wider scope of visibility?
            * Or own impressions by default, but intermediary can also pass a flag to get impressions from everyone?  That could only work if somehow histogram bucket numbers are coordinated across intermediaries; that seems extremely difficult.  Or broaden only to top-level-site, but not to other intermediaries?
        * Option 2: An intermediary can only query over impressions saved by that same intermediary?
        * Option 3: Coordinate with specific set of other intermediaries, with whom you've agreed to work and agreed on bucket naming mechanism.  But this seems like it is going to require global coordination anyway, because of transitivity (intermediary A coordinates with B,C,D, but also C coordinates with D,E,F, etc.)  And what about impressions by top-level origin?

Questions (Alonso, Fabian): Are these DSPs or SSPs that you're coordinating with?

Martin: Could be either

Erik: Could be huge list, could be outsourced — can you even know it?

Aram: There wouldn't be a long list of impression providers, unless they are all the impression sites

Martin: Might work for direct sales, but not reasonable for many-site situations

Brian: So how can I trust the data coming from these calls?

Martin: Need to trust the intermediaries.  Or else you make your arrangement to have your own (advertiser) code present inside your ad each time, and then just trust yourself.

Brian: We should just give this intermediary capability to buyers.  DSPs are the ones that ought to do this job.  This needs to be somebody I trust, so someone I'm already giving money to anyway.

Alonso: There will be multiple buy-side companies that want to play this role

Fabian: These are just like tags in creatives

Martin: Ad campaign running through multiple intermediaries means need to measure across them



        * Option 4: At the time that you save an impression, you bequeath it to someone else = declare that you are giving it to someone else to own.  Complexity at impression time, not conversion time.  Still requires coordination.

Charlie: This could be some kind of consortium.

Martin: Do you need coordination controls on both sides?  Or OK to have it just at query time?

Charlie: I think you need explicit opting in on both the impression and conversion sides.  Need to protect against someone else giving you too many impressions by accident

Erik: need to worry about stealing credit — control over who can measure you

Martin: This might be orthogonal to questions on the querying side.



* Martin: Sounds like the right answer here is Option 3, list intermediaries at conversion time, just yourself by default but can expand the list if you want.
* Brian: How can you deal with it if the list gets really long?
* Alonso: We have data about how many we need to worry about — upper limit of 10 seems like it's more than enough
* Brian: Are there problems managing resources if you need to save this for every call?
* Martin: Probably not, statically configured.
* Brian: You need to remember stuff, though, right?  You can't dispatch the decision right away, if you're saving it at impression time and evaluating at conversion time.
* Miguel: Managing it at impression time seems logistically easier for the way advertisers usually do things.  Advertisers are used to doing things on the tag itself.
* Martin: But we've heard that it's necessary to retain this control on the conversion side, even if it seems like more work.
* Isaac: If I'm a DSP, surely I would want to control who can measure my impressions.  Might not be used, but we definitely should provide it
* Martin: I don't see any privacy consequences to our allowing that additional control on both sides, so we-the-browser should make it possible.
* Erik: If you have a sophisticated impression site as the first party, do they just act as their own intermediary?  So at conversion time, you specify social.example as an intermediary site?  Seems like you need more control than just "all top-level sites are the same"
* Martin: Conversion measurement being run by top-level is surely legit.  They can list themselves as intermediary in all of their own ad impressions.  Impressions being registered by a top-level publisher site, measuring conversions on behalf of the advertiser?
* [many voices:] that definitely exists also, google facebook etc
* Isaac: Intermediary can be too limiting a name, someone might be first party to the transaction even if they're a 3p at some point
* Roxana: "ImpressionRegistrator"
* Charlie: Need the API to make sense when a first party is doing their own job, on either side; don't want to tell all these big top-level sites that they are now intermediaries instead.
* Erik: We should move on from bikeshedding on the name
* Miguel: Semantics sound good to me, but should make it possible to use with only making changes on ad creative side; changing advertiser site is making updates to CMS, that's a harder operation.
* Isaac: What if you want to specify yourself?  Need to make it explicit?
    * Martin: Surely you know your own name.
    * Charlie: You could just say `self`?
    * Martin: No, that's a bad idea.  Need to say the domain explcitiyl
* Charlie: Should probably be able to say * (= everyone) on the impression side, but not on the conversion side.
    * Martin: We are requiring that they list the sites that the impression is going to convert on.  So * means we just allow the conversion page site to make its own decisions.
    * But we have * as the conversion site, at saveImpression time.
    * kleber: We should probably require *either* specifying the conversion site *or* a matching value in the matchFilter, i.e. a matching campaign ID.  Can't allow global ads that match everywhere.
    * Erik: How about allowing * for intermediary?  Seems like there's a contract here
    * Martin: on conversion site, you surely don't want to list all impression sites, that could be the entire web.
    * For "car dealership" example, at impression time you would surely know a small list of intermediaries that act as conversion providers, even if the conversions themselves might happen at any Ford dealer in the country.


### Day 2

Advanced use cases (like cross-DSP attribution): Put this off until tomorrow


#### Advanced Delegation (pt 2)

[Martin presenting] Slides: [https://docs.google.com/presentation/d/1dvuWG8zGj_95Mu2yEWhmAVhnvIqmDZ0-7B51KiU1ePE/edit?usp=sharing](https://docs.google.com/presentation/d/1dvuWG8zGj_95Mu2yEWhmAVhnvIqmDZ0-7B51KiU1ePE/edit?usp=sharing)

Martin (Slide 11): summary from end of day 1: intermediary may use the API to record impressions and delegate this recording to partners listed under intermediarySites. We might want to tie this list to the top level origin and we might want to stop the accidental capture of the impression. We may need to consider more limiting factors like campaign IDs, etc.

Martin (slide 15) cross-dsp attribution: to correctly bucketize the impressions and attribution coordination is needed. So there are 2 choices: each DSP measures independently or they coordinate.

Brian: so more noise on top of noise? It seems to me there’s not too many things to coordinate?

Martin: the semantics of the histogram need to be agreed upon [i.e. bucket 0 means “x”, bucket n means “y”]

Alex: I have a drawing: a common use case is that there is one measurer across DSPs - but the DSPs can only see their own (permissioned) impressions or all? (asking for clarification)

Martin: in this case the expectation is that the measurement partner and the DSPs would write to the same histogram but the semantics of “which impressions are attributed to which DSP” would be inside the histogram.

Alex: is possible that the DSP doesn’t know who the advertiser’s measurement vendor is (actually, likely). This coordination and formal acknowledgment doesn’t happen

Alonso: yes the way these tags are specified within the creatives in the DSP UIs is low-tech

Erik: The pigeonhole principle will say that you will eventually collide with the noise that with a limited number of buckets, eventually buckets will collide.

Brian: they may not want to partition too much, it will have lots of noise

Erik: It will be harder to lift later.

Aram: There is an illusion that the lift is easier now.

Martin (slide 16): [presents slide 16]

Alex: this is what is new Brian, DSPs just don’t coordinate like that

Tomas: as a DSP I want to know when I’m attributed the conversion to train my models; for that we need all this complexity so because of that I don’t see this coordination happening.

Aram: as DSP A I don’t want to lose the opportunity to claim the conversion over DSP B

Martin: let’s remember that we are operating under a shared privacy budget regime

Alex: we didn’t know what the measurement partner was recording, but in that case we wouldn’t see any discrepancies, if you coordinate well

Charlie: if this graphic had measurement partner C instead of DSP B in the conversion site, DSPs A and B get NO data. To have no discrepancy only way is to throw away their own data

Alex: but even in that scenario, the DSP needs the data not for ML Training reasons but for human consuption reasons, so the coordination does happen and it can be done well

Erik: the incentives given the privacy properties do push towards consolidation which doesn’t exist today

Brian: Also this affects smaller campaigns

Erik: this changes the dynamics of who does what

Martin: I’m just talking about the coordination problem now, trying to find some lightweight tools that make this feasible

Charlie: slide 18 proposal doesn’t solve the ML training use case. Want to make sure we are okay and on the same page with only focusing on the human consumption use case for now.

Martin (slide 23) the conversion site (or authorized delegate) performs attribution across all impressions

Brian: does each DSP get their bucket?

Erik: it’s more like several histograms are created not like one histogram is partitioned per DSP right?

Martin: it’s logically the same

Charlie: this loses some of the benefits from the prior approach

Brian: it seems to me this benefit last touch attributions more?

Martin: no the attribution model is independent

Charlie: Like today, that’s what the advertiser wants, the API doesn’t decide that

Brian: yes, I am complaining about last-touch attribution, seems this exacerbate whem this happens

Alex: you are right Brian it will be more visible to the DSP when they are losing b/c of last touch attribution but that’s not different today for the measurement partner

Michael: I think we are in a good spot here, this make the attribution algo choice independent and allows for the change

Erik: what is the difference between this and the measurement provider having a large historgram?

Martin: in one way none, but how the histogram allocation happens needs the coordination

Erik: could we figure out how to let the DSP decide at the last minute?

Martin: we think it’s doable

Charlie: this does add more signal, but keeps the noise constant. So we indeed reduce the signal by 3x instead of increasing the noise by 3x.

Brian: As you add more DSPs to more buckets than you end up with noise from each DSP

Martin: The tradeoff is if you are looking for reports and you have a low vol campaign with poor conversion rates you don’t want to throw a lot of buckets at it b/c you’ll get washed by the noise.

Erik: we can support both things : we can coordinate the bucket assignment and the impression recording for level 1 right?

Martin: yes and that’s what I propose: I want to record my stuff and get a report of what was attributed to me. So I propose we add this part of the spec to level 1

Arpana: wouldn’t the DSPs need different attribution logic?

Martin: I heard from [ ] this may be necessary. It really depends on what the advertiser wants. But if the DSP has different logic for ML training reasons we cannot support that

Alex: why would the DSP want to optimize for something the advertiser doesn’t want?

Paul: If you want to solve a multiobjective optimization problem when there are different goals per advertiser this is needed, goals that benefit the DSP.

Paul: There is no Nash equilibrium there is no optimization that is both local and global. This also can flow into things like pacing

Charlie: to support this we’d need different privacy budgets

Michael: It is very reasonable for each provider to allocate a different budget to each goal

Charlie: but now the advertiser centric model conflicts with the goals the DSPs have

Paul: people will figure out how to optimize the model

Aram: I prefer the idea that the budget logic is allocated to the entity who benefits from and is paying for the reporting; just not sure if we are able to fit a design that meets this criteria within level 1

Martin (slide 26): API shapes proposals

Erik: in the second case, does the histogram go back to intermediaries?

Martin: both models do the exact same thing,

Erik: is there a simpler version? Where coordination is not needed as much

Charlie: coordination will be needed anyways

Erik: is really just a question of do you want to mash the histograms together vs create them separately

Martin: I think we can do that, the first model gives more flexibility

Charlie: in this model we are delegating to a DSP and it’s doing conversion measurement.

Martin: the attribution logic will be independent and not happen here

Charlie: is this model the DSP chooses it’s attribution model

Erik: the network tax concerns me. I’m concerned we are making a call from the conversion site it calls the DSP

Charlie: but that happens today already

Brian: does this increase noise?

Erik: like discussed earlier noise-to-signal ratio increases

Aram: the idea of the DSP delegating to the measurement partner is believable, it mirrors what is already happening here in the EU due to GDPR. The idea of unlimited post messages from the Handle Model makes me nervous for my web vitals

Erik: does the parameters and config need to be pre-declared?

Brian: seems to me we are adding a routing layer.

Martin: the amount of information the DSP is adding to the process is not too much

Erik: and it doesn’t need to be private, just bound to the report

Martin: we can find several ways to do this, just not a well known file that’s a terrible idea.

Charlie: seems to be that the browser could be the one to do the fetching. We need to coordinate, and this model allows us to coordinate later.

Erik: there is a benefit to the second model, because of you make a mistake you only need to dump the data once and rerun later.

Aram: I’ll also add that a version of this where we don’t need to add more iframes to the page is desirable.

Charlie: If each DSP pre coordinated with the measurement provider we would not need to do any of this.

Martin: I think everyone understand the proposal that we have here, need to solve some designs but we are at the cusp of having figure out this API.

Alex: the same people will be in yachts in Cannes


#### "Discuss" Issues

[https://github.com/w3c/ppa/issues?q=is%3Aissue%20state%3Aopen%20label%3Adiscuss](https://github.com/w3c/ppa/issues?q=is%3Aissue%20state%3Aopen%20label%3Adiscuss)


##### HTTP API, [https://github.com/w3c/ppa/issues/31](https://github.com/w3c/ppa/issues/31), [https://github.com/w3c/ppa/issues/47](https://github.com/w3c/ppa/issues/47)

Martin: We have a header field, if it comes back in a response then same behavior as if we invoke the JS API with all the options in the header.

Delegation is trickier, that’s the topic of #47 – it requires a return value. General pattern here: include a URL in the relevant response header, and then the conversion report gets POSTed to that endpoint.  That’s a straightforward deliverable.



* Charlie: As long as attribution logic is an enum like `logic: "last-touch"`, everything is fine, but if we want more expressive/powerful querying or matching logic (even arbitrary code), is it OK for an `&lt;img>` tag to have a response header that causes arbitrary script execution (even if in a worklet)?
* Martin: very good point, maybe the HTTP API will have only a limited subset of the capabilities of the script-on-page API.
* Paul: For some companies, attribution model needs 3p auditing.  Pushing attrib login to browser makes it easier for those companies, no worry about unpredictable logic, and an enum-based mechanism is a form of security
* Alex: Ad tech industry might not be so happy about browser dictating attribution model
* Brian: As long as the algorithms available are part of what everyone in the industry can opine on and contribute to, that should be fine
* Alex: There is risk of us being accused of bad behavior though.
* [many voices] Allowing arbitrary algorithms is going to be a can of worms
* Charlie: arbitrary code running on the advertiser’s page will make a lot of people unhappy
* Martin: Level 1 will definitely just be an enum.  Right now it’s an enum with only a single value, last-touch, and we have not yet had anyone propose a second attribution logic to this group.
* [multiple voices] multi-touch is going to be important too
* Aram: The browser already determines attribution logic, cookie availability and the APIs measurement currently interacts with is already subject to browser rules
* Alex: Even if you agree with that POV (which some do but many do not), it’s easy to attack a “browsers write the attrib code” story
* Isaac: We all agree that “last-touch” on its own is not enough, but we don’t need to go full-JS
* Brian: Ask IAB Tech Lab to say what other models are required?
* Eric: Right now we don’t know how to do arbitrary-JS in the first place, that would take real work
* Charlie: We can just add additional ones easily, if there are only a few, and could even do that in Level 1
* Paul: Multi-touch maybe should even be in level 1
* Martin: It’s always going to be an enum of strings, but the string might be parameterized with a JS URL
* Charlie: We could build a domain-specific language (DSL) for this – indeed Chrome did build one and we considered an even more complicated one.  There are lots of downsides to needing to design this, but we could write something that surely terminates and is safe and we could even be comfortable putting it into an HTTP header API.
* Brian: Seems good enough, as long as browser is able to expand things if industry says it’s needed.  Arbitrary JS should be off the table.
* Martin: No DSL for Level 1 (we don’t need or want to do that work now), but this is enough for us to know it might well be a direction that we head in the future.
* Eric: Maybe SQLite?
* Martin: We have a database.  Need two portions of DSL: how to do selection or filtering from the database of impressions, and then how to assign value to each.
* Charlie: We could surely deliver some SQL-type thing.  Would that be sufficient?
* Isaac: arbitrary SQL would go a long way, probably satisfy any naysayers
* Eric: There are attribution models that solve a differential equation, probably not plausible in SQL, or probably we would need to support only a limited subset (e.g. not recursive).
* Charlie: We don’t need constant-time.  But it’s super-hard to keep the runtime from leaking via a side-channel
* Martin: Options include fully-capable SQL (probably nearly as bad as arbitrary JS), limitations on SQL, worklets, another DSL with limited properties, etc.
* Brian: People will snoop on other people’s attribution logic and get grumpy about it
* Martin: Yes, this logic is going to be published and visible to everyone.  We can’t avoid that and we don’t intend to try.
* Martin: I would like to see more time on the low-end than the high-end.  Something like multi-touch, we can just implement in the browser, let’s add that.
* Brian: I think that might be enough to satisfy some people
* Alex: Not all people will be satisfied, for politically-motivated reasons even if it’s good enough for technical purposes.
* Aram: I don’t think we need to concern ourselves with things people say for PR reasons in stage presentations.  Make something that we think is good enough for now and be prepared to add more later
* Charlie: We should think about delivering value, not about optics.  We need to know how big the gap is between one-value enum, two-value enum, full DSL, etc.
* Paul: “Halo attribution” gets crazy
* Arpana: That’s a filtering question, not attribution logic; we could do that actually
* Isaac: Current world allows for innovation, and we don’t want to lock the world into only things that we’ve thought of right now.  Something more powerful (SQL or DSL etc) means it’s possible for people to innovate over time, and maybe something new will arise.
* Brian: We’ve had 20 years to innovate and it hasn’t given us much.
* Charlie: Last-touch might not mean the same thing to everyone, there are small nuances that might make a big difference to some people.
* Arpana: With ARA we designed a lot of flexibility to work around exactly that problem, but it’s very ad-hoc
* Martin: We want to know where we’re going to end up, want to be more deliberate than that
* Arpana: A lot of people want last touch; go with the simplest option now, then explore more options
* Isaac: Two meta-points.  (1) Some people are acting as if IAB Tech Lab represents all of ad tech.  Don’t make that mistake. (2) Yes things have not changed much in the past 20 years, but we are in the middle of fundamentally changing stuff, and that could well overcome inertia and spur innovation
* Aram: As long as we have a design that anticipates the ability of someone to come in and do that work, we’re OK.  We just need to decide whether we need something more than last-touch in Level 1.
* Charlie: “last-touch” is well specified.  Please read it and tell us if it’s wrong, and we will either change it, parameterize it, or add another value.
* Aram: Do we need last-touch by click-only vs by view also?
* Charlie: in ARA we allow impressions to have priorities, it’s possible to do this if needed.
* Sean: Practically, how do we get feedback?
* Aram: We make a call here, we put it in the spec, we present it to the CG, and we solicit feedback.  OK to send to IAB Tech Lab or other places.
* Eric: We should pull out a few key decisions or points where we’re soliciting feedback.
* Arpana: need to figure out how to break it out into issues and get attention
* Brian: I’m happy to advocate for Tech Lab to give feedback.  If your request is concise, that makes it easier
* Charlie: Current spec doesn’t have any concept of “click” vs “view”, it just has “impressions”.  You could use filtering to attribute to *only* clicks.  But we can’t today do something like “attribute to a click, or to a view of there is no click”.
* Brian: I think that’s what you’re going to get – I want a click if not too long ago, otherwise a view with certain limitations
* Charlie: I think it’s likely that what we have now in PPA is not good enough; I don’t know whether what we have in ARA with priorities is good enough.  How would you like to filter + how would you like to prioritize
* Arpana: Don’t need custom attribution; keep filtering and attributing separate.  Many people don’t want to attribute to a view if it’s more than a day old, for example, but that’s a filtering stage question
* Isaac: Maybe in level 1 acknowledge post-view vs post-click, and say that we’re evaluating things broader than that.
* Martin: Sounds like our goals are (1) post-view vs post-click; (2) multi-touch with some sort of proportionality; and (3) something more general that we want to understand directionally but won’t deliver in the short term.
* Charlie: Let’s be sure the scope of post-view vs post-click is well-understood and validated.  There’s an existing issue on making click-vs-view a first-class distinction, [https://github.com/w3c/ppa/issues/42](https://github.com/w3c/ppa/issues/42).  Right now you could use match values to get only clicks
* Isaac: As long as it’s possible to accomplish the thing, that’s OK
* Charlie: But logic like “click or fall back to view if no click” is not possible now, and that may be needed in an MVP
* Thomas: I don’t think anyone uses “all clicks or else a view if there isn’t one” is a use case that people really use.  All clicks are views.
* Alex: Do we really want ourselves to need to have this kind of conversation every time you want to add something?
* Brian: I do think “all clicks or else a view if there isn’t one” is a real thing, people want some signal.
* Isaac: Need to draw a line and say what we need to ship in level 1
* Arpana: We need to pick something that is extensible.  How much work is it for an implementer to prototype and change if we expand it later?
* Martin: Will two enum values cover 97%, or will it only be 30% of desired functionality?
* Alex: How long will it be between when we come out with Level 1 and when we expand to more functionality?  A year or more?  If so then we should try to do to the better thing now.
* Isaac: Can we just adopt the ARA method?
* Charlie: If all we’re doing is adding a priority attribute on impressions, that’s not bad.  If filter data needs to be a function of impression value – like lookback window that is variable based on time of impression –  then it’s a lot more trouble.  Alternative is that we hard-code two values: “Paul-attribution” and “Brian-attribution”, and be open to other things later.
* Martin: Someone volunteer to really do one additional algorithm, as a second enum option.
* Paul: What is your algorithm actually?  Something cascading, last-click-in-time-T1 and if not last-view-in-time-T2?
* Brian: I’ll need to think about it but I will write something up.  You probably want to reexamine how you expire things from the impression store
* Martin: If we had a new attribute that is click-vs-view at impression time, then it is easier to play with alternatives.
* Arpana, context:
    * Lookback windows: [https://support.appsflyer.com/hc/en-us/articles/208338403-Set-up-lookback-windows](https://support.appsflyer.com/hc/en-us/articles/208338403-Set-up-lookback-windows)
    * Attribution setting: [https://www.jonloomer.com/meta-ads-attribution-setting-a-complete-guide/](https://www.jonloomer.com/meta-ads-attribution-setting-a-complete-guide/)
* Martin: There is an existing issue for multi-touch, [https://github.com/w3c/ppa/issues/44](https://github.com/w3c/ppa/issues/44), and please add to the issue.  If we get three values in enum, we are in a very good spot.  There may be many MTA options, but we should pick one simple one to put a stake in the ground.
* Paul: Take the last N attribs, different lookback windows for click vs views, then divide credit across them with some simple fall-off function.  Picking one might be good enough.
* Charlie: If we asked 20 people and got 20 instead of 2 answers, then punt to the SQL thing.
* Isaac: Are we thinking of two levels of value?  Better filtering to a single attribution, and better crediting to multiple events?  [All: Yes]
* Martin: Need a stake in the ground, so that we can learn how close that is to what people want.
* Charlie: I’m concerned that multi-touch is not going to be close enough to what people want to satisfy anyone.
* Isaac: Need two stakes in the ground, one for filtering and one for credit-spreading, and we can evaluate them separately.
* Eric: How about counterfactual places where you didn’t show an ad?
* Can just save an impression, but nothing keeps it from being fraudulent
* Charlie: If we’re treating clicks and views differently, should we have another attribute for “opportunity that could have been a view”?
* Charlie: What is the problem with just doing priority?  Let’s look back at #47: Last-touch-by-priority, i.e. highest priority and then ties are broken by time.,  Doesn’t allow lookback to differ by priority
* Isaac: but you can set that at impression time
* Arpana: But different campaigns might choose lookbacks of different lengths, might not know at impression time or might not have only one lookback window desired
* Brian: priority is not enough, I want to treat them differently in ways other than just preferencing one
* Isaac: Expiration and priority is enough, if the caller specifies everything and only wants a single lookback window for each type of impression
* Arpana: But if you want to think of that as a property of the join rather than of the impression event, that doesn’t work.  If different types of conversions (eg pageview vs purchase) want different lookbacks for different events, then you need lookback specified at conversion time, not just at impression time
* Isaac: I think fixing at impression time gives a substantial fraction of the value
* Martin: You lose some resolution with just priority + window-at-impression-time, but maybe it gives you most of it.  New value on the event drop (priority), and new attribution logic that takes that into account, which Brian is going to give us.
* Charlie: We used “source” rather than “impression” because there are other types of events; there is a lot of bikeshedding needed here.
* “Ad event”?  “opportunity”? ← no that means something specific.  “Impression” ← no, nobody uses that for a click.  “Engagement” ← no, a counterfactual is not that.  “Ad event” is the most plausibly generic
* Let’s file an issue?  Let’s ask someone to come up with a better name and sell it?
* Martin: ISSUE #47 - HTTP API still needs some discussion. To what extent do want to build out the measure conversions API with an HTTP API
* Option 1: When you want a measurement report a response includes instructions from server. You’d say what the parameters are. Both versions of API presented earlier could work here.
* A little trickier because in the browser API there is a list of services. With HTTP there is not a direct way to get that answer immediately. Is that a concern? Maybe cookies work to set this? Something for people to think about.
* You could have a redirect that says “I’m not done here and I’d like you to make another request to another URL and here is the stuff you need to know to make that request such that the next request you’ve got the answer.” This would be tied in as a consequence of doing pixel loading or pixel loading. It’s an inverting request/response flow
* Charlie: what if you want to do the normal redirect
* Martin: Exactly, this is why we might want to consider not doing this design. It’s not ideal.
* Option 2: Response points to where conversion reports should be sent. “Here’s what I want you to do at this location to post the result”.
* Not part of the chain of processing. Probably more natural/reasonable. You don’t have to worry about restraints with headers.
* Probably wouldn’t use link relations like the issue currently suggests. But it would just be custom.
* Is this similar to how ARA works?
* Charlie: Yes. But link is fine. For instance, does CSS link rel work in header?
* Martin: Don’t think so.
* Kleber: Firefox supports, but no one else does
* Brian: Does failure have the same semantics in these options?
* Martin: No. This is a reason to have this as a separate process from redirects
* Charlie: Regular failures should be included.
* Martin: Yes, but we wouldn’t post the error. We’d use the reports and console log.
* Brian: Negative values in the histogram
* Erik: No, that messes up your numbers
* Martin: You can’t use negative indices. We can just tell you you’ve made an error.
* Charlie: What about stuff where we can’t tell you because of the leakage? Probably not in level 1, but a consideration for later.
* Erik: With this approach, how does budget get allocated to the intermediary who is issuing the request?
* Martin: answer in ISSUE #48. It gets kind of crazy. You’d have some sort of instruction in the response from the top level site. A template with a list of places it would post results to the intermediaries. Charlie’s idea to compress into measurement version is a better way.
* Charlie: if you do it all with measureConversion the advertiser needs to get a ping and return a list of URLs to fetch plus attribution logic for the browser to fetch and post back. All sharing L1 budget.
* Erik: What happens if there are two on the page?
* Martin: It does both but fails for one if there’s too much budget.
* Brian: Is there such a thing as a transaction ID?
* Martin: There is “delegation ID” to tie these all together so that everyone involved can connect to the same thing. But not necessary in any other interactions. It does complicate things quite a bit. May not need the delegation, but doing measureConversion would make sense. Might it be fine if some things are only doable in script? The top level site or its delegate will have to figure out how to delegate. Measurement partner would have to come back with the attribution logic and how to split the portions of the histogram across DSPs for example.
* Erik: if we want to do more advanced stuff it comes with this server side piece?
* Martin: Yes
* Charlie: How this works today is embedding a pixel. Probably doesn’t work unless we say anything on page is fair game to call. But this feels like it could be dangerous. In which case you might need an attribute on the tag that grants permission.
* Erik: Even if it was the default you could allow advertiser to turn off. Are we trying to avoid retagging or not.
* Brian: conversion retagging is hard.
* Charlie: think it is ok to fail open due to not wanting folks to have to retag
* Martin: you could make it so you could turn things off/permissive policy. Kind of there in permission policy. But perhaps worth dusting off the cobwebs.
* What sort of fetches? Links? Script and img? Programmatic fetches? Window.open??? A relatively small set of things, but this gets very invasive.
* We have a spec where we decide that a subset of [fetch destinations](https://fetch.spec.whatwg.org/#concept-request-destination).
* We gain people knowing which of their resources could do this. But it’s probably just fetch destinations basics: document, frame, iframe, image, empty string “”, script
* Aram: Video and audio destinations are the file load so you probably don’t need these
* Erik: there’s a use case for web identity
* Charlie: We should ask publishers. Aram, what do you think about elements requesting events? Is there value in limiting the types that don’t allow. Does it matter to open up to all of these.
* Aram: I think you want to limit
* Brian: Concur
* Martin: It's relatively easy to add and remove. At this point we just change the spec. For implementation it will just be the list in the spec to change in the code. Not a big thing for browsers to change. What meets publishers’ and advertisers' needs?
* Charlie: we should talk about document
* Martin: we might need ping
* Brian: Did ARA run into fraud concerns around these things?
* Charlie: Yes. That led us to the design of attr-src param because you have to change the HTML. We may have gone too far. Trying to steer us towards more permissive so that we don’t have to retag everyone because that was/is painful and ARA.
* Brian: Does this not represent vulnerability?
* Martin: Permission policy gives you the ability to name entities can call the API. The reason we’re talking about this list is because you may not want to think about font load for instance in permissions policy.
* Charlie: I don’t know if we want to support document. There are plenty of times you have an untrusted link. You’d need something more restrictive to support document. You don’t trust all the document loads you initiate.
* Room: agrees
* Martin: Maybe document, but only for same origins?
* Charlie: Maybe for level 1 we don’t support and then we revisit?
* Room: that seems reasonable
* Aram: should we include area?
* Martin: a ping solves. Do we really need frame and iframe?
* Charlie: we should double check that permissions policy is expressive enough for us
* Erik: Brian, we are making this overly permissive so that we don’t have to immediately retag. But we expect people will want to retag to make sure their budgets are efficiently spent.
* Martin: this becomes the “anybody” (not just “intermediary”) definition
* Charlie: we should probably think about system resources and disk space, but this doesn’t seem insurmountable
* Too hard to retag and will block adoption, but we understand that it can be spammed. If a bad thing happens you are incentivized to use the update to permissions policy.
* Charlie: Permissions don’t allow HTTP controls today.
* Martin: Do we split the permissions policy to have different JS vs HTTP?
* Matthew: Webkit may still use feature policy
* Martin: So you aren’t opposed, you’ve just not gotten around to it?
* Matthew: Not opposed. MDN may even be out of date.
* Brian: Is it easy to understand if Permissions Policy is breaking something?
* Martin: It’s specc’d but probably not implented. It does should up in console logs.


##### Discussion on renaming the API



* NAMING IDEAS SURFACED:
    * Private means more than what the API is doing. Suggest dropping. It also implies other APIs without the private prepend aren’t private?
    * Attribution
    * Aggregation
    * Matching Actions
    * Protected Attribution
    * Attribution Helper
    * Guidance for naming: name for what it does, not how it does it
    * Group suggestion is: “Attribution API”

Rename the API



* Alex: Don’t call it private. Very little benefit
* Charlie: cons on both sides
* Alex: Agree w/ Martin on keeping it simple. Publishers on multiple sites w/ private agg, reach / freq / understanding audiences.
* Kleber: Aggregation?
* Charlie: Don’t like aggregation
* Matt:
- [ ] ACTION ITEM: Chairs to issue CFC for [#202](https://github.com/w3c/ppa/issues/202).


#### "Other" Issues

Admin



* Sean: What day of TPAC do we want? Do we need 2 for the working group and 1 for the CG?
* Martin: That works
* Sean: AIs from last meeting? Demos/cookbook?
* Martin: Too early
* Charlie: Andrew says he has something that could be ready soon
* Sean: Create issue for people to document their important workflows for full test coverage
* Charlie: Maybe this is blocked by the demo? ARA has a frontend and backend. There’s a testing framework for the backend with events in JSON. Don’t need to specify HTTP requests or JS. This may be what Andrew is working on. Not exactly a web platform test, but a good high level test
* Martin: There’s an open issue for that
* Sean: File an issue to talk about flexibility of filtering?
* Charlie: We did that today
* Tara: Hit a milestone with working draft of PPA
* Sean: This may help browsers who were waiting
* Martin: Close, unless you want to use a TEE
* Sean: We have remote meetings until TPAC in November. We do have some offers of North American spaces in Spring of 2026


## Action Items



- [ ] Create a PPA folder for examples. People can add to it with PRs (Aram)
    - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
        - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example -[ Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))
- [ ] Develop Web Platform Tests in PPA (group)
    - [ ] Create issue for people to document their important flows for future test coverage (Aram)
- [ ] File an issue to talk about flexibility of filtering and impression logic based on April’s conversation.
- [ ] ACTION ITEM: Brian May: Write up enum for last touch attribution logic; see issue 42.
- [ ] ACTION ITEM: Paul deGrandis: Write down multi-touch attribution; add to issue #44.
- [ ] ACTION ITEM: Charlie and Erik write up CEL.
- [ ] ACTION ITEM: Aram Zucker-Scharff (following Brian May’s write-up): Submit issue to CG agenda around definition of last-touch and if we need more in the enum for level 1 logic options.
- [ ] ACTION ITEM: Chairs to issue CFC for [#202](https://github.com/w3c/ppa/issues/202).


## Future Meetings’ Information and Logistics

Remote meetings until TPAC in November and then again in December.

For TPAC CG/WG: We have a better chance of getting more time if we meet on Friday as we did in 2024. Should we do the same thing in 2025, i.e., as for multiple sessions on Friday?

—---------


## Joint PATWG/PCG


### **Agenda**



* Introductions, Code of Conduct, Minutes Document, Scribes
* Updates on Privacy Sandbox [#221](https://github.com/patcg/meetings/issues/221)
* Public dataset to benchmark private advertising proposals [#223](https://github.com/patcg/meetings/issues/223)
* TPAC Planning: Shooting for one session Thursday or Friday near WG
* AOB


#### Updates on Privacy Sandbox

Official Blog Post: [https://privacysandbox.com/news/privacy-sandbox-next-steps/](https://privacysandbox.com/news/privacy-sandbox-next-steps/)

Alex: Will do a quick overview of the state of the sandbox. On April 22 we announced via blogpost that we will no longer pursue the 3rd party cookie prompt and that we will keep the current settings for users. The why is multifaceted. We continued to have divergent opinions among stakeholders. During the last 2 years of testing we learned a lot, that there is a lot of work that goes into implementing these technologies and the world has changed. More private technologies outside of what we’re building in Chrome have evolved since we started.

The larger question I’m sure this group has is what does this mean for our posture to pursue private technologies on the web. The answer is that we are still here and will continue working on more private and data protective technologies on the web (for example, the work in PATCG/WG).

We understand that the technologies we currently have may play a different role and we are discussing with the ecosystem to understand where they can bring more value, where they no longer have value, and additionally what we call “greenfield,” where we might explore problem statements that we can address with the ecosystem while being protective of users’ privacy.

One important thing to call out for this group is we have consistently heard from different personas that focusing on measurement is valuable to developers. That’s a great signal to the focus on measurement in PATCG/WG.

Garrett: *What is the timeline for continuing the Chrome Sandbox experiment*?* I think this serves a useful coordination and testing function for industry. My sense is that several ad techs focus their Sandbox activity there (treatment_1 group) in part because doing so limits cannibalization between their traditional & sandbox systems (on the buy side).*

*This also serves a useful research function in testing & evaluating Sandbox. As researchers, we have made excellent use of this experiment. Also, given that ad techs focus on these groups, this helps identify which sites and vendors are using these technologies (see https://app.sincera.io/privacysandbox). Finally, I had a research idea yesterday I’d be really excited to try to do!*

Alex: Shortest answer is “no” we don’t have a timeline. We are working on giving an update.

Michael: I think Garrett he is referring to a group (treatment_1 or “Mode A”) of users that has the APIs turned on and the traffic is labeled. There is no behavior difference in this group, this is just a group of people that we suggest people focus their testing there. That group has been shrinking over time because it only includes a percentage of people who were using Chrome when we turned on the Privacy Sandbox experiment, minus some users that opted in to join the group. We haven’t shared a timeline for switching this off.

Martin: can you please share the number of people that have opted in to be in the group

Michael: unfortunately no.

Alex: Eventually the experiment labels will end.

Michael: thanks to the storage access header (recent launch) adtech can tell if a 3PC is available so that adtech can use that slice of traffic to do some more testing knowing that other adtech will also not be able to use the 3PCs.

Aram and Brian: Yes this would be useful to have better visibility to the top window/1st parties.

Brian:  2 things. Wrt the labeled groups what is the drop rate?

Michael: we don’t have that now sorry

Brian: the merging of the attribution API and ARA, when will that happen?

Michael: that’s a great question. Of course we would love to go towards a world where we incorporate and consolidate functionality. As things stand today given the limited scope of phase 1 of Attribution API, we are not close to that, we would first need to see a material amount of parity and then we need to have a good deprecation plan. Of course we want to launch this functionality in Chrome, we think the value of having an interoperable API is great for us.

Miguel: Alex presented last week in IAB TL. I wanted to share some insights/feedback from Tech Lab members that call. Some saw value in helping with fraud detection and IVT. Also, measurement , web2app attribution measurement people would love to see something like ARA. The question was also asked on what’s the point of having ARA with 3PCs. An “advertising ID” was also mentioned by a few Tech Lab members.

Aram: I wanted to answer why do 3PCs exist (from the perspective of this community and the working groups). They are not fit for the purpose they are used for. So putting together an API that is fit for purpose with input not just by the IAB but also other stakeholders is valuable. According to some sources 30% to 40% of users have 3PCs turned off. That doesn’t even account for matching issues, or user opt outs. I also want to briefly address the question of an advertising ID. This was discussed 5 years ago I think, at the earliest meeting of this CG, and it was not accepted then either. This will not happen in the context of this group, and is, in my opinion, not good for the web; this group will not work on it, just to settle this question in the context of the PAT groups and their relationship with other groups.

Michael: We want the web to continue working well for all users, inclusive of those that turned off 3PCs. I don’t have numbers on how large the population is by adtech can measure that themselves

Miguel: what about ARA?

Michael: Yes we are very interested in continuing to develop it, we are interested in continuing to develop those capabilities. On web2app, we see that as a potentially useful thing, it requires collaboration with Android, and even iOS. If we can get buy in from the people who own other areas we will address that?

Brian: Are there any plans to retire any of the existing APIs?

Alex: Everything is on the table. We are like we said listening to the ecosystem to find out where there’s value. We may hear there are things we should stop developing or even deprecate. When we make decisions about those things we will give an update.

Michael: We want to make sure we don’t take things away without hurting people.

Tomas: adtechs are interested in developing an interoperable measurement API, so we want to ask Google to continue on this journey.

Martin: I got a list of suggestions, I’ll share that later keep that to myself for now (some to keep some to throw away). Do you have timelines for when these decisions on what to do will come?

Michael: so it has been 6 weeks and we are in the process of doing this listening tour. The CMA may have questions on the items we propose. We don’t have a timeline

Brian: Should developers be adopting the APIs while we wait?

Michael: We spent years telling people to adopt the APIs because 3p cookies were going to go away.  Now that that pressure has been removed, we understand that the incentives to adopt the APIs are much lower than they were two months ago.  I'd be surprised to see new adoptions right now.

Brian: some people in IAB TL asked for support in consent signaling.

Alex: Yes we have heard this feedback. I wouldn’t call it a theme the same way we heard about measurement (in terms of how frequently we’ve heard about it). But it has come up a few times in the feedback we’ve heard.


#### Public dataset to benchmark private advertising proposals

[Criteo presentation by Alois]

Brian: 30 days data collection, do you have another 30 days on top for that?

Alois: yes we have windows but I cannot disclose. Paper ([https://arxiv.org/pdf/2502.12103](https://arxiv.org/pdf/2502.12103))  mentions a 28h window for sales attribution.

Watson: if I understand correctly, these ads that were entirely displayed are in the data so the model knows what to look for to show an ad but cannot know the opposite

Alois: Another study that we do is the second price bidding so that very valuable for us.

Tomas: probability of winning a display does not equal probability to be chosen

Brian: link to the criteo dataset?

Sean: it’s in the agenda and the corresponding GH issue


###
    ***** QUEUE *****

(`qq: Name` to ask immediate questions for clarification. I will *italicize* to acknowledge, we will bring it forward at the next break/pause



1.
2.
3.
4.
5.
6.
7.
8.

 **QUEUE OPEN**


#### TPAC Planning


#### AOB


## Participants (Day 1)



1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Benjamin Case (Meta)
5. Martin Thomson (Mozilla)
6. Paul deGrandis (Kevel)
7. Isaac Foster (MSFT Ads)
8. Miguel Morales (IAB Tech Lab)
9. Thomas Prieur (Criteo)
10. Charlie Harrison (Google)
11. Michael Kleber (Google Chrome)
12. Matthew Finkel (Apple WebKit)
13. Erik Taubeneck (Apple)
14. Andrew Paseltiner (Google Chrome)
15. Tara Whalen (W3C)
16. Matthew Finkel (Apple WebKit)
17. Fabian Höring (Criteo)
18. Arpana Hosabettu (Google Chrome)
19. Aloïs Bissuel (Criteo)
20. Taiwo Idowu (Google)
21. Maxime Vono (Criteo)
22. Alonso Velasquez (Google Chrome)
23. Ehsan Toreini (Samsung) ←- observer
24. Garrett Johnson (Boston University)
25. Roxana Geambasu (Columbia University)


## Participants (Day 2)



1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Martin Thomson (Mozilla)
5. Tara Whalen (W3C)
6. Matthew Finkel (Apple)
7. Erik Taubeneck (Apple)
8. Miguel Morales (IAB Tech Lab)
9. Aloïs Bissuel (Criteo)
10. Taiwo Idowu (Google)
11. Isaac Foster (MSFT Ads)
12. Paul deGrandis (Kevel)
13. Alex Cone (Google)
14. Alonso Velasquez (Google Chrome)
15. Michael Kleber (Google Chrome)
16. Thomas Prieur (Criteo)
17. Aloïs Bissuel (Criteo)
18. Arpana Hosabettu (Google Chrome)
19. Ehsan Toreini (Samsung) ←- observer
20. Garrett Johnson ( Boston University)
21. Andrew Paseltiner (Google Chrome)
22. Chris Needham (BBC)


## Participants: CG



1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Martin Thomson (Mozilla)
5. Tara Whalen (W3C)
6. Matthew Finkel (Apple)
7. Miguel Morales (IAB Tech Lab)
8. Aloïs Bissuel (Criteo)
9. Taiwo Idowu (Google)
10. Isaac Foster (MSFT Ads)
11. Paul deGrandis (Kevel)
12. Alex Cone (Google)
13. Alonso Velasquez (Google Chrome)
14. Michael Kleber (Google Chrome)
15. Thomas Prieur (Criteo)
16. Aloïs Bissuel (Criteo)
17. Arpana Hosabettu (Google Chrome)
18. Ehsan Toreini (Samsung) ←- observer
19. Martin Pal (Google Privacy Sandbox)
20. Andrew Paseltiner (Google Chrome)
21. Chris Needham (BBC)
22. Pierre Tholoniat (Columbia University)
23. Kyle Hogan (MIT)
24. Nirvan Tyagi (University of Washington)
25. Watson Ladd (Akamai)
