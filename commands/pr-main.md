Create a draft PR that targets `main`.

Use the Conventional Commits format for the branch name and each commit message.

The project uses squash merge. Use a descriptive branch name.

Before you create the PR:

1. Update the branch with the latest changes from `main`.
2. Resolve all merge conflicts.
3. Make sure the branch is ready to merge.

In the PR description:

1. Write the `why` and `how`, without using headlines.
2. State why the PR exists and how it changes the project.
3. Use short, clear sentences.
4. Do not list test or verification results.
5. Use ASD-STE100 Simplified Technical English.

## Writing style

1. Do not use openers, closing text, or filler.
2. Do not use smart quotes, em dashes, or ellipsis characters.
3. Do not use decorative Unicode. Use natural-language characters when the content requires them.
4. Do not add a preamble, summary, or closing remark.
5. Make sure all strings are safe for JSON serialization.

## Body

Use no other headings or Markdown.

Before you submit the PR description, remove jargon. Use simple, clear language.

Examples: 

```
Legal paperwork and audio need a private review queue before the system sends them to AI.

Add private storage metadata, signed upload and read endpoints, and reviewer approval routes.
```

```
Large result buffers stay in memory until the request ends. This can cause high memory use for large requests.

Process results in chunks. Release each chunk after the system writes it.
```