Ping-pong buffer solution:
- Increase shared memory buffer by 2
- Use the same buffer format
- Async send
- Then pack the next buffer -> async send
- Wait for previous buffer
- Repeat.

Staging plan:
- Do sender first - receiver after
- Naive - stage 1: Increase shared memory buffer by 2
- Send the first buffer - then do async copy for the 2nd buffer
- Wait 2 seconds -> print the 2nd buffer and exit
- Naive - stage 2: Remove the 2 second wait with a wait
- Naive - stage 3: Send the first buffer, then send the 2nd buffer, then pack the 1st buffer
- print and exit
- Check on receiver end for the 2nd buffer.

Napkin math:
- 256 tokens global
- 16 tokens per rank
- hidden_dim = 7168 - 2 bytes each (bfloat16)
- 230,336 bytes per rank
- If each rank has at least 1 block and each block does transfers of 122,688 bytes per rank
- then this will complete within 1-2 transfers.
- In that case doing a double buffer seems like we won't get that much overlap.
- But suppose we reduce the transfer size? Then we can overlap more - but if we're bandwidth bound - then that might cause more slowness?
- Very unlikely that we're bandwidth bound - very likely we're bound by latency.
- So if we reduce the transfer size - and overlap all these overheads - maybe we'll get a latency speedup?



Prompts:
- Give me additional ideas or help me prioritize the above ideas. Lmk if you need more details - I can run ncu to get more details.