# Client review: Larkspur disruption-care agent

**To:** Room 21  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-11-17 10:07

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py get_fare_rules() calls mock_backend and Path, and neither is imported in the file.**

The diff adds `from support.mock_backend import load_policy` and no other import. get_fare_rules() then runs `mock_backend.get_booking_raw(pnr)` and `Path(__file__).parent / "data" / "americas" / "fare_rules_excerpt.md"`. The fare_rules tool is registered in LOCAL_TOOLS, so the first call Claude makes to it can hit a NameError.

Run python3 run.py --show-tools, then run python3 run.py K7PQ2M --trace with a customer message asking what the ticket allows, and paste the fare_rules entry from the trace.

**2. readout-trace.json shows 3 tool calls (lookup_booking, get_flight_status, check_policy) and none of the 3 tools you added.**

The committed wire run made 4 API turns, 12584 tokens in and 1081 out, in 13.8s. next_available_day, cause_in_plain_words and fare_rules never appear. Nothing in the repository shows any of them firing on a real booking.

Run python3 run.py --all --trace and paste the totals footer plus the tool sequence for each booking.

**3. search_alternatives and hold_seat sit at 47 and 73 characters, while fare_rules carries 563 and check_policy 445.**

Your diff rewrote the search_alternatives description to "Search for alternative flights for a given PNR." That string says nothing about when to call it or what it returns, and send_confirmation is also thin at 71. The next_available_day tool you added overlaps with it, and both descriptions are short enough that Claude has little to choose between them.

Run python3 run.py --show-tools and paste the search_alternatives and next_available_day entries side by side.

**4. Priya's model question has no data behind it: no evals/cases.json, no bench file, and prompt caching shows 0 read, 0 written.**

readout-trace.json reports hit ratio None across 12584 input tokens, and agent.py contains no cache_control. TONE_ADDENDUM is 0 characters, and the schemas total 12 tools against the 9 the pack ships. A bigger model reads the same 12 schemas on every turn, so tool-description quality and the cache gap stay where they are.

Run python3 bench.py --label before --stage 1 --runs 3, then python3 bench.py --compare before after once you change something, and paste the compare output.

**5. run_agent() in agent.py now appends response.content and sets answer inside the loop, a fix you can claim only after a multi-turn run.**

The diff replaced `"content": text_of(response)` with `"content": response.content`, so tool_use and thinking blocks go back to the API intact. It also moved `answer = text_of(response)` after the second client.messages.create call. MAX_TOOL_CALLS is 8, and the one committed run used 4 turns.

Run python3 verify.py 1.2 and paste its output.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (317 lines)`
- `PITCH.md`
- `TEAM.md`
- `readout-trace.json`
- given files that differ from the shipped pack: `verify.py`, `eval_harness.py`, `bench.py`, `readout.py`, `setup.py`, `pod_sync.py`, `run.py`, `review.py`, `support/__init__.py`, `support/client.py`, `support/data.py`, `support/models.py`, `support/tools.py`, `support/trace.py`, `support/offline.py`, `support/mock_backend.py`, `support/mcp_client.py`, `support/mcp_server.py`, `support/mcp_selftest.py`, `support/access.py`, `support/keyscan.py`, `support/review.py`

Reviewer: `claude-sonnet-5-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
