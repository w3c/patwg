# PAT WG Minutes \- 2026-03 Meeting

## Scribes

*

## Agenda

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

- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
      - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
            - [ ] Will keep open for now \- tracking via [PATCG \#227](https://github.com/patcg/meetings/issues/227)
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com)Develop Web Platform Tests in PPA (repo)
      * Aram: Create issue for people to document their important flows for future test coverage
      * Will keep open for now
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [Issue \#256](https://github.com/w3c/attribution/issues/256) should be reviewed by the Community Group
      * What issue was this intended to refer to — [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)?  We did tag it as a blocker.
      * Revisit at TPAC CG session.

## PPA Discussion Topics

###

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

## [\#386 Epoch Cadences](https://github.com/w3c/attribution/issues/386)

Ben made a doc: [https://docs.google.com/document/d/1OnZ1jn-gaYN52G\_0D\_HhJy\_e6isYxZQ04ZE0VTEuiNk/edit?pli=1\&tab=t.0](https://docs.google.com/document/d/1OnZ1jn-gaYN52G_0D_HhJy_e6isYxZQ04ZE0VTEuiNk/edit?pli=1&tab=t.0)

Ben: This is on the topic of how epochs start and end.  We have a few options.  Option 1, when you introduce safety limits, you could put all budgets on the same cadence.  Besides the obvious problem where everyone synchronizes rollover.
Option 2 is maybe how we fix that: where every device picks its own start time for epochs, but they are all aligned.  Fits Big Bird analysis. But creates a side channel issue, where timing or side channels might leak that unique start time, which is persistent for a given browser.  You might improve the situation, with 2b where you coarsen the values, but that only reduces the granularity of the timer (and reduces the entropy of the identifier).
Option 3: you might separate the cadence.  First attempt was to allow all devices to have the same safety limit cadence, but per-site epochs are random.  But you get overlaps, which is outside of what our theory would support.
Option 4 is skippable.
Option 5 is what Pierre and Michael suggested, for fixing up Option 3\.  Safety limits follow the same cadence for all devices, but per-site epochs are chosen as a set of safety limit epochs.  The safety limits are short (day/hour) and the per-site is a selection of 7 days from those.  SO they align and there is never any overlap.

Michael: Good summary.  The thing that Martin brought up and that I don’t have fully in my head is what is the downside of option 5\.  In particular option 5 takes the safety limits and makes it much shorter.  As small as an hour.  That’s a parameter we can adjust.  What is the downside of that being shorter?  This has ups and downs.  Would like to hear what others have to say about them.  The good part is that an attack, which might be a spike of activity, that is stopped in a shorter time, because the hour-long epoch has a smaller amount of budget that goes with it, so an attack on the budget is contained and they get a lot less information.  They hit the limit much sooner.  Then the budget is restored much sooner.  Downside is that lots of real conversions are more likely to accidentally hit the global lockout.  Because the  longer cadence is that you lose an entire week, but lose the rest of the week.  Now, you lose that.  From the view of protecting privacy, option 5 seems nice.  The downside of cutting off real activity is less.  The threshold is lower.

Martin: For the 1hr example: There are 168 hrs/wk, so you could imagine having the global budget 1/168th as large. If the global budget were 100x the per-site budget, then you'd get the hourly global budget similar to a per-site budget, so a person could burn through it quickly on a single site.  So to get the desired utility, you'd need to make the per-hour budget larger to keep good behavior from being cut off.  Our data on "how people use browsers" is weird — many people use the browser for something like "15 minutes per month", so a 1hr-granularity budget doesn't match their usage.   Maybe a 1-day global epoch is more reasonable.

Ben: The downside probably comes when we try to configure the values for these budgets.  One constraints for the quota is that you can spend the per-site all in one place.  BUt if you slice the global budget, that might be harder to do.

Pierre: If you allocate budget, you leave it on the table when people aren’t able to use it.

Ben: Suggest that we consider day or hour.  Most users are asleep for part of the day.  No point in having budgets while people sleep, so maybe at least we could roll it up into a day.

Michael: I suggested hour, partly because you need to find an extreme.  But also with a day you have to choose a rollover time.  It’s hard to imagine how to preference time zones.  Hourly is nice in that most places agree.  The daily thing: either you reset according to their local clock, which might make sense, because the browser already reveals time zone.  Hard when someone travels.  Maybe that’s not a big deal.  Or everybody resets at midnight GMT.  DIfferent times of day for different parts of the world. Maybe both are OK.  Daily might be acceptable to me.  Then we only save which day the epoch starts for each site.  Some randomness.

Pierre: Even if we don’t want… Option 5 was to maybe prevent spikes, we can have that separate from safety limits.  Might complicate things, but we can add a time quota, loosely configured, which could trim really big spikes.
… Option 5 in terms of DP might not be entirely OK analysis wise.  I believe per-site limits will interfere with safety limits.  A week long per-site will touch multiple epochs for safety limits.  We might not get formal guarantees.  Better than option 3 in that you don’t have DP in either direction, but not fully as safe.

Martin: More that we don’t know, which is still bad.

Martin: Tending toward option 2: spend the effort to hide the epoch boundaries.

Michael: What that means is that every device will carry around a second of the week that is its preferred second.  That might be down the second.  Every week at that time, the budgets reset.  And so, for me in particular, if it is possible for site to tell whether you are over or under budget, then they might be able to find that second.  That is a means to recognize you, which is a property of the device that every site can read.  That’s more like a cookie than a supercookie.  Clearing state is a little bit worse than a cookie.  If it is possible to read it.  I see the downside.  Is it reasonable for us to try to make it so that the site cannot tell when that second is.  Is this something we can try to hide.

Martin: Global state reset is fine, but per-site state can't be reset.  So it all depends on our ability to hide this value.  The whole point of this is that we're preventing leaking information, so it should be feasible to protect.  If we have the budget clearing when the user isn't online, then it makes it again harder to detect at all (though does reduce somewhat the randomness of the choice of epoch time).  Would need to start thinking about this more strategically.

Ben: Main thing to leak is the time that the API call takes.  I’m not sure if that it matters when the browser is active.  Does this change how the implementation loops over epochs.  If different epochs have different numbers of events/impressions, based on when the window is bounded.  That’s the piece that needs more thought.  How to make the API call constant time.

Michael: If we can hide that side channel, such that you can’t observe the rollover, that takes care of it.

Martin: State of the art here isn’t great, but Andrew has done some thinking about this.

ben: Is this a requirement for level 1?

Martin: Can’t imagine a supercookie getting approval anywhere.

Michael: Conclusion: align epochs.  Per-device constant value.  Try to protect that value, primarily by making the algorithm constant time.  Not clear how epoch rollover will affect timing, so it’s not like there is an obvious way the information leaks anyway.

Sean: Action on Ben to note outcome.

###

Pierre: What about coarsening the value?  Option 2b.

Martin: If the value can be protected, maybe we don’t care about this.

Michael: Maybe 2b it still has benefit. The 168 hours might be some value here in terms of having a better narrative about limited device classes.  That might be a better story than having a totally unique value.  Might be more defensible.  Will still need to protect that from leaking.

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

## AOB

## Future Meetings Information & Logistics

[Sean Turner](mailto:sean@sn3rd.com) Confirm with Martin that we are still on track for Berlin in May.
Martin: All looks good so far, will let you know once it's official.

## Review of Action Items From This Meeting

- [ ] Keep old ones
- [ ]

## Participants

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Michael Kleber (Google Chrome)
4. Tara Whalen (W3C)
5. Pierre Tholoniat (Google)
6. Benjamin Case (Meta)
7. Martin Thomson (Mozilla)
8. Séverin Ferrand (Google)
9.
