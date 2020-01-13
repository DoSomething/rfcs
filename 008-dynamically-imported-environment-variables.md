# Dynamically Imported ENV Variables

Establishing a system where environment variables declared in an environment's `.env` file are dynamically imported both on the backend and the frontend will help simplify the use of test variables and feature flags within Phoenix.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/8>

## Problem

We do not currently utilize setting environment variables in the `.env` as much as we could within Phoenix because it is cumbersome to do so, since each new variable added to the file requires additional code updates to the

## Proposal
