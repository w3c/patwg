# Private Advertising Technology WG - 2025-10-14

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Tara Whalen (W3C)
4. Benjamin Case (Meta)
5. Roxana Geambasu (Columbia University)
6. Charlie Harrison (Google Chrome)
7. Michael Kleber (Google Chrome)
8. Martin Thomson (Mozilla)
9. Ethan Leeman (Google)
10. Alex Turner (Google Chrome)
11. Sid Sahoo (Google)
12. Martin Pal (Google)

## Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes  
2. Review of Action Items from Previous Meeting(s)  
3. PPA Discussion Topics  
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)  
      1. [Optimization Query Evaluation Framework \- Issue \#60](https://github.com/w3c/patwg/issues/60)  
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)  
4. AOB  
5. Future Meetings Information & Logistics  
6. Review of Action Items From This Meeting

## Hellos, Intros, Policies, & Call for Scribes

Scribes: Michael Kleber

Policies: See WG Read All About It Slides.

## Review of Action Items

- [ ] Aram [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
    - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
    - [ ] Will keep open for now
- [ ] Develop Web Platform Tests in PPA (repo)
    * Aram: Create issue for people to document their important flows for future test coverage
    * Will keep open for now
- [ ] Brian and Miguel to provide feedback on [Issue \#204](https://github.com/w3c/attribution/issues/204)
    * Sean Turner \- to reach out
- [ ] Brian and Miguel  to provide feedback on [Issue \#222](https://github.com/w3c/attribution/issues/222)
    * Miguel couldn't join today, but will add some references for this to a recent IAB Tech Lab document on "IAB Tech Lab Attribution Use Cases:" [https://iabtechlab.com/identity-addressability-privacy/attribution-use-cases/](https://iabtechlab.com/identity-addressability-privacy/attribution-use-cases/)
    * Sean Turner \- to reach out
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [Issue \#256](https://github.com/w3c/attribution/issues/256) should be reviewed by the Community Group
    * What issue was this intended to refer to — [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)?  We did tag it as a blocker.
    * [\[For PPM\] PPA queries in DAP](https://docs.google.com/document/d/1-BOnlUus2LtFk-Oesauijrb5PFaUeyBqVwhTfeEIFcQ/edit?tab=t.0#heading=h.q0k70yp654td) — what we should test for, maybe this is what we're looking for feedback on? This should be easy.
    * We will try to bring it up for discussion in the CG meeting in two days.

## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

#### [Optimization Query Evaluation Framework](https://github.com/w3c/patwg/issues/60)

Ben Case and Roxana, and associates.  Slides link: ([slides](https://docs.google.com/presentation/d/10BSQHXqGM61T9Cf8qb_E8rHX5L5zazxBf_pdJRu2Ic8/edit?usp=sharing)).

* Attribution level 1 is all about Measurement queries.  Some amount of optimization is possible built on that alone, but there is a lot more that could be envisioned for future work.
  * Level 1 can support single-advertiser queries, while optimization for cross-advertiser queries would be Level 2\.
* Methodology: Based on Criteo dataset (thank you Criteo\!\!), focusing on Learning from Labeled Proportions (LLP)
  * Want to predict click-throughs with large skews between large and small advertisers.
    * Very imbalance, 1% of advertisers account for 30% of impressions
  * LLP: Train on "bags" of examples with known label proportions; features are  known but labels are hidden.  Level 1 is a good fit for learning label proportion estimates:
    * Assign impression to bag at impression time; DP aggregation at conversion time lets you learn noisy conversion rate in each bag.
    * Single-advertiser setting: Bags must be per-advertiser (matching DP guarantees).  This means small advertisers may get too few conversions for their bag to overcome DP noise, so the model does poorly on them.
    * Cross-advertiser: Can include small advertisers in good-sized bags.  Does LLP improve in this case?
  * Multiple settings to be tested:
    * A \= No LLP, no DP.
    * B \= LLP, no DP, random bagging (bags of size 20-30, so 10-20k bags, here and for all below — relevant for the conversation about histogram domain sizes).
    * C \= LLP, no DP, per-advertiser bagging.  
    * D \= LLP, cross-advertiser DP query.
    * E \= LLP, single-advertiser DP query, drop too small bags.
    * F \= like E but keep small bags despite too much noise.
    * Evaluate CTR predictions on held-out set, looking at all vs. small-advertiser.
  * Preliminary results (Criteo dataset, notebook-based, so no on-device budgeting):
    * b=0.1 i.e. not much noise: all pretty tightly clustered
    * b=0.5, b=1: E,F do much worse ROC curves
    * starting around b=2, small advertisers are close to adding random noise
    * starting around b=5: D performs worse than baseline
  * Charlie: Noise from discrete rather that continuous laplace distribution might make a big difference here.
  * Utility for small advertisers specifically:
    * overall ROC curve for small-advertiser queries seem to stay pretty close to corresponding all-advertiser queries.  (Note these are even more preliminary results, don't hold us to this\!)
  * Next steps:
    * Actual budgeting (on-device etc)
    * Other bagging strategies (trading off bag size vs noise, histogram width, etc)
    * Approaches other than LLP, e.g. weighted aggregate logistic regression, and open to suggestions on other areas
      * Charlie: with bag size \= 1, this matches the ARA event-level API.  Won't really work in MPC, since too many bags, but in theory could implement using a local algorithm.  We don't have anything to share about this, but it would be interesting if your framework can handle bag-size 1, where other noise mechanisms are available (like debiased randomized-response)
        * Martin: So the algorithm is averaging the label over the bags, so bag-size 1 is baseline?
        * Charlie: smaller bag size generally gives better performance, but this is not a theorem
        * Roxana: Loss looks very different when applying noise at the event level
        * Charlie: Not a natural way to instantiate that in the existing spec, but if it had good performance for the privacy parameters that we care about then we could work it in.
        * Martin: Picking fixed bag size is somewhat artificial, since at impression time in the real world you wouldn't actually know the number of impressions.
    * Charlie: Would be natural to compare against DP-SGD.
      * Yes, we will add it.  Makes sense once we use the real attribution model so that you can match the privacy guarantee.
    * Ethan Leeman: The Criteo dataset has timestamps — does it make sense trying bagging by hour, for example?  This simulates how often you train a model.
      * Martin: This will just push towards making a case for larger epsilon
      * Ethan: of course performance will go down, it's kind of creating more small advertisers
    * Martin: Most interesting to me is that it seems like the small advertisers really do get a lot of benefit from riding on the coat-tails of the large advertisers.  That's pretty encouraging\!
      * Looks like all the message here is that it's about data volume — if you use b=0.2 but double the volume, does it look like b=0.1 again?
      * Why even train on small advertisers?  Is it \*all\* from training on large advertisers?
        * Ben: No, the 65% of conversions from small advertisers really does improve things.  We could measure whether this is all due to the small advertisers helping the large advertiser queries, or whether they are helping themselves as well.
    * Roxana: Criteo mentioned the 1-conversion-per-week scale (in a previous meeting).  Not even clear what to measure in that case; API would be useless.  But with clustering and measuring together, maybe it is possible to group them and let them learn something useful.
    * Ben: This work supports the usefulness of cross-advertiser queries, so assuming there are browsers that will want small epsilon, this work has value.
    * Ethan: Looking at ROC curves misses stuff, especially with the scaling they did in the data set.  Could we get weighted cross-entropy loss, as in the Criteo paper?
      * Roxana: Yes, this is early, we will do this.  Thanks for the feedback.
      * Ethan: There are specific metrics Criteo used in their paper (up-weighted LLH?), let's be sure we get those.
      * Ethan Addition: LLHCompVN from the Criteo Private Ad Paper [https://arxiv.org/pdf/2502.12103](https://arxiv.org/pdf/2502.12103) page 7

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

Martin: \#289. [https://github.com/w3c/attribution/issues/289](https://github.com/w3c/attribution/issues/289)

Martin: **CR blocker**: Attempt to revisit how we're thinking of impressions.  Originally imagined that an impression \= distributing info to conversion sites it identifies.  Recently identified the case where any site might query impressions, so that concept has been weakened.  Andrew pointing out that when a site registers an impression, we might want to let a site "take it back later" (to support clear-site data).  We probably want to be more deliberate about this — mistakes, and fixing them, happens in the real world.  But maybe clear-site-data isn't the right API for that?  This is the nuclear option, but maybe letting sites e.g. fix things by time range would be more deft.

Charlie: That argument doesn't necessarily mean we shouldn't do this, maybe just hints at once we have the mechanism to support clear-site-data, we should expand to cover more use cases also?

Martin: Yes, but let's understand the space of "more use cases" first.  For example, lose both incoming and outgoing impressions — is that right?  Should be a deliberate decision.

Aram: Agree that there should be a way to remove impressions.  Not just for errors early on, but on an ongoing basis.  Would be good if there were a way to do that without using all the analytics — would be appreciated by domain owners showing the ads.

Martin: exactly why we should think about this more thoughtfully.  E.g. an API for an advertiser like "if I have registered an impression that meets criteria X, then delete it".

Aram: Based on date/time is probably the most important use case by a lot.

Martin: Maybe what's suggested here is fine, and we would need a separate API for the other use case.

Charlie: So is this fine and we could add more on top of it, or is this wrong and not useful or in conflict with the good solution?  I think before this patch the clear-site-data spec was a little weird and inconsistent, so the PR linked to this is necessary for some stuff, and is principled and consistent.

Martin: Yes, the question is whether the fix should have been done a different way (point 3, the intermediate site check).  Probably a mistake for an intermediary on site A to be able to clear impressions it stored when it was an intermediary on site B?  Breaks some independence expectations.

Charlie: Actually I think this is fine?

Aram: This seems like a good thing actually?  Users expect the site can clear all of its data if they ask it to?

Martin: Already not the case for something like partitioned cookies.  This is a little weird, since people can't observe the data store, but it's strange.

Aram: Would be valuable to preserve the options, though would be good to have another option.  Need to support what the market does as well as what users expect the market does.  There may well be a future where sites have to propagate data deletion requests to the third parties they use; we shouldn't pretend to predict how the law will go here.

Martin: That will be very difficult for a browser like us that does partitioning.  There is a bit of a blind spot when 3p cookies are available (eg with Storage Access API this can happen in any browser) — when I'm on Washington Post and Google is asked to delete their data and they have access due to SAI, what is supposed to happen?  This is a mess.

Aram: So what are we looking for here?  A call one way or the other?

Martin: I'm convinced by Charlie's argument that we can do both, allow clear-site-data and a more refined thing.  But also I think the intermediate-site data is more of a mess than I realized before.  I would be OK landing the PR as-is, but I think we need to put a pin in the intermediary site question and come back to it as a separate issue.

Charlie: Let's land the PR, since it makes clear-site-data more consistent.  Then we should do nothing more until we get someone complaining.  Then once we have a feature request, we will know what the goal is.  I don't think we had anything beyond the basic in ARA, and never got a feature request to improve.

Martin: I'll open an issue but not mark it as a CR blocker.

Aram: \#189. [https://github.com/w3c/attribution/issues/289](https://github.com/w3c/attribution/issues/189)

Aram: [\#189](https://github.com/w3c/attribution/issues/189) is marked as ready for review by editors, can we do that now?

Martin: I don't think so — need a way to get responses back? The API surface is missing a key component here.

Charlie: This is first in a sequence of PRs.

Martin: I don't want this when using it has no value (or negative value, since you use up privacy budget and get nothing back for sure).

Charlie: The label "editor-ready" means it's time for someone to work on this PR 🙂 not that the PR is done and ready for merging\!

## AOB

Aram: Talk about CR next steps.  Once this group feels like we are ready, do a consensus call, then Sean & Aram have some paperwork to do, but it's all pretty fast.  Does the group still feel like it's just blocked on clearing the labeled issues?  Any questions?

Charlie: Maybe some of these are not actually CR-blockers, let's do a re-review offline.

Sean: We'll need to take it back to wider review, inc to the CG.

## Future Meetings Information & Logistics

- [ ] TPAC 2025 (November, Kobe Japan)  
- [ ] 09 December 2025 (Virtual)  
- [ ] 2026 Schedule  
    * Do we want a face-to-face meeting e.g. in March or April?  
    * TPAC'26 is October 26-30, shoot for part way in-between
