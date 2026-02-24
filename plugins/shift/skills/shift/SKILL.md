---
name: shift
description: Process Laravel Shift pull requests - review changes, act on instructions, and test the app
argument-hint: [pr-number]
---

# Process Laravel Shift PR #$ARGUMENTS

Review and process this Laravel Shift pull request from this repo.

## Step 1: Fetch PR Details

Use `gh` CLI to:
1. Get the PR description and diff: `gh pr view $ARGUMENTS --comments`
2. Get all comments on the PR for any additional instructions
3. Review the changed files: `gh pr diff $ARGUMENTS`

## Step 2: Check for Uncommitted Changes

Before checking out the PR branch, verify the working directory is clean:
```bash
git status --porcelain
```

If there are any uncommitted changes, **STOP immediately** and tell the user:
> "You have uncommitted changes in your working directory. Please commit or discard them before running this skill."

Do NOT stash, reset, or otherwise modify their working directory. Only proceed if `git status --porcelain` returns empty output.

## Step 3: Checkout the PR Branch

Checkout the PR branch locally so all testing happens on the actual PR code:
```bash
gh pr checkout $ARGUMENTS
```

## Step 4: Understand the Changes

Analyze what Laravel Shift has done:
- What Laravel version upgrade is this (if applicable)?
- What packages were updated?
- What code changes were made?
- Are there any breaking changes noted?

## Step 5: Composer Update

First, run `composer update`. If it fails with PHP version compatibility errors, check whether this is a Valet or Herd isolated site:
1. Run `valet isolated` (if that fails, try `herd isolated`)
2. Check if the current directory's folder name appears in the output as an isolated domain
3. If it does, re-run as `valet composer update` (or `herd composer update` respectively)

If `composer update` fails for other reasons, investigate and fix before proceeding.

## Step 6: Act on Instructions

Next, follow any instructions given in the PR description or comments. This typically includes:
- Running migrations
- Updating configuration files
- Making code changes that Shift couldn't automate
- Addressing deprecations

## Step 7: Handle Optional Upgrades

For any changes marked as "optional" in the PR:
- **Small changes** (one-liner, simple config tweaks, straightforward updates): Just do them automatically
- **Medium to large changes** (new features, architectural changes, changes requiring decisions): Ask the user before proceeding

## Step 8: Test the Application

After making changes:
1. Run `php artisan test --compact` to verify tests pass
2. Fix code style on changed files: check `composer.json` for Duster, Pint, or PHP CS Fixer (in that priority order) and run the first one found. Use the `--dirty` flag for Duster and Pint. If none are found, skip this step.
3. If tests fail, investigate and fix the issues

## Important: Do Not Commit or Push

Do NOT commit any changes or push to the remote. Leave all changes as uncommitted modifications in the working directory so the user can review them before committing.

## Step 9: Report Back

Summarize:
- What changes were made by Shift
- What additional changes you made
- Any optional upgrades you skipped (and why they need user input)
- Test results
- Any remaining action items
