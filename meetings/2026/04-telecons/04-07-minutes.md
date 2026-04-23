# Private Advertising Technolgoy WG \- 2026-04

## Attendees

1. Sean Turner (sn3rd)  
2. Aram Zucker-Scharff (The Washington Post)  
3. Tara Whalen (W3C)   
4. Andrew Paseltiner (Google Chrome)  
5. Benjamin Case (Meta)   
6. Pierre Tholoniat (Google)  
7. Alexander Turner (Google Chrome)  

## Agenda

1. Introductions, Code of Conduct, Minutes Document, Scribes  
2. Review of Action Items from Previous Meeting(s)  
3. PPA Discussion Topics  
   1. ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)  
   2. ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)  
      1. [Clearing browsing history for any site effectively clears data for all sites](https://github.com/w3c/attribution/issues/393)  
      2. Epoch cadences  
      3. PR Safety Limits   
      4. \[Possible Budgeting Optimization\] Determine |isSingleEpoch| from impressions themselves  
4. AOB  
5. Future Meetings Information & Logistics  
6. Review of Action Items From This Meeting

## Introductions, Code of Conduct, Minutes Document, Scribes

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

### ["Discuss" Issues](https://github.com/w3c/ppa/issues?q=state%3Aopen%20label%3A%22discuss%22)

### ["Other" Issues](https://github.com/patcg/ppa-api/issues?q=state%3Aopen%20-label%3A%22discuss%22%20%20is%3Aissue)

#### [Clearing browsing history for any site effectively clears data for all sites](https://github.com/w3c/attribution/issues/393)

Looking at the Clearing browsing history question   

Alex Turner: Looking at the smaller task among those we had to fix. The clear site algorithm has an option to clear the data from all sites or certain sites. But it ends up doing the same thing at the end, just by a different route. The overall result maybe hadn’t been considered here. Do we expect to clear all site data in this case and if we do we can simplify?

Ben Case: How safety limits will interact with clear history. Last time we removed this epoch store which is per site. So there’s less that would need to be cleared. So if we have a simple thing where it is like clear all history that we can just clear everything. We don’t really need to restart the epoch cadence, because that is across all sites now. But I think that is interesting for the impressions quotas. The impression quotas and limits are an interesting question, as we consider cross site quotas and limits.   

Andrew Paseltiner: I don’t know about the chosen wording here.

Ben: We may have to talk with Martin about desired behavior then. It seems nice that we could clear a subset but maybe we want to start simpler. The concern is the local throttle. Access to the local browser state would let you know which site had history cleared. Previously we had a different start time for every epoch. For every conversion site. But now all sites share an epoch start so we don’t need to worry about that. Do we leak some information that a site was sort of here or deducted off of quotas?

Pierre Tholoniat: Tension between clear anything and keeping a meaningful global budget. We probably cannot reset the budget or anything like that, you want to keep clear DP.

Ben: When we clear budget for an epoch we disable the API for the remainder of the epoch to preserve DP. All sites is very clear, but a subset is harder here. Subset would not change the actual state of the global budget.

Aram: Did we agree to clear all sites previously? I think so. How often is the user going to clear a specific site realistically? Users just clear browsing history?

Andrew: User agent may have some leeway to add controls for deleting for a specific site and not have the spec clear it. But lets check with Martin since he wrote this.   

Ben: I will write a quick comment. 

#### [Epoch cadences](https://github.com/w3c/attribution/issues/386)

Ben: I think we can land this issue without further discussion, after last meeting I have something here ready to merge. 

#### [PR Safety Limits](https://github.com/w3c/attribution/pull/309) 

Andrew: Need to take a look at it again and re-translate the changes into the simulator. Almost ready to merge, try to get to it today.

#### [\[Possible Budgeting Optimization\] Determine |isSingleEpoch| from impressions themselves](https://github.com/w3c/attribution/issues/398)

Ben: Number of epochs per single request that are in the lookback window. When we start checking what’s in the filters for that request is this a future optimization thing.

Pierre: So basically you maybe would have a request that comes you request 10 epochs that contain the relevant impressions and if for that one epoch can you use the data. Accounting to expand less privacy proportional to the maximum value. Yeah we thought about this while writing the cookie monster paper. We don’t support this I think, because we are not sure if it is possible using the composition logic.

Ben: You explanation makes sense, even the lack of the impression,  is sort of effecting across epochs based  on the data that you have. It is gonna change the sensitivity across epochs.

Pierre: I don’t think this optimization would be valued from a DP perspective. 

## AOB

## Future Meetings Information & Logistics

- Thursday's CG meeting canceled.   
- Next meeting in Berlin  
- Slide the CG that month a bit later in the day. 

## Review of Action Items From This Meeting

- [ ] Keep old ones
