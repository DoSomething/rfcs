# Dynamically Imported ENV Variables

Establishing a system where environment variables declared in an environment's `.env` file are dynamically imported both on the backend and the frontend will help simplify the use of test variables and feature flags within Phoenix.

**Discussion:** <https://github.com/DoSomething/rfcs/pull/8>

## Problem

We do not currently utilize setting environment variables in the `.env` as much as we could within Phoenix because it is cumbersome to do so. Each new variable added to the file requires additional code updates in different locations to make the variable available to the backend as well as the frontend. Due to this process, most developers prefer to use env variables only for the backend and setting hard-coded constants in a variety of different JavaScript files for the frontend. This can lead to issues with inconsistency in variables and lack of confidence in knowing that a feature flag or test suite variable is readily available.

Additionally, since environment specific variables are typically hard-coded for the front-end, the JavaScript code tends to require checks against which environment the code is running on to ensure it is using the correct variable.

## Proposal

It could be possible that by generating a standard naming convention for these types of environment variables (used for feature flags and test suite specific runs), we could dynamically import variables set within the `.env` file to automatically become available for both the backend and the frontend. Developers would only need to create the variable in the `.env` file and they could be confident in knowing it would become available universally.
