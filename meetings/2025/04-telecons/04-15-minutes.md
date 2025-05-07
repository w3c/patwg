# Private Advertising Technology WG - 2025-04-15

## Attendees

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Paul deGrandis (Kevel)
4. Ben Case (Meta)
5. Taiwo Idowu (Chrome)
6. Charlie Harrison (Google Chrome)
7. Erik Taubeneck (Apple)
8. Tara Whalen (W3C)
9. Roxana Geambasu (Columbia U)
10. Miguel Morales (IAB TechLab)
11. John Wilander (Apple WebKit)
12. Andrew Paseltiner (Google Chrome)
13. Garrett Johnson (Boston University)
14. Joey Knightbrook (Snap)
15. Andy Leiserson (Mozilla)
16. Phillipp Schoppmann (Google)
17. Mark Blunk (Apple)

## Agenda

### Hellos, Intros, Policies, & Call for Scribes

Scribes: Paul deGrandis & Aram Zucker-Scharff

Policies: See WG Read All About It Slides.

### Review of Action Items

- [x] ~~Fill out [form](https://forms.gle/LSs5xoL5NzFwneAA9) for June meeting (Everybody)~~  
- [ ] Create a PPA folder for examples. People can add to it with PRs (Aram)  
   - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
   - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \- [Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (group)  
  - [ ] Create issue for people to document their important flows for future test coverage (Aram)  
- [x] ~~Weekly summary of ppa repo (Tara)~~

Sean: We gave a two month notice for the f2f in Paris, please fill out the form (link above)  
If you’re a maybe, fill out the form.

Aram: I didn’t get to the example setup (the goal was to create it in the PPA spec repo). I’ll take care of that. I have in-progress examples which will be completed in a month.

Sean: Tara set up the weekly reminder of activity in the PPA repo.

### PPA Discussion Topics

#### [Use of Intermediary Site](https://github.com/w3c/patwg/issues/38)

See [slides](https://docs.google.com/presentation/d/19JfHNrdqYVkWNqlPFf0N5qyhnzRrqO231C0PPhX7nJE/edit?usp=sharing).

Erik:

As I was looking through the issues on the PPA spec and began investigating what we do on the intermediary (on the given events), but nothing on the algo that addresses that.

Motivation: Why do we need an intermediary? At impression time, you may not know all sites where the impression was on. We need to be able to set the intermediary (and match on that).

Can we just say that the site is present or the intermediary is present and be done? It gets gnarly.

This presentation presents the full truth table of how the matching algo should work and where the ambiguity exists. Do we have any reactions?

Charlie: One thing we should try to capture is what flexibility do we need on site-matching and what flexibility we can pass on to filtering? It’s worth an exercise to determine what strategy should be taken across different kinds of filtering need to be done (and where the extension point should be) instead of N different ways and how it all gets integrated into the matching algo (there’s more than just site filtering).

Roxana: There should be some kind of access control to ensure privacy loss flow.

Erik: The browser sets the actual controls for access/visibilit/y, but what is considered valid to match on can be filtered. (See the slides for an example where the ambiguity exists, given a difference in how impression vs. conversion get filtered/matched).

Miguel: What is the intermediary site?

Erik: Some iframe that calls the PPA API under the hood, embedded in the actual site.

Miguel: What sites or intermediaries can be configured?

Erik:  All use-cases are valid and any collection of sites/intermediaries could be listed/filtered.

Charlie: ARA doesn’t really support this and maybe we don’t need this in the spec.

Erik: (walking through additional examples \- conversion matching): There are open questions about the conversion, since we don’t currently allow certain specifications. There exists a large trade-off, e.g., should the impressions be able to set what intermediaries are valid?  Are there opinions?

Ben: Some of the questions seemed like they could be phrased as “What should the defaults be?”

Erik: When the things aren’t over-specified, it’s unclear how to assess/evaluate priority, but I agree we also need to have a conversation about what the defaults should be. I could see an issue where intermediaries could cause exclusions on each other, and how this plays into the spec. I think we need to evaluate how we want to enable.

Aram: Currently, the world works “all intermediaries can measure on my site”, but I think turning on/off intermediaries makes sense and would be desired. Do we also need the intermediaries to control budget and I’m sure some will disagree: the TLD site should have the ability to control which are valid intermediaries to measure on my site (thinking of my own use case). If there is an advertiser that wants to use a specific intermediary, that’s a contractual issue not a technical one that we should solve within the spec. (Paul agrees with Aram’s perspective).

Charlie:  When we’re having these conversations, it’s good for us to consider the trade-off between expressivity vs the security (and the scope of the measure). Separate expressivity vs. security discussion? Perhaps we should have a separate conversation about this particular trade-off. Eg: What do we want to limit and what don’t we want to limit?

Miguel:  Why is it necessary for an advertiser to configure this on the front-end? (given how it runs in the current world).  Is this more like a security thing?  A privacy budget thing?  Usually they apply the filtering \_after\_ they get the data.

Erik: We don’t have those joins (attribution) off of the device, so we need some mechanism to determine the matching and forward the data for aggregation. The conversation is around what the rules ought to be for this filtering.

Erik: (one more slide, [issue 138](https://github.com/w3c/ppa/issues/138)) Should the default be an empty list (“match everything”)? This gets to Charlie’s point of expressivity vs security. “Match everything” seems very dangerous and a security hole waiting to be exploited (e.g.,: using this aspect to drain budget).

Charlie: Thinking through the business-case which of these are security-focused actors and which are just simple intermediaries? For example, based on the use-cases, maybe we give intermediaries special treatment (and maybe there aren’t a lot of intermediaries so listing them out won’t be a burden).

Aram: I agree this concept of “match everything” as a default is very undesirable. We don’t want to reproduce the situation where the default state is a million measuring intermediaries. I could see issues arising from too many intermediaries being caused at once.

Ben: On the side should we specify conversion-side intermediaries on the impression? But this limits how certain parties can participate.  (Provided some examples of how intermediaries could be preferred but defaults could further control).

Garrett: Could it be useful to use something like sellers.json to be able to look up intermediaries used by the advertisers?

Charlie: This is something that IPA had to check against. If there is a very strong use case for it then it might make sense. But there is an adoption hurdle that could make the system harder to use. You’d have to figure out who you are working with and collect them all ina file somewhere. We need to find out what the use cases are before we make it a blocker.

Miguel: Buy side vs sell side intermediaries have some different concerns.

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

#### [https://github.com/w3c/ppa/issues/24](https://github.com/w3c/ppa/issues/24) & [https://github.com/w3c/ppa/issues/132](https://github.com/w3c/ppa/issues/132) 

Charlie: Let’s talk about both at once since they both deal with the functionality of filterData.

We ran into this issue in ARA where we only supported simple operations but had to add more and more to support and then it became an unruly mess and still doesn’t fully specify boolean logic. Do we want something robust from the beginning or do we feel confident that the simple thing we do will be good enough initially and we can add on as another parameter later on.

Paul: What prompted you during ARA to add more to it? Real use cases?

Charlie: Yes.

Paul: We should prob get out in front of it right?

Charlie: At the same time we decided that this level one API should be simple and not do anything too crazy. I can see the benefit from both sides. Do we want to pull off the 80/20 and design something crazier later? I think that’s prob fine and that’s why we haven’t been pushing to make it crazy expressible to match ARA. We could go back to the drawing board if needed.

Erik: Suppose we did allow this set, would there be a limit to the size of it? Is there weirdness to the edges around that limit?

Charlie: Side channels and system health concerns on users. Kind of similar to the way histograms are formed under PAM to allow for ad ids. You don’t want to have to specify a ton for good performance. But there may be use cases for if you need to specify a million of these things? For the issue, measure conversion would be the thing that specifies the list in this I think?

Erik: You might have a campaign for a full brand of shoe and on the conversion line say it is eligible for a specific item or the whole brand. Is that not what’s going on here.

Andrew Paseltiner: You can imagine both multiple filter values on the impression side and multiple filters on the conversion side.

Charlie: That gets it closer to what we have in the ARA. It gets very complicated. Might be worth an exercise to reason through this so we don’t just tack on features as we can.

Paul: That pushes me over the line to do the whole thing, people will want to do halo attribution-like things.

Charlie: Maybe we take this as a sub item to further discuss then. In theory, we have something that is worklet based where there is trusted on device calculation that comes up with a filtered list based on code instead of half baked propositional logic. Martin was fairly opposed to this because of side channel attacks, but I think it is an option for us to consider if we can mitigate side channel attacks. That might be a nice way to bypass these issues. Issue is [https://github.com/w3c/ppa/issues/58](https://github.com/w3c/ppa/issues/58). He was suggesting some limited versions. Specifying that you need to use some limited algebra also not great.

Aram: Good to bring to CG. Want to think through use cases with people who will have to implement.

Erik: Is there an alternative where this could be solved, thinking of the simple version, by calling the API repeatedly? We only have one to one matching but you register different impressions?

Charlie: Possible, but a performance tradeoff, especially with a Prio backend. There might be some linear scaling with the processing costs. It depends on how large it is. 2 is prob fine, but at 10 it starts getting messy. 

Erik: That makes sense so every time you call the conversion it scales up in terms of processing costs, but on the impression side that won’t match. Just thinking through what we want to present in this case.

Sean: We want to figure out where we want it to land and we are thinking about bringing it to the community group. Who is going to take that action item?

Erik: What we really want to know is what are the use cases and then we can figure out what the costs are. When we talk to them what we want to get is not a request for features but use cases.

Sean: Like the idea of simpler version.

Charlie: We discuss that it isn’t a big deal to add another param. We could try to nip it in the bud the one time after we launch the simple version and then we deal about it later. If we can communicate it in the right way we can get good feedback. I’m not sure exactly how to best express the question.

Sean: Ok, we’ll have to consider it more. 

#### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

John Wilander: General question for the three engines, do we have a feel to when we are thinking about implementing? It would be good to know how to schedule engineers.

Andy Leiserson: Not sure.

John: We have 3 major releases per year, if you look back it is November-ish, March-ish, Sept-ish. Can’t predict the future for sure but that is a good indicator. Iterative process for these releases. It would be a little nice to be coordinated here so the ad ecosystem can try it out. If I’m going to plan to do this in a year I need to get started.

Charlie: On the Chrome side, we’re investigating and improving until we are in a place where we feel we can make a decision. We follow the Blink process, with intent to prototype, the signal we’re going to start committing resources is intent to prototype as part of the Blink process.

John: Let’s make this a topic for a future meeting. It will help us on converging on something, to limit the amount of stuff we can do on level one. It can help us target what we want to ship.

Benjamin: Makes sense we can check in.

John: Some ad tech players will likely start to engage if they know it is coming. The sooner we hear from them and course correct if needed that will help. Google Ads coming to these meetings with feedback would be great.

Miguel: IAB Tech Lab is doing some of that work already to look at the spec proactively and talk to the use cases and gaps in the spec or proposals to give feedback in the next few months.

John: Nice, and this one has the potential to be interoperable.

Miguel:

It is difficult unless there are dollars on the line to get people to be proactive.

One of our concerns for example is a lot of buyers have complex impression models and they are used to it and there are concerns about how to replicate that in the new model, just as an example (referring to [https://github.com/w3c/ppa/issues/42](https://github.com/w3c/ppa/issues/42) as an issue that has come up in the IAB Tech Lab).

Maybe a scripting language can be used to attach the conversion model? Difficult to wait for browsers to update to include the mode they are talking about.

Charlie: This is related to the worklets conversation. The IAB Tech Lab knowing that they need a very expressive thing and giving that feedback is valuable. Please start giving us more about these issues.

### AOB

None.

### Future Meetings Information & Logistics

May \- Online  
June \- F2F \- Fill out [form](https://forms.gle/LSs5xoL5NzFwneAA9)\!

## Review of Action Items From This Meeting

- [ ] Create a PPA folder for examples. People can add to it with PRs (Aram)  
  - [ ] Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.  
  - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \- [Live URL Viewer](https://jsdom.github.io/whatwg-url/#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=&base=YWJvdXQ6Ymxhbms=))  
- [ ] Develop Web Platform Tests in PPA (group)  
  - [ ] Create issue for people to document their important flows for future test coverage (Aram)
- [ ] File an issue to talk about flexibility of filtering and impression logic based on today’s conversation
