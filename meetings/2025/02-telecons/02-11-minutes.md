# Private Advertising Technology WG - 2025-02-11

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Brian May (unaffiliated)
4. Arpana Hosabettu (Google Privacy Sandbox)
5. Ben Case (Meta)
6. Andy Leiserson (Mozilla)
7. Martin Thomson (Mozilla)
8. Haley Patoski (Google)
9. Michael Kleber (Google)
10. Aloïs Bissuel (Criteo)
11. Charlie Harrison (Google)
12. Thomas Prieur (Criteo)

## Agenda

### Hellos, Intros, Policies, & Call for Scribes

Scribes: Ben Case & Martin Thomson

Sean: Welcome. No new people this time for doing intros.

See the Read All About It slides.

## Review of Action Items

Sean: The following consensus calls all completed:

- [x] ~~Consensus call: gDoc \-\> recordings/transcripts \-\> markdown~~
- [x] ~~Consensus call: Github Centric \+ Slack (logistics)~~
- [x] ~~Consensus call: Adopt PATWG charter repo~~  
      - [ ] Move to W3C  
- [x] ~~Consensus call: Adopt PPA-API as FPWD~~  
      - [x] ~~Auto-Publish included~~  
      - [ ] *Process Explainer: Issue \-\> PR \-\> Merge*  
- [x] ~~Establish: once per month WG meeting, 2 hours long, rotating times zone~~

Sean: WRT minutes, we decided to drop the idea of using recordings. While potentially useful, it is not clear we will actually use them.

Sean: WRT adopting & moving the PATWG charter repo, the majority of other WG charters are located in [GitHub W3C's repo](https://github.com/w3c) and to not break with precedent instead of moving the PATWG charter's repo to the PAT repo we will instead leave it in the W3C's repo.

Aram: I will complete moving the repo over; see [Action Item List](#action-item-ist).

Sean: WRT adopting PPA-API repo, we still need to do generate the process explainer. And, right now it is [here](https://github.com/w3c/ppa) and we will move it under [PAT repo](https://github.com/w3c/patwg).

Charlie: can we go over what the process explainer is? Since more recent activity on PPA-API?

Sean: we will leave all editorial PRs for the editors to just merge.  If things are not controversial, create an issue and do the discussion in in the issue. Once settled, create a PR (use GitHub linking [mechaism](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue).  Don’t need to use the consensus tool every time if not controversial. If something is controversial, then can do a consensus call.

Charlie: should we create a label for “needs consensus”?

Martin: yes, have a “needs discussion” tag for more high touch things. But we can have a separate label for consensus call.

Aram: we can have a separate label for consensus call.

Charlie: those with merge rights on repo rather ad hoc; is it meant to be only editors?   

Sean: right now a bit ad hoc and may change with the repo changes. Will be chairs for sure.  We can see blame so we can be sure nothing nefarious happening.

Martin: propose we make Charlie an editor of the spec.

Charlie: can we also add Andrew; he is doing lots of heavy lifting.

Martin: anyone should be able to make a PR. Members in read only at least; need them to able to review.

Michael: triage access is ability to add labels to issues.

Aram: so with write access we now have (chairs \+ Tara, Martin, Charlie, Andrew, Andy) and everyone else with triage access.   

Martin: already added the label we were discussing of “needs consensus”.

Aram: maybe didn’t save; will make sure it is added. See [Action Item List](#action-item-ist).

### PPA Discussion Topics

#### [Partial Conversion Values](https://github.com/w3c/ppa/issues/16)

Martin: if you perform attribution have value to contribute of 10; but you also specified 10 budget; but there is only 5 budget left. What happens? Do we say you have no budget left so you get nothing; or do we reduce how much of a value to give you back. Discussing on issue with Charlie and we think it is quite complex to do and easiest is to just say if you don’t have budget then you loose the impressions in that epoch. One nice thing is that for queries that are trying to measure bias of being out of budget, then this will be easier. Count you got to the threshold. Proposal here is to go with the simpler approach, which is also the approach that requires us doing little to update the spec.

Charlie: PR up on privacy budgeting does nothing for this.

Brian: real world example?

Martin: if you’re running a campaign and you’ve already been querying daily calibrations; now at end of the week you want to get as much info as you can; if someone had an impression every day they have spent all their budget and can’t participate in the end of week reporting query. You could take the little bit they have left and scale down their contribution. Or you could just drop them from the query.  None comes into effect if you don’t overspend your budget and that will be public.

Brian: i would think most people would be conservative.

Charlie: there will be a bias/variance tradeoff that will need to be evaluated on a case by case basis. If you spend only a little on each event, can do without bias but will have higher variance. If you spend more per event, will have less variance but some more bias.

Martin: nice that is configurable by whoever is querying; all up to the party making the query.

Brain: potential abuse vector?

Martin: no, only will be you who are setting this up for yourself.

Arpana: I think with this issue I like the example of how layed out; I think I agree with punting as with multi touch it will be tricky to do if doing partial conversion values; so maybe we can figure out at that time.  Wanted to say that this example is nice and see if we should have these scenarios collected somewhere so we can see these more tricky cases.

Aram: we could create a doc of tricky examples; could go in patwg repo; we can create a folder in there for it and people can add to it with prs; see [Action Item List](#action-item-ist).

Michael: wondering if an API user takes the over spend approach can they know?

Martin: Roxana working on a design for this; not ready to share yet but have a design working on that counts how many have crossed over.

Michael: great.

Charlie: There is some discussion about this in the CookieMonster paper  

Ben: bias measurement queries.   

Charlie: to Arpana’s suggestion we talked about the spec being also an explainer so could consider if we keep it in the spec as an explainer. Some stubs for explaining at a high level and could go there.   

Aram: could start it in the separate doc and move over when we think something is ready.  Need to determine if this doc is an actually w3c publication.

Martin: we have lots of examples now to work through with.   

Aram: on this issue; no disagreement so we can close the issue. We can record the consensus decision here. 

#### [Inconsistent IDL naming: aggregationServices vs aggregators](https://github.com/w3c/ppa/issues/66)

Martin: doc uses multiple terms for aggregation service/ aggregator – do we leave it to the editors to decide?

Brian: do these have different meaning?

Aram: nice to say the full aggregation service.

Arpana: nice to be able to say something compatible with TEE service.

Martin: we decide to go with aggregation service.

#### [Avoid using floating point arithmetic for privacy budget](https://github.com/w3c/ppa/issues/77)

Martin: suppose you split your budget into 11 portions; then when you add up all 11 portions you get something with round off error and the last time you try to spend you will not have enough budget; only get 10/11 of your budget spent. 

Charlie: options we’ve discussed include formula for budget deduction: epsilon, individual sensitivity, max sensitivity: do epsilon individual sensitive / max sensitivity. So even if epsilon integer this division by max sensitivity will hurt you and put you back in place of rational numbers approx by floating point and you might be able to get a case where you are out of budget on a last query.  We could do some clever things with rounding to give you slack at the end; we could do smarter rounding to give more error per query but avoid these catastrophic effects. These two approaches when doing rational. ARA does just one big integer and you don’t specify epsilon and individual sensitivity; this means you can’t have different reports with different max sensitivities.  Reason this approach isn’t necessarily better is that it is better for Laplace mechanism but that isn’t necessarily what we want to use here. Think we will need to either adding some slack or playing with rounding so we can keep in floating point.

Martin: clever rounding probably almost the same as giving you slack; easier approach is to add the a slack term at the end. Would be giving someone budget of like epsilon \+ 10^{-10}.

Charlie: if you have a min budget spend, you can upper bound the error.

Brian: you have some number of queries till you’re exhausted – does it matter how you got there at some point you have a certain number of times to query till you no longer get results.

Martin: that is what we are trying to guarantee; that you can query a precise number of times and never run into out of budget; don’t want it to happen b/c of floating point errors.

Charlie: that number can be different; but want to make it possible.

Aram: The simpler it looks, the easier for adoption. Lots of math detail we can work through but the wider space won’t want to work through the nuance.   

Aram: have we come to a decision here? For now agree to a minimum budget spend and keep the floating point calculations as is and add a slack term; seems what we are largely converging on.  We could revisit but I think that is going to be good enough for now.   

Brian: sounds like in either approach you get the same number of whole queries.   

Charlie; yes, it is just a difference of do we give you a tiny bit of extra budget or make it so you can’t use a tiny slice; we are suggesting going with give you a little bit extra. 

Aram: finished planned discussion; anything else Martin? 

Thomas: on floating point, do we plan to document the rationale? 

Martin: the issue will track the decision making; and outcomes in the spec.

Charlie: for some things, like this extra slack term, we can include some explanation of why it is there.  Can also link to closed issues in the spec notes.

Sean: working model is have the issue; create a PR to close it and then we can link to if we need from spec.

### [Initial detail of HTTP API](https://github.com/w3c/ppa/pull/56)

Martin: focused on getting core spec correct. Then will go on to things like the HTTP aspects. [PR \#56](https://github.com/w3c/ppa/pull/56) on types could be worth having discussion on; committing to use a certain type.

Charlie: another topic to add on overall spec structure; comment in the budgeting PR. For [PR \#56](https://github.com/w3c/ppa/pull/56), I need to review before discussing. Some limitations with structured headers need to review. If we go with structured headers, take advantage of all they offer.

Andy: we don’t have Andrew and he probably knows most about structured headers. The filter data is where the biggest issue is. Question: can we do something to enable forward compatibility. Or just say filter data is an integer and if we do something else later we call it something else.

Charlie: think at TPAC discussed uses with more than integer filter data.

Martin: two questions of i) how to implement that and ii) what actual functionality would be enabled by more than integer filter data.

Arpana: from extensibility, keep open if we use structured header now.

Martin: can do that but still may be hard for compatibility if we need to change an attribute.

Aram: trend in w3c to do structured fields with more constraints.

Martin: when we start getting to HTTP, we would like to maintain parity with the JS but maybe there ends up being some difference.

Martin: summary: this needs a bit more work as the PR is made. For some of this more boarding stuff may setup a call for just the editors to get through these editorial things and keep momentum.

Aram: we have [a slack channel](https://w3ccommunity.slack.com/archives/C042939UU11) for editor work on the spec.

## Next Meeting

Sean: fill out form for April meeting; Criteo nicely offered. April/May/June would be the ones we’d consider. 

## Action Item List

This list is updated with new action items. Aram established the Admins for Repo during the meeting.

- [x] ~~Consensus call: Adopt PATWG charter repo~~  
      - [ ] Move to W3C (Aram)  
- [x] ~~Consensus call: Adopt PPA-API as FPWD~~  
      - [x] ~~Auto-Publish included~~  
      - [ ] *Process Explainer: Issue \-\> PR \-\> Merge (Sean)*  
      - [ ] Land PPA-API in right place (Tara)  
      - [x] ~~Establish Admins for Repo (Aram)~~  
      - [ ] Add label for "needs consensus" (Aram)
- [ ] Create a folder for examples. People can add to it with PRs (Aram)  
- [ ] Update meeting template to include a standing item for PPA related topics (Sean)
