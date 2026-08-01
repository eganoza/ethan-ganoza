# FX Hedge Framing for the EUR Receivable

**Created by:** Ethan Kainalu Ganoza 
**Updated by:** Ethan Kainalu Ganoza 
**Date Created:** 2026-07-30 
**Date Updated:** 2026-07-31 
**Version:** 1.0
**LLM Used:** Codex (GPT-5)

---

## Executive Summary 

We're getting €4.5M in July 2027 that's locked. What's not locked is what we'll actually collect in USD. At our planning rate of 1.10, that's $4.95M. But currency moves. If the euro falls to 1.00, we're looking at $4.5M instead. That's a $450K gap that blows a hole in margins and liquidity. I want to evaluate our options now, before this becomes a problem. This isn't about betting on the euro, it's about choosing how much certainty we buy, what upside we keep, and what it costs us.

## Background & Objectives

We have a euro receivable but we operate in dollars. That mismatch is the risk. The euro could weaken tomorrow or hold steady for a year, but we can't budget for both outcomes. We need to pick a strategy and know what we're getting in USD at settlement.

The goal is simple: make a decision we can defend to the CFO, know the USD outcome for each hedge, and understand the trade-offs. No surprises at settlement. 

## Methods

I'm comparing three approaches.

**Forward:** Lock the rate today. You get certainty. You give up upside. Plus there's counterparty and settlement stuff to manage.

**Money-market hedge:** Borrow euros now, convert at today's rate, invest the dollars. It's a DIY forward built from rates instead of a contract. Works if forwards are expensive or hard to find. Downside is it uses up borrowing capacity and requires more moving parts.

**Put option:** Buy insurance. You pay a premium to set a floor on what you receive. If the euro strengthens, you keep the upside. If it weakens, the floor catches you. The cost is the premium either way.

## Limitations & Next Steps

I'm not recommending a hedge yet. I need to source real rates, premiums, and terms. With approval, I'll build a workbook that models all three strategies, audit it, plug in live market data, verify the math, and then recommend what makes sense. That's Phases 2–5.

Right now I'm asking for a green light to build the analysis.
