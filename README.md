# Harshavardhan Jothikumar

I build real-time voice and LLM systems — the kind where the audio thread has a 20ms deadline and the model behind it answers on a budget measured in seconds. Most of my working time goes into the scheduling problem that sits between those two facts.

Senior SDE at Mynaksh, on real-time voice infrastructure: WebRTC/VoIP transport, streaming speech recognition and synthesis, and the conversation layer that decides when an agent should speak and when it should stop. Before that, founding engineer at Mantys (YC W23) and on the founding team at Reachgig.

## What I've built

**A voice product from empty repository to paid live service in about six weeks**, across five services. Holding a 20ms audio cadence against second-scale model latency means everything downstream of the LLM streams and everything upstream of the speaker runs to a deadline; there is no slack anywhere in the middle. Billing was per-minute against a wallet, which made correctness on call teardown a revenue problem rather than a cleanup detail.

**Transport work below the SDK boundary** — SRTP/DTLS/ICE, STUN/TURN, Opus over RTP, jitter buffering. The debugging case I'd pick if you asked for one: every call was dying at exactly ten minutes. The cause was a credential-refresh timer in a TURN layer scoped globally instead of per channel, so the second channel inherited the first channel's expiry. Found it by tracing inside the library in production, since the symptom was invisible from the outside, and shipped a vendored patch.

**Interruption handling that tracks what the caller actually heard.** Naive barge-in cuts on voice activity and assumes the transcript agrees with the audio the user received; it doesn't, because synthesis runs ahead of playback. Resolving that at sentence granularity is most of the difference between an agent that feels conversational and one that talks over people. Streaming STT and TTS across 11 languages.

**Money-critical paths**: wallet and ledger design, idempotent settlement, recovering call state when a vendor SDK dropped it mid-session, and the unit-economics model underneath the pricing.

**LLM evaluation at a production bar.** At Mantys, an evaluation framework for clinical prior-authorisation validated at 98% extraction accuracy, along with the retrieval and extraction pipeline it graded.

**Performance work by measurement, not intuition.** A 7-second screen render taken to imperceptible after counting the work instead of guessing at it: 21,780 redundant translation lookups in one file reduced to 330, across 145 call sites.

## Open source

Three clean-room benchmarks, one per layer of a real-time voice path, in the order audio moves through them. They share one statistics package, so a p99 in one means the same thing in the others.

**[cadence-bench](https://github.com/Harshavardhanjo/cadence-bench)** — the send side. Whether a loop can hand the transport a frame every 20ms under CPU and GC pressure, across four ways of writing that loop. The one almost everyone writes first drifts without bound, and it only shows under load. The harness measures the host's clock first and refuses to report figures the clock cannot resolve. CI measures it on Linux, macOS and Windows.

**[jitter-bench](https://github.com/Harshavardhanjo/jitter-bench)** — the receive side. How much latency a jitter buffer has to add to keep audio continuous, traced as a curve across synthetic network conditions, with an RFC 3550 adaptive policy placed on it. Late discards are counted separately from network loss, because those are packets the buffer chose to lose. [Try it in the browser](https://jitter-bench.harshavardhanjo.com).

**[turn-bench](https://github.com/Harshavardhanjo/turn-bench)** — the conversation layer. When a caller talks over an agent, the agent decides when to stop speaking and what to record as having been said. Synthesis runs ahead of playback, so the second decision can quietly fill the transcript with words the caller never heard. [Try it in the browser](https://turn-bench.harshavardhanjo.com).

The browser UIs run the same Go code compiled to WebAssembly, so a chart cannot disagree with the command-line tables.

Also public: [RAG-chatbot](https://github.com/Harshavardhanjo/RAG-chatbot), PDF ingestion and pgvector retrieval added to Vercel's chat template, and [astrochat](https://github.com/Harshavardhanjo/astrochat), a React Native chat UI with gesture-driven replies and reactions.

## Tools

Python and TypeScript daily — asyncio, FastAPI, Celery, PyAV/Opus. Go, Java, C++ and Kotlin as the problem calls for them. Postgres, Redis, BullMQ/SQS, ECS/Lambda, Docker, Grafana/Loki, pgvector.

B.Tech CSE, VIT Chennai.

---

[harshavardhanjo.com](https://harshavardhanjo.com) · [LinkedIn](https://www.linkedin.com/in/harshavardhan-jothi-kumar-259ba8185/)
