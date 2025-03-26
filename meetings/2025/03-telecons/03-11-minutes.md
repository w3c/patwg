# Private Advertising Technology WG - 2025-03-11

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Paul deGrandis (Kevel)
4. Benjamin Case (Meta)
5. Alex Koshelev (Meta)
6. Ben Savage (Meta)
7. Charlie Harrison (Google Chrome)
8. Tara Whalen (W3C)
9. Martin Thomson (Mozilla) 
10. Brian May (unaffiliated)
11. Arpana Hosabettu (Google Chrome)
12. Haley Patoski (Google Chrome)
13. Miguel Morales (TechLab)
14. Andy Leiserson (Mozilla)
15. Andrew Paseltiner (Google Chrome)
16. Nick Doty (CDT)
17. Michael Kleber (Google Chrome)
18. Garrett Johnson ( Boston University)
19. Thomas Prieur (Criteo)
20. Phillipp Schoppmann (Google)
21. Erik Taubeneck (Apple)
22. Aloïs Bissuel (Criteo)

## Agenda

### Hellos, Intros, Policies, & Call for Scribes

Scribes: Paul deGrandis & Michael Kleber

Policies: See WG Read All About It Slides.

### Review of Action Items

- [x] ~~Consensus call: Adopt PATWG charter repo~~
      - [x] ~~Move to W3C (Aram)~~
- [x] ~~Consensus call: Adopt PPA-API as FPWD~~
      - [x] ~~Auto-Publish included~~
      - [x] ~~*Process Explainer: Issue \-\> PR \-\> Merge (Sean)*~~
            - [x] ~~*See [https://github.com/w3c/ppa/pull/114](https://github.com/w3c/ppa/pull/114)*~~
      - [x] ~~Land PPA-API in right place (Tara) (remaining where it is)~~
      - [x] ~~Establish Admins for Repo (Aram)~~
- [ ] Create a folder for examples. People can add to it with PRs (Aram)
  - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
  - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \- [Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))
- [ ] Web Platform Tests in PPA
- [x] ~~Update meeting template to include a standing item for PPA Topics (Sean)~~

## June Meeting Logistics

Sean Turner (Chair): There is a [form](https://forms.gle/LSs5xoL5NzFwneAA9) for an in-person meeting in June, in Paris (thanks Criteo).  Please see the form in Slack.  We’ll close out the form in a week.  We have to give two months notice (best effort hybrid).

Alois Bissuel: June 10th / 11th is the preferred date, as on the 12th we will have to leave the room at 4PM.

## Results from running Hybrid scaled tests

[Slides](https://docs.google.com/presentation/d/1HCqxDh-gmGG_2uE2sdYtFfdTaBR5D8tOgEb-Lz2Brv0/edit?usp=sharing), [agenda issue.](https://github.com/w3c/patwg/issues/29#issue-2883018586)

Ben Case (Meta):

Update on the hybrid proposal and engineering implementation from Meta/Mozilla, using similar mechanism to the previously-reported-on IPA work.

Hybrid proposal is a combination of IPA, PAM, and ARA.  On-device attribution and DP budgeting, impression-level match key to join conversion reports off-device in an MPC.  Allows late binding of breakdown keys.  ("What else could you want for Level 2?").

Scale test: 3-party MPC, malicious honest-majority MPC — more efficient than 2-party MPC that we've been discussing for PPA.  Compute OPRF, bring conversion reports and impression reports, aggregate by breakdown key, add DP noise.

Alex Koshelev (Meta):

High-level diagram of deployment (in slides).  All three helper parties reading from same AWS bucket, provision ahead of time (so infrastructure already spun up before load test), 50x copies of each server.  All shards independently and in parallel pull from the S3 bucket; shards check for duplicate reports (based on a hash) and abort if any duplicates found.

Alex Koshelev (Meta): MPC does not need cross-shard communication, since there are no shared records across the partition, so get great parallelization.  Shuffle protocol does need some cross-shard permutation, but that communication is only within a single helper; use shared randomness within a single helper for the shuffling.

Each shard computes its own contrib to the final histogram, then all 50 partial histograms sent to the "leader shard" number zero, which adds the randomness.

Experimental setup: 150 EC2 instances (50 per helper), two sizes 1 billion and 2 billion events, so 20M or 40M per shard, 256 breakdown keys.  (Number of breakdown keys is the thing that determines scaling.)

Outcome: 1B events → 64 minutes run time, 1.25 TB bandwidth, 425GB egress per helper (all 50 shards), 120GB ingress per helper.  Using public AWS pricing, cross-shard traffic is free and $0.09 per GB helper-to-helper, so 30 cents per million reports.

Summary: 3-party MPC can process terabytes of input; zero-knowledge proofs that upgrade to handle a malicious helper party scale sub-linearly, so great for cost.  There are still opportunities to improve (OPRF is compute-heavy and would benefit from specialized hardware that AWS doesn't offer), and known ways to make implementation more efficient.

Q \[Charlie Harrison\]: 30 cents per million reports: so $30 for the 1 billion reports?  
A: No, $300, off by one factor of 10\.

Q \[Brian May\]: Processing time: around 1hr, so could you make faster with more shards and paying more money?  If you add 10 more shards, what happens?
A: We did a test with 30 shards (instead of 50), took about an hour and a half.  We didn't compute the cost.  70% of the cost is compute cost, so with 30 shards \* 1.5hrs the overall cost would be a little higher.  Cost is a little misleading, $1.50/hr EC2 is certainly something you could do better.

Ben Savage: We've spent all our effort optimizing on network, which was always most expensive before.  We can optimize for CPU also, now that that's what matters.

Martin: Can't shrink too much on machines; you'll run into a memory limit.

Brian: Bigger clusters are going to be better generally?

Martin: Overhead for having too many nodes.

Ben: Roughly linear.  Double the instances, take half as long, close to a wash on cost.

Brian: Would be helpful to see something on cost for different input sizes and different cluster sizes.

Martin: Yes, you can just draw a straight line from the data points we have.  And these are upper bounds on cost — expensive network, expensive machines, you could get most of the throughput for substantially less money if that's desired.

Q \[Aram\]: Were you thinking about real-time, Brian?
Brian: No, just looking at cost overhead for a campaign.

Q \[Charlie\]: For the hybrid test we have input records — raw impressions and raw conversions.  Does the protocol scale based on what fraction are impressions vs conversions?  and if it matters, what ratio did you use in the test?

Martin: Oh the ratio does matter, that's true.

Ben Case: Only need replay protection on conversion reports, drop impression reports before aggregation.  So it's a good question, we didn't really dig into this.

Martin: This *might* have been the worst case, 1:1, so that very few conversions would have made it better.

Ben Savage: Full malicious PRF \+ shuffle is most of the CPU, and those need to happen irrespective of the ratio.  So there might be a win on network.

Ben: Maybe more benefit in later stages, like scaling breakdown keys.

Q \[ Alois\] (scaling with respect to breakdown keys): Sizing of breakdown keys?  If you wanted 10x more or 10x less breakdown keys, how would that affect it?

Alex: Breakdown affects scaling of the aggregation stage and noise, but not PRF computation.  Everything scales linearly (or worse) with number of breakdown keys.  This implementation is the best one we've seen in terms of breakdown key scaling, because of the number of shards, but doesn't help after we aggregate the histograms onto a single shard, so that part will cost more (both bandwidth and latency).

Alois: So 10x breakdown keys is 10x more cost?

Alex: It won't cost 10x more, because it only affects a single stage of the protocol, but we don't have data, because our implementation was tightly tied to 256 breakdown keys.

Martin Thomson (Mozilla): The thing that scales with breakdown keys is a relatively small stage and should increase around linearly, so would be much better than linear overall.

Ben Savage (Meta): Also a log increase for aggregation and shuffle.

Q \[Brian\]: It would be helpful to folks on the ad tech side to be able to run simulated workflow on realistic data — lots of events with no data attached to them that can be dropped immediately.

Q\[Miguel Morales\]  (Clarification and documentation of proposal): Anywhere to find out more information about this proposal itself?  Browser would send impression events to the server, and then joined by some sort of key?  Can you tell me more?  

Ben: [Link in the slides](https://github.com/patcg-individual-drafts/hybrid-proposal), which we will share.  Randomly-generated key used to send a report back to whoever shows the ad at impression time; at conversion time the same id is sent with the conversion report.  It's good on the data minimization side; got support from browsers in the past.  The data that leaves the device can't be tied back to any one device, just joining up two events.

Ben Case: Scaling in IPA world: Since budgeting and capping all happens in MPC, it's best to put all reports into one big query.  With the hybrid proposal and on-device DP budgeting, you-the-client only pay for queries that you contribute to.  So you can just put in queries at the size you need them to be aggregated at; no need to glob everything together into a single mega-sized query.  This 1B query size seems realistic.

## Process Explainer

Sean Turner (Chair):

As requested last time, I put together a summary of how the WG process ought to work.  Please take a look at this [link](https://github.com/w3c/ppa/pull/114/files).

Please take a look at the open pull request, we'll pin it to make it easy to find.  We'll do a consensus call in a few weeks.

We could make the repo send to the PATWG mailing list, but that would cause a lot of email.  So just follow the PPA repo yourself if you want to.

Brian: Maybe the weekly summary version?  Sean: We'll (ask Tara to) look into it.

Sean: If you've done GitHub-heavy projects before, then we don't expect the process.md doc to be much of a surprise, but please take a look.

## PPA Discussion Topics

Aram: Call for any issues that people want to discuss.

### \#107 [https://github.com/w3c/ppa/issues/107](https://github.com/w3c/ppa/issues/107)

Martin: \#107, considering two aggregation types, TEE-based or Prio-based (DAP).  Prio needs submissions to come along with a proof that the histogram contrib per bucket is below the pre-chosen maximum.  Right now, for efficiency, we only allow the cap to be an L1 norm of the form 2^n-1 for some n.

After discussion with Charlie, we're OK with that — if the histogram contributions are 1-100, then the proof only bounds contrib in the range 0 to 127\.  Malicious client could cheat by at worst a factor of two.  This seems like a reasonable outcome; speak up if you have a problem.

Charlie: Only issue here is for ad techs that really care about limiting invalid reports, they need to know this implementation limitation, and pick 2^n-1.  Not that big a deal, but some cut points are better than others.  Feels like a fairly minor down-side.

Martin: We'll need to document it carefully, but seems OK.  There is another possibility, to make bounds proof-tight, but submissions would end up being larger.  Not much reason to think the benefit is worthwhile.

Charlie: Can we close the Issue, to document that we've talked about it and there were no live objections (and also no previous github objections)?

Martin: Only action is in spec'ing the DAP implementation and documenting the caveat.  Will update the issue.  Issue updated, but let's keep it open, will close with a documentation PR.

### \#77 [https://github.com/w3c/ppa/issues/77](https://github.com/w3c/ppa/issues/77)

Martin: I was looking at the DAP implementation, we're going to need to send the spent epsilon to the server.  Could send an IEEE float, but that's fraught with peril.  I've spec'ed it as milli-epsilon as an integer.  For ensuring remaining in budget, going to need to do a comparison, which is easier with fixed-point than floating-point.

Charlie: My original proposal was milli-epsilons, but still need to round when you perform the division to get milli-eps in the first place.  From the prev discussion, maybe we're fine with the rounding in floating point, and give some buffer.  Should check if we're still comfortable with that in milli- or micro-epsilons world.

Martin: If you divide your privacy budget 1000 ways, you end up off in the 14th decimal digit at worst.  Would be bigger with micro-epsilons, so maybe 1 milli-epsilon extra.

Charlie: Should be fine, let's review the numbers for worst-case rounding.

### \#93 [https://github.com/w3c/ppa/issues/93](https://github.com/w3c/ppa/issues/93) 

Charlie: Document structure: would like feedback from the group on the way for the spec to be written.  Starts with an explainer, then API surface, then internals and discussion.  Merging the spec and explainer is a choice.  If you have opinions about how you'd like the spec laid out as a document, please give feedback, in the issue or to us directly.

Brian: What section includes the wire format of the encrypted reports?

Charlie: There are specific backend sections for TEE vs MPC, which is where that would live.  But so far this is just about API *inputs*, not outputs, so that's kind of hidden.  API usage section is in progress, the aggregation protocol itself will be its own section(s), or maybe an independent document.

Aram: I encourage folks to take a look at the issue.  This looks good to me as a proposal, hard to tell?

Charlie: This is kind of how the document is coming together right now, so you should be able to feel right now whether the doc seems good to you.

Sean: This seems good now, lets people ease in at one end of the pool and only go as deep as they want to.

Martin: Yes, that's the goal.

### How much do we need HTTP-headers-based hook?

Martin: We might consider doing that next, but not a lot of clarity on the needs.

Charlie: We had a chat with Andy last Thursday, we're happy to help out.  Still important from the Chrome POV.

### \#17 [https://github.com/w3c/ppa/issues/17](https://github.com/w3c/ppa/issues/17)

Martin: Define the epoch.  We've been acting as if it's one week.  Does anyone want to challenge that?

Charlie: I think it's premature to ask this.  It really depends on other parameters of the API, how people will deal with bias vs. variance, chatted about this at TPAC and we did not come to a solid conclusion.  I don't think we have more info now than we did then.  We could pencil it in I guess, but I don't think we're ready to call this a firm decision.

Aram: Maybe get more input as more people get experience with the system.

Martin: Putting aside the duration, there is a separate question of when the epoch rollover happens.  Does it start at Sunday Midnight in some fixed time zone?  Does it start at the moment you first spend budget and then run for a week from that point?  Would you rather be sure that queries on Sunday/Monday are most likely to have budget, and factor that in to your API use?  Or would you be happy with a budget that varies per person, based on when they first used budget, and then have some kind of unknown distribution of positions in the budget cycle across users?  That might have biases more evened out.

Brian: I'd say you want the second; the first would impose structure on campaigns that we might not want to deal with.  Based on Martin, pick a tentative answer for the second, until we see a reason to do otherwise.

Ben S: Is this a global reset time across the whole world, or per-device approach? Privacy budgeting is applied on device. “In whatever timezone for this device, reset at midnight”.

Martin: What happens when they move, etc? Cumbersome. My current calculation has the unix epoch (thurs morning at midnight)?

Ben: Small % of people move, we can contemplate this.

Charlie: There are a few issues where I felt like a global reset point might be useful. One is there is a bias / variance tradeoff. But unless you have the bias reporting extra feature you might not notice if you are getting truncation. Good and bad. Smooth. But could lose a lot of data you don’t realize and it might be harder to realize you made a mistake. If we allow clients to report their truncation maybe that is not the end of the world. The 2nd issue is that there is like some optimizations if you are targeting impressions only in the current epoch you are in right now. I think this is in the [issue single epoch optimization](https://github.com/w3c/ppa/issues/78), also specified in cookie monster. But if you are a super advanced ad tech and you want to leverage as much as possible for good optimization of budget than for each user you want to know where the user is in their epoch. Easier for a global public thing. If not is it ok to tell the caller where the user is in the epoch. Is that a privacy violation?

Martin: Can’t be. If you start the clock running than they’ll know what happened. No point in hiding epoch rollover.

Charlie: We couldn’t tell the caller when the epoch rolled over?

M: That is fine. If we make it fixed or make it start running the first time they call the API then callers will know.

C: If we start it when they call a conversion than yes we can make it public. We prob should have an API that tells you so ad techs do not have to maintain on their server. Overall despite those issues, the dynamic budget windows seems to me to be the better of the two options. Mostly because it reduces one arbitrary choice we need to make.

Kleber: Really worried about the bias of different times of epochs having different states of users. Extremely hard to debug errors at analysis time. More likely to be out of budget on Saturday than Sunday could be bad. IMO, most natural thing is to do *neither* of the two options, but rather have every client pick a random time during the canonical epoch to be the beginning of their week (on that site). Each browser makes a random choice. Your probability density of epsilon available is smeared across the week. No lumps. Starting the epoch when the first budget deduction happens doesn’t actually help, it just makes it more complex to guess. That first budget deduction is based on ad campaigns / user behavior /etc. Not sure about single budget optimization.

Brian: campaign specific?

Kleber: advertiser specific, since the budget is per advertiser site.

Charlie: The single epoch optimization says that if your conversion only matches impressions from the current epoch than you only have to deduct the budget that you use. And then what that means is kind of technical and depends on the attribution model that you use and depends on l1 budgeting vs l2 budgeting. But I don’t know if it is a big enough optimization to impact our decision here. We can take advantage of that optimization without knowing where the budget starts. Mby that is good enough. Only really gives you benefits for more optimization models. My understanding is that there is no benefit in that case. More complicated MPA/L2 thing? For single epoch optimization to give you any wins.

Michael Kleber: Is it a fixed set of epoch optimizations?

Charlie: No. Cascading effect.

MK: While that has value, the people being at a random unknown point in their epoch cycle or making an api that returns where you are in your epoch cycle are better tradeoffs than dealing with a weird unknown of where people are with the are budget availability.

Erik: If we provide an API that tells you where you are in a random timestamp, fingerprinting vector.

Michael: Only in a single site.

Erik: Available for a long time.

Martin: Secret key, PRF, technically totally workable.

Erik: Clock skew implications. Not sure how to affect this.

Kleber: My solution doesn’t care about clock skew.

Erik: State of budget. Not able to access how much budget is available. Should be possible to know how much you’ve requested (public). The client could maintain how much has been requested. You could know some minimum bound. Throw one more option. Instead of resetting at an epoch. there could be a “rate of replenish”. Every day you could gain some epsilon.

Martin: we talked about this in boston. it doesn’t fit into the analysis that we have so far.

Ben Case: With the current budget assigned per epoch there is always a fixed (non-replenishing) budget available to query any actual event that happened in the past. If you have a replenishing budget you could blur that line and might get an increasing budget to query one event in the past.

Paul deGrandis  (Support for Michael’s idea; ignoring technical implications) : Much prefer Michael’s approach. There are ways in which option 1 and 2 could look similar. Michael’s solution fixes this.

Martin: Also like Michael’s suggestion.

Aram: Come to an answer on this issue? Yes? Let’s document in the issue. Move on to the closing stuff.

## AOB

None.

## Future Meetings Information & Logistics

Sean: Next meeting is scheduled for 15 April 2025.

Sean: Reminder to fill out the [form](https://forms.gle/LSs5xoL5NzFwneAA9) for June.

## Review of Action Items From This Meeting

- [ ] Fill out [form](https://forms.gle/LSs5xoL5NzFwneAA9) for June meeting (Everybody)
- [ ] Create a folder for examples. People can add to it with PRs (Aram)
  - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
  - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \- [Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))
- [ ] Develop Web Platform Tests in PPA (group)
  - [ ] Create issue for people to document their important flows for future test coverage (Aram)
- [ ] Weekly summary of ppa repo (Tara)
