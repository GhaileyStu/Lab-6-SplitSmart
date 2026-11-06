# ChatGPT Architecture Prompt

You are a senior software architect reviewing two architecture proposals for SplitSmart.

Project context:
- SplitSmart is an expense-sharing application for groups such as roommates, friends, family, and coworkers.
- It supports user accounts, groups, expense entry, approval workflow, notifications, balances, payments, and reports.
- The current Java Swing prototype is a temporary desktop MVP.
- The full product must be primarily web-based and also available on Android and iOS.

Use the requirements in `spec.md` as the authoritative source.

Compare the baseline architecture in `architecture.md` against an alternative architecture proposal for SplitSmart.

Your review must include:
- requirement coverage
- architectural fit
- domain model quality
- technical practicality
- complexity versus value
- scalability and performance considerations
- maintainability and future growth
- risks, inconsistencies, and missing details

Provide a structured review with:
1. Executive summary
2. Requirements alignment analysis
3. Architecture comparison by dimension
4. Strengths and weaknesses of each proposal
5. Weighted scoring (1-10)
6. Final recommendation

Be objective. The preferred architecture should be the one that best fits the actual needs of SplitSmart, not necessarily the more modern or sophisticated one.
