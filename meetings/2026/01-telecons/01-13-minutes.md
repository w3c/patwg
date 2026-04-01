# PAT WG Minutes \- 2026-01 Meeting

## Scribes

* Ben Case
*

# Proposed Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes
2. Review of Action Items from Previous Meeting(s)
3. [DAP Integration](https://github.com/w3c/patwg/issues/76) \- @martinthomson
4. PPA Discussion Topics
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)
5. AOB
6. Future Meetings Information & Logistics
7. Review of Action Items From This Meeting

## Introductions, Code of Conduct, Minutes Document, Scribes

Scribe:  Ben

Sean: Intro, policies and procedures
Sean: agenda
Sean: review action items
Sean: should we keep this open? [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)  We still want some demonstration numbers.
Aram: has had CR blocker removed.
Charlie: we got good feedback from Watson; we’d like some feedback from a potential implementer/ad tech user give some feedback who plans to use the API.
Also the web tests item we will keep open.

## Review of Action Items {#review-of-action-items}

- [ ] Aram [**MOVE TO CG**](https://github.com/patcg/meetings/issues/227): Create a repo for use cases or workflows that demonstrates how the API actually services those cases.  We should provide a template for on-going use cases.
	  - [ ] Demonstration HTML (a cookbook) with code for both the impression and conversion side.  Ie: full, correct transactions (illustrating how to confirm the transactions were successful; expectation of outcomes) (Example \-\[ Live URL Viewer\](https://jsdom.github.io/whatwg-url/\#url=aHR0cHM6Ly9leGFtcGxlLmNvbS8=\&base=YWJvdXQ6Ymxhbms=))
			- [ ] Will keep open for now \- tracking via [PATCG \#227](https://github.com/patcg/meetings/issues/227)
- [ ] Develop Web Platform Tests in PPA (repo)
	  * Aram: Create issue for people to document their important flows for future test coverage
	  * Will keep open for now
- [ ] [Aram Zucker-Scharff](mailto:aram.zucker-scharff@washpost.com) [Issue \#256](https://github.com/w3c/attribution/issues/256) should be reviewed by the Community Group
	  * What issue was this intended to refer to — [https://github.com/w3c/attribution/issues/256](https://github.com/w3c/attribution/issues/256)?  We did tag it as a blocker.
	  * [\[For PPM\] PPA queries in DAP](https://docs.google.com/document/d/1-BOnlUus2LtFk-Oesauijrb5PFaUeyBqVwhTfeEIFcQ/edit?tab=t.0#heading=h.q0k70yp654td) — what we should test for, maybe this is what we're looking for feedback on? This should be easy.
	  * We will try to bring it up for discussion in the CG meeting in two days.
	  * Was discussed at CG.
	  * Revisit at TPAC CG session.
	  * More feedback from potential implementers needed // Jan 2026
	  * Martin: the Criteo presentation included some parameter feedback on what is important.
	  * Charlie: if they had feedback on histogram size, we should add to this issue.

## [DAP Integration](https://github.com/w3c/patwg/issues/76) \- @martinthomson

[DAP for Attribution](https://docs.google.com/presentation/d/1LvX9eDMDU4rL9ygqJzKeWLDBGc-2-a95qMySgOLp90o/edit?slide=id.g3b295de53ae_0_86#slide=id.g3b295de53ae_0_86)

Martin:

* Aggregation service requirements of aggregating a histogram and adding DP noise with anti-replay
* Similar architecture to DAP; a collector collects a batch and then submits to the aggregation service which adds noise.
* Some inconsistencies with the current DAP architecture.
* DAP clients produce a constant stream of reports sent to the leader.  In DAP the clients directly submit to the leader which we don’t want to do here.  Don’t want the leader highly available and want the collector to be able to filter their reports before deciding what to aggregate.  In DAP a “task” covers the stream of tasks and a “collection job” selects a slice of reports for aggregation (by time or by total).
* Current mapping of PPA to DAP
  * Website creates the task and client doesn’t see it
  * All reports are submitted in that task and all the task is aggregated at once
  * Not how DAP would normally work
* Charlie:
  * Q: The fact that the browsers use a dummy mapping, would the conversion site overwrite it.
  * Martin: Yes, the original idea was the task id meant it was going to be rewritten later.
  * Charlie: Can this impact privacy?
  * Martin: In the current conception, the privacy parameters would be bound to the report.
  * Q: The task to batch ratio doesn’t have to be fixed? The collector could cause multiple queries.
  * Martin: DAP folks are concerned about not binding everything into the task id as they do in their security model.
  * Charlie: Prevents hot swapping the protocol somehow?
  * Martin: DAP supports batch uploads for optimizations.
  * Charlie: If you want a daily query, you can batch upload hourly with the same task and wait to get the result till the end of the day.
  * Martin: That  is possible in current mapping.
  * Brian: Why is there a dummy task?
  * Martin: Don’t want the site to have to know everything upfront and figuring out task ids upfront makes it more operationally difficult.  Mozilla found a similar deployment difficult because of this.  You want to bind the reports to the aggregation jobs as late in the process as possible.
  * Martin: the dummy task id means it can be replaced later. The DAP folks we’re happy with it though
  * Erik: if you had planned to run daily but decided later to combine across days, it would be necessary to not have them on the same taskid
* Slide: a better fit for DAP
  * Martin: switch to taskprov. Task id is generated based on aggregation parameters. Deterministic process.
  * Can be longer lived covering everything with a fixed set of characteristics. Things you can’t change past creating the report, like histogram size
  * Brian: helpers can derive? To validate?
  * Martin: yes if you know the fixed parameters for the report, the Helpers can also create the task id to match it to the report in validation. Works with anti-replay. Then batches are what define the aggregation set of reports and the collector defines this.  We would need to define a new batch mode in DAP to allow the sites to do this.
  * Having the task id fixed is nice for anti-replay; DAP folks happier with it
* Slide: DAP Taskprov
  * Martin: hash all the parameters
  * Parameter table DAP has but not necessarily fixed
  * What we do at client and aggregation time needs to be consistent – need to be able to communicate to the helpers
  * DP parameters
  * Charlie: right now we have AEAD – this should be exhaustive, should that just move to the taskid?
  * Martin: yes, we can just move to the taskprov; should capture everything we need in the task id
  * Aram: sites need to provide correct values?
  * Martin: we’re working out what the sites need to provide, not the task id itself but parameters that will go into it.
* Slide: item: DP
  * Three places you could put the DP budget
	* 1\. In the task configuration – all reports would need exactly the same DP budget requested to be aggregated together.  If you change the value you get a new task
	  * Advantage is smaller reports
	* 2\. Report include the budget value (like AEAD); task includes a flag DP is necessary
	  * Could have a flag for DP needed or maybe don’t need that
	* 3\. Only in the report
  * Identity of conversion site
	* Need to keep in separate aggregations
	* Can bind to reports or put in the task
	* Recommendation: bind to the task and save space. Different site means different task
	* Erik: Does that imply for the impression site?
	  * Martin: only the conversion site.
	* Charlie: if conversion sites are not colluding, a
	* Ben Case: Necessary to bind it to whatever site spend the budget.
	* Charlie: That’s all pre-DAP.  It’s important toSh have conversion site bound to the report.
	* Ben: You are right.  For level 2, it’s the site that spends budget. It could be the intermediary site in this field.
	* Pierre: can call “query site”
	* Charlie: concatenate histograms of already aggregated ones; same as putting into the same bin with increased sensitivity – same degradation as colluding conversion sites
	* Pierre: but you don’t get guarantees in isolation across queriers
	* Martin: lets take offline
* Slide: suggested taskprov items
  * Task info
  * Min\_batch\_size – let’s pick something small and fixed
  * Batch mode – we’ll need the the new one
  * start/end time – we can probably remove this
  * VDAF – L1 bounded sum
  * VDAF config – TBD
	* Have talked about the maxValue – they don’t just need a
  * Ben: maxValue is included in the vadf\_config?
  * Martin: yes
  * Dp\_budget – include in the task or do it on the report
	* Choice here.
	* Martin: leaning toward on the report not the task
  * Charlie: Is there a philosophy for what goes in the task vs report? Why not maxValue in the report?
  * Martin: maxValue MUST be the same across reports in an aggregation because of the MPC aggregation needing it all the same.
  * Charlie: could look for more flexibility of the same in a batch but not a task
  * Then there is the option of the same configuration but there should be simply a different task for it. Separation of the reports. For us it would be max\_value and histogram size. Makes sense to use the task\_info for the caller to split different reports into different tasks
  *  Ben – could they separate out? Not if anti-replay based on task\_id
  * Martin: that’s right, maybe worth letting them partition but not high priority
  * Erik: lets you do anti-replay with timestamp that roles forward for a partition
  * Shan \- question: when you say each report have different DP budget, do you mean they should be aggregated in different batches with different central DP applied?
  * Martin: yes if a report has epsilon \= 2, then it should be aggregated with central DP epsilon \= 2
	* Raises the question of what to do if one report has smaller epsilon
  * Shan: can explain the min\_batch\_size   – in DAP if you were using shuffle based local DP to get a central guarantee then you need a min batch size
* Slide: batch mode: report submission
  * Martin: add collection job in the report submission id. When sending to helpers
  * Martin: what to do if different DP noise on reports? If in task it is inflexible. Can’t combined conservatively across reports.
	* Doesn’t extend easily to partial spend if it is in the task
  * Erik: you could at conversion time, split into adaptive queries even if we didn’t support it at helpers.  Adaptive would change the DP analysis?
  * Ben: theory isn’t all worked out fully for this, but you should be able to support multiple requerying a report with partial spend without needing something different on the device
  * Martin:  if dp on report, we can filter out reports with too low DP budget
  * Brian: do we want the option to cancel a job if too many being dropped out?
  * Martin: that is a possibility that could be worked out
  * Ben: in case with some reports w/ too low. Flip side (dp on the report), some with 2, a few with 3, could aggregate them all together.
  * Charlie: we shouldn’t get too crazy on flexibility as most of these are “quality of life” features.
  * Martin: yes it can work but would create a footgun
  * Erik: nice to avoid a footgun if we can.
  * Erik: do we need a (lower case) delta?
  * Martin:  prefer a fixed one
  * Charlie: CookieMonster can support approx DP but suggest not making it a two parameter choice.
  * Shan \- you can define a DP config, DAP allows different type of DP, not just pure DP, it’s not prescribed by DAP, but there was plans to extend DAP or taskprov for that.
	* Can define different noise mechanisms, etc.  in certain settings delta is important.
  * Charlie; things like L1bounded sum like pure DP
  * Erik: suggest we don’t anchor here too tightly on pure DP and laplace, then
  * Charlie: should be able to do other DP
  * Ben: make a more general DP parameter string that lets you request the client a certain helper DP noise method and bind to the report
  * Pierre: should be able to make with zdp filters
  * Erik: do we want sum of two gaussians?
  * Ben: sum of two laplace
  * Charlie; do you want the guarantee of two helpers if not colluding or are colluding
  * Martin: discrete laplace by both is how a current deployment works
  * Charlie: did some analysis on laplace
  * Martin: we don’t need to solve that now; what we need to to make sure we have flexibility in the future
* Charle: do we need anything added to DAP to support anti-replay?
  * Martin: DAP does have some time interval based anti-replay optimizations like we looked at
  * Martin: as long as the guarantee is there from DAP they can work on improving that implementation
  * Charlie: but the client needs to include the right things
  * Martin: they have the things from the client, high entropy report id and a timestamp; that is what you need for anti-replay system
  * Charlie: but we’ll want to do on coarser buckets
  * Martin: not necessarily
  * Charlie: if you want to use a bloomfilter
  * Charlie: if we change this we’ll need changes to the API.
  * Martin: you need something that is coordinated through the use of the API
  * Charlie: if there is a hard rule for anti-replay; want to make flexible for client
  * Charlie: we’ve been putting things in the AEAD that you’d want to use for anti-replay.
  * Ben: thought that task\_info would be enough
  * Martin: not enough

	(Martin: here is what I wrote up: [https://martinthomson.github.io/dap-dp-ext/draft-thomson-ppm-dap-dp-ext.html\#name-report-partition](https://martinthomson.github.io/dap-dp-ext/draft-thomson-ppm-dap-dp-ext.html#name-report-partition))

Open issues

1.  [https://github.com/w3c/attribution/issues/127](https://github.com/w3c/attribution/issues/127)
   1. Martin: What to do in case of third-party cookies? Nothing we can really do so do nothing.
2.  [https://github.com/w3c/attribution/issues/174](https://github.com/w3c/attribution/issues/127)
   1. Martin: Number of sites to min have as conversionSite that saveImpression can call
   2. How many ways you can split across impression sites
   3. Max lookback days – 90 days
   4. Supports long attribution windows; some folks
   5. Martin: interested ini what Apple would think as they loose cookie state
   6. Erik: not sure about the browser; know there are some long lookbacks like 1 yr but might need to consider other privacy budget for that or something
   7. Michael: 90days pretty good middle ground but some people won’t be happy with how long it is
   8. Aram: 90 pretty generous
   9. Michael: but certain verticals pretty important to have longer.
   10. maxHistogramSize
	   1. No implementation limit but let technology decide.
	   2. Brian: a storage cost
	   3. Martin: no storage cost
   11. Erik: do we expect these to be the same across browser/clients
   12. Martin: this is a minimum we’re trying to put in the spec. Other reasons a browser might jettison state
   13. Erik: like private browsing session ends
   14. Martin: other ones we could add like number of impressions allowed stored for a site
   15. Erik: will have to start throwing out old stuff.
   16. Martin: larger histogram better
   17. Erik: larger histogram gets paid in aggregation; may add client side
   18. Martin: on client, we don’t limit resources a site can use on client generally
   19. Brian: seem like reasonable arbitrary numbers

Sean:

* Meeting schedule
* May meeting probably in person in Berlin
  * Martin will add office location details when available


## PPA Discussion Topics

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

## AOB

PAT WG Repo Issue clean-up:

* Add “Pinned: “ to beginning of Issue [\#2](https://github.com/w3c/patwg/issues/2), [\#3](https://github.com/w3c/patwg/issues/3), [\#23](https://github.com/w3c/patwg/issues/23)

## Future Meetings Information & Logistics

Sean: 2026 Schedule is set; look [here](https://github.com/w3c/patwg/issues/73). Going to make it official after this meeting as nobody has complained..

## Review of Action Items From This Meeting

- [ ] Keep existing AIs from [Review of Action Items](#review-of-action-items)
- [ ] Assign AOB WG Repo clean-up to [Sean Turner](mailto:sean@sn3rd.com)

## Participants

1. Sean Turner (sn3rd)
2. Aram Zucker-Scharff (The Washington Post)
3. Andrew Paseltiner (Google Chrome)
4. Charlie Harrison (Google Chrome)
5. Brian Mau (unaffiliated)
6. Martin Thomson (Mozilla)
7. Michael Kleber (Google Chrome)
8. Mark Blunk (Apple)
9. Tara Whalen (W3C)
10. Shan Wang (Apple)
11. Alex Turner (Google Chrome)
12. Ben Case (Meta)
13. Erik Taubeneck (Apple)
14. Pierre Tholoniat (Google)
15.
