# Private Advertising Technology WG - 2024-12-19

## Attendees

1. Sean Turner (sn3rd)  
2. Aram Zucker-Scharff (The Washington Post)  
3. Arthur Coleman (ThinkMedium)  
4. Brian May (unaffiliated)  
5. Tara Whalen (W3C)  
6. Aloïs Bissuel (Criteo)  
7. Erik Taubeneck (Meta)  
8. Michael Kleber (Google Chrome Privacy Sandbox)  
9. Benjamin Case (Meta)  
10. Richa Jain (Meta)  
11. Andy Leiserson (Mozilla)  
12. Thomas Prieur (Criteo)  
13. Charlie Harrison (Google Chrome)  
14. Garrett Johnson ( Boston University)  
15. Joey Knightbrook (Snap)  
16. Andrew Pascoe (NextRoll)  
17. Tammy Greasby (Anonym/Mozilla)  
18. Miguel Morales (IAB TechLab)  
19. Martin Thomson (Mozilla)  
20. Arpana Hosabettu (Google Chrome)  
21. Christian Berkhoff (Meta)  
22. Maxime Vono (Criteo)  
23. James Aylett (Omnicom / Annalect)  
24. Haley Patoski (Google Chrome)  
25. Roxana Geambasu (Columbia University)  
26. Russell Stringham (Adobe)

## Agenda

### Hellos, Intros, Policies, & Call for Scribes

Sean: Welcome to the first meeting\!  It's been a long time coming, glad that we are finally a formed group.  

\[Round of introductions, since it is the first meeting — see participants [list](#Attendees).\]

Please use the \#patwg channel on W3 slack for back-channel chatter

"Read All About It" slides:

* There will be contentious topics; remember to be courteous, talk to the Chairs if you experience behavior to the contrary.  
* You're a member, you have agreed to the rules of the road here, remember that.  We will mostly do things by consensus, though sometimes things may come to a vote, all laid out in the documents  
* The group has IPR policies and licensing requirements, you should read them  
* We may take external contributions, as spelled out in the Charter

WGs are different than CGs: we're developing a standards track document, this is the meat of the matter.  There are patent policy and licensing obligations associated with the work you do here.

### Discuss W3C mechanics for working group

Sean: Invited Experts, people who have been active in developing work in this area, 6 approved people, contact the Chairs.  WG policy is that IEs do not have a vote, if it comes to voting.

Almost all communications are open.  We can set up a private mailing list just for WG participants, but in fact we have chosen to not set that up, at least not yet.  Might go back and revisit if there is a need, but we don't see one yet.

David Dabbs: The CG still exists, and I know there are things CGs cannot do.  Have you delineated why keep around a GC?

Sean: There are other documents that are not standards track that the CG will keep owning, and also the CG will keep generating the future work that will feed into the WG including later features of the measurement API we're working on here.

Aram: Also part of our charter includes consulting with the CG, as part of collecting feedback from the broader community outside of the Working Group.

Sean: We will get to cadence, probably going to keep the historical two days of meeting, once WG and one CG, order not yet chosen.

Modes of working — (links to) calendar, github repo, slack, mailing list.  So far 58 participants (incl 6 IEs) representing 16 organizations.

Mostly GitHub-centric organization: PATWG GitHub repo under the W3C, meetings will be organized there in a dedicated repo, announcements will be sent to the mailing list.  Consensus calls will be run and tagged directly in the repository.

Consensus call: GitHub-centric with Slack on the side — are there any objections?

Charlie Harrison: Can you be more specific about what topics belong in Slack?

Sean: Clarifications OK in slack, but need to open an issue for discussion and open a PR for the change itself.  If the issue needs back-and-forth discussions, slack is a good way to do that, but discussions should mostly be in GitHub, easy to search and is permanent.

Aram: Slack expires — anything that makes a substantive contribution to the argument, or is informational or contains data about what we're working on, those should all go into GitHub.

Charlie: Slack is fine for logistics maybe?  I'm happy with what you just suggested.

Brian May: Slack is great for clarifying things, keep an eye on it, but we should make sure anything important ends up on GitHub.

David Dabbs: Is there any Slack/GitHub integration?

Martin Thomson: It does happen, but you get twice the noise.  It's not great.  The differentiation between the two is often helpful.

Sean: Happy to keep things separate in that way.  If we see stuff bleeding over or in the wrong place, we will try to steer things, and feel free to remind the Chairs if you see this not going right.  Seeing no objections to the consensus call, this is how it will go.

Minutes and Recordings / Transcripts: Google Doc for notes, W3C policy says no recordings if anyone objects.  If there is a way to turn on transcripts, we could try that, since our minutes are pretty transcript-like?

Martin: Apple people and Nick Doty have both objected in the past.  Please talk to them first.

Sean: I will check with them offline.  If anyone says "no" then we just drop it.

Charlie: Sean, do you have experience with the auto-transcript feature?  I've tried it and it has caused more problems that it's worth with poor transcriptions, you end up with an extra job beyond scribing.  Not making a normative claim of whether I'm OK with it, just pushing back on the idea that it would make our lives easier.  Please someone chime in if you've had a good experience.

Aram: I find it useful, I feel like we could try and see.  I feel like it's a reference for cleaning up good minutes, but cannot replace minutes.

Arthur: It definitely helps when there are echoes, bad audio, accents, other reasons that make things hard to understand.

Aram: Let's give it a try unless someone raises an objection.

Arthur: Let's say we're working on v2 in the CG and a question comes up there.  Can we bring it up here?

Aram: If questions relate to the CG then bring it up in the CG; if it's a question about our work here in the WG then bring it up here.  If it is a question about how the two groups relate, then bring it up here.

Arthur: Let's say a question crosses over between v1 and v2, and a question comes up in CG that seems relevant to WG work.  Can we bring it up here?

Aram: We're not discussing v2 stuff in WG, that should stay in CG.  Right now this is a joint meeting, so OK to bring it up now.  For WG in general we are going to stay focused on v1, so let's remain focused.

Brian: Re earlier point about putting salient information into issues — also remember there are two groups on slack

Andrew: I'm OK with transcript but no video recordings

Sean: I amended the slides to only audio recordings / transcripts, to be clear it's not video.  If anyone objects, we will not do it.

We need to have minutes up and posted within 48hrs after a meeting, will convert to Markdown and post promptly.

## Adopt PATWG Charter Repo from the PATCG into the PATWG

Sean: Consensus call to adopt repo with our WG Charter in it, which is offered to us by the CG that hosted it now.  Any objections?  \[none heard\]

## Adopt PPA-API Repo from the PATCG in to the PATWG

Sean: Want to move the PPA-API repo over and adopt it as well.  The charter does not explicitly mention PPA-API, but talks about measurement stuff in general, and this is the one incubating in the PATCG for a while.  Martin, introduction?

Garrett Johnson: What does it stand for?

Martin: Privacy-Preserving Attribution.  It produces aggregate histograms that are differentially private.  (We could call it the Conversion Measurement API, CMA, instead?)  The basic idea is that at conversion time you can pick a value to contribute to a histogram bucket, based on ad impression/click and conversion information, and data can be aggregated across many people and be made differentially private.  The shape is very familiar to this group.

Sean: Was incubating on its own for a while, then the PATCG adopted while this WG was being chartered, consensus call was to bring it into the PATCG temporarily and then hand to WG once we were chartered.  Now we are.  Looking for consensus to bring the repo over and adopt it as a first public working draft.  Any objections?  \[none heard\]  We will do an official consensus call on the repo.

Erik: PPA doc has a bunch of content already.  What is the minimum amount of content that we would accept before we adopted and started working on something?

Sean: Seems very case-by-case, can't think how to answer in general.  Seems like it should be more than a skeleton, and we are member-driven in terms of contributions, but… it depends.  The API we're adopting here is pretty sketchy in parts; we definitely don't expect something to be fully-formed.  Need to work to find a happy medium — risks of showing up with too little or with too much.

Aram: Really all about consensus of the group.  Need to be enough to be taken seriously.

### Managing Future Specifications

Sean: In the PPA spec, we need to figure out whether we want to auto-publish.  Do you wait and stage things and publish a bunch of stuff all at once, or do you just publish upon merge?  Aram seemed very happy about it, there is automatic tooling to support it.  Other opinions, especially from people with GitHub foo?  Any concerns with the mode of working?

Charlie: Can you explain the alternative?

Martin: The other choice is for editors to manage doc on their own, eds take snapshots, lots of process about when there is a good state for taking a snapshot.  Concern about IPR releases in other WGs meant deliberate snapshots were needed, but it's heavyweight.

Eric: Does that mean a PR being reviewed is a consensus call?

Martin: There are a number of processes that apply.  Editors making a call about whether a PR is ready to go in.  Working Drafts don't have any official status, so the auto-published thing could have errors or could have things people disagree with; it's when the doc goes to Candidate Recommendation stage that decisions and objections happen.

Sean: and remember to tag PRs as editorial if they don't make substantive changes.  Any objections to auto-publish?

Arthur: As an IE, am I good to participate in editing a PR?

Sean: Absolutely, as an IE you can participate in PRs.  Only excluded from voting.

Martin: Please do\!  You write well, and the document could be greatly improved by good writing.

Brian: Maybe it would be helpful to see a reference for the steps of a PR lifecycle leading to auto-publish.  I won't be remembering the stages at all times.

Martin: There is a copy in GitHub; that copy will be published, with a time-delay of minutes.  Merge a PR into the copy in GitHub → published on the website within minutes.

Sean: Lifecycle of a PR: Starts as an issue, open a PR, gets tagged, discussion on Issue and PR.  We can work up a description of the flow.

Martin: Process will be similar to elsewhere: Issue to be filed for anything substantive, then a PR to address whatever was opened in that issue, and editors will judge when it's ready and then merge.  Anything controversial means editors and Charis will be involved.  Substantive discussion belongs on the Issue, not in the Pull Request.

Brian: Those sorts of things would be helpful to have written up somewhere, please document.

Sean: Consensus call for auto-publish based on merged changes?  Will do official call, but sounds good, with more documentation.

Sean: Regarding "Adopting Other Documents" They will mostly be incubated in the CG and then pop over here after some amount of time.

### Scheduling of Future Meetings

Sean: Heard no objections so far to a Tuesday/Thursday as we've been doing, one for WG and one for CG, order unclear.  It would be nice to have them scheduled on a regular basis, so that we're on people's calendars, rather than ad hoc scheduling.

Brian: I feel like CG before WG is more natural.

Charlie: I was going to say the opposite — the CG is more likely to bring up new and different topics; seems more likely that an issue will come up in the WG where we want to solicit feedback from CG site.

Aram: That mirrors the pattern we have seen in other WGs, especially for in-person meetings.  Common to come up with a technical thing and then dive into details with stakeholders on the CG call.

Brian: I was thinking there is a risk of the WG dictating the agenda for the CG meeting.  Seems like the CG starting up without the WG setting it up for specific things gives the CG more freedom.

Aram: I think we as Chairs can moderate to prevent that.  WG picking up work from CG would go through issues etc anyway, not just discussion from meeting.  WG adopting something that just came up from CG immediately seems unlikely, there will probably be process in between.

Erik: Work in WG will be going through PRs that have been opened, need consensus calls, etc.  GC will be more about bringing up new ideas, here's a thing we've been thinking about, general discussion.  Knocking out PRs seems like the right thing to do first, to clear the queue and be ready to think about the future.

Aram: That makes sense.  Unless there is an objection, my sense is a preference for WG-first, CG-second.  WG can hand stuff off to CG to consult on; CG-to-WG isn't a quick thing.

Brian: I'll remove my suggestion to the contrary, I'm persuaded by discussion here.

Aram: Any objections?  \[none heard\]

Sean: Next question is how often to meet.  I'm inclined to go for a monthly cadence.  This is somewhat more than our current pace.  Maybe we will focus on other documents in CG now that WG is spun up.

Erik: Can we keep them more separate, and e.g. be ready to cancel one but not the other?  Also can we institute cancellation if there isn't an agenda one or two weeks beforehand?

Sean: Makes sense, and also we should shorten to 2hr not 3hr.

Brian: Should we just have independent schedules for the two?

Sean: Maybe.  No straw proposal here; depends on how often we have inputs and cadence on documents.  We could have a monthly one for WG on Tuesdays, e.g. "third Tues", and run CG separately, totally OK with that.  That depends on authors or others waiting to drop contributions, if it seems sustainable.

Erik: Instead of separating, we could do WG monthly and CG after half of them.

Sean: I will reach out to Nick to ask about churn and work on Principles doc will be in the future.  The Threat Model document seems pretty stable.

Arthur: Frequency seems related to how often Charlie, Martin and others need feedback.  PPA-API shows lots of issues and very often; writers want feedback often.

Sean: Having watched people here churn out many issues, it can become very complicated and lots of burden.

Charlie: We can always re-evaluate.  Right now a lot of work in the WG is stuff that was previously happening in CG, where we had way less than monthly.  Seems to me that starting at Monthly is fine, and we can see whether we need more.  If we're doing lots in GitHub Issues, and people are engaged, then we may be fine.  Since this is early-stage work, nobody testing yet, I expect we will get less of a flood of engagement, compared to the WICG PA calls that Arthur was referring to.

Martin: Editors are able to meet at a more frequent cadence than the larger group, to get through the nuts and bolts of putting words in place, but most of the work will happen on the issues.

Sean: Other point: No fixed time, expect a rotating schedule across three timezones, as has happened before.  Sorry, we know this is painful for everyone sometimes.

Sean: There are also in-person meetings at TPAC annually (this year in Kobe Japan), and we can also meet in person another time.  We have standing invitations to host, for the CG at least, and likely to translate to WG.  Things move a lot quicker at face-to-face meetings.  For WG we have hard requirements to publish time and place 8 weeks in advance, e.g. at this point it would be impossible to have one sooner than April.  We can table that for now and move on.

Brian: We had three f2f's last year — was that enough?  Will WG want more than that?

Sean: They are painful to set up, doing farther in advance in some ways makes that harder.  Try to do one at the start, maybe make it two?  We'll see.  It's not cheap to fly around the world.  I was thinking about May-ish, but can't say that until I arrange an actual place to host 50 people.

Sean: See action item list below.  Anything we have missed?

Sean: Other point: No fixed time, expect a rotating schedule across three timezones, as has happened before.  Sorry, we know this is painful for everyone sometimes.

Sean: There are also in-person meetings at TPAC annually (this year in Kobe Japan), and we can also meet in person another time.  We have standing invitations to host, for the CG at least, and likely to translate to WG.  Things move a lot quicker at face-to-face meetings.  For WG we have hard requirements to publish time and place 8 weeks in advance, e.g. at this point it would be impossible to have one sooner than April.  We can table that for now and move on.

Brian: We had three f2f's last year — was that enough?  Will WG want more than that?

Sean: They are painful to set up, doing farther in advance in some ways makes that harder.  Try to do one at the start, maybe make it two?  We'll see.  It's not cheap to fly around the world.  I was thinking about May-ish, but can't say that until I arrange an actual place to host 50 people.

Sean: See action item list below.  Anything we have missed?

## Action Item Review

The chairs need to:

* Consensus call: gDoc -> recordings/transcripts -> markdown
* Consensus call: Github Centric + Slack (logistics)
* Consensus call: Adopt PATWG charter repo
* Consensus call: Adopt PPA-API as FPWD
  - Auto-Publish included
  - Process Explainer: Issue -> PR -> Merge
* Establish: once per month WG meeting, 2 hours long, rotating times zone
