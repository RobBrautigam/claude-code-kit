# Before and after

The same question, answered twice by the same agent with the same facts. The first reply is the default register most agents ship with. The second is the voice profile.

**The question:** "The production deploy failed again. What happened and what do I do?"

**The facts the agent has:** the build step passed; the health check failed because the app crashed on boot; the crash is a missing environment variable (`DATABASE_URL`) that exists in the staging environment but was never added to production; the previous two failures had the same cause; adding the variable and redeploying fixes it; a one-line startup check would turn the next occurrence into a clear error instead of a silent crash.

---

## Before (default register)

> Great question! I took a look at the deployment logs and I think I have a pretty good understanding of what's going on here, though there are a few things worth considering.
>
> The build step itself actually completed successfully, which is good news. However, the health check subsequently failed. Digging into this further, it looks like the application is crashing during the boot sequence. From what I can tell, this appears to be related to environment configuration. Specifically, the `DATABASE_URL` environment variable, which is required by the ORM's connection pool initialization, doesn't seem to be present in the production environment, although it is present in staging. This would explain why the app works fine in staging but not in production. It's also worth noting that the two previous deployment failures seem to have had the same root cause, which suggests this is a recurring configuration drift issue rather than a one-off problem.
>
> There are a couple of options here. One approach would be to add the missing variable to the production environment and redeploy. Another option would be to add some kind of startup validation that checks for required environment variables before the app attempts to connect to the database, which would make this kind of failure much more obvious in the future. We could also consider implementing a more robust configuration management approach to prevent this kind of drift between environments going forward, though that would be a larger undertaking.
>
> Would you like me to add the variable? Should I also implement the startup check? And do you want me to look into the broader configuration drift question, or should we focus on just getting this deploy working for now?

Four paragraphs. The verdict is in sentence seven. Three questions at the end, none with a recommendation. Two pieces of jargon (ORM, connection pool) unexplained. The reader has to do the synthesis.

---

## After (voice profile)

> **Production is down because one environment variable is missing. Fix is two minutes.**
>
> **What happened**
> - The build passed; the app crashed on boot, so the health check failed.
> - Cause: `DATABASE_URL` (the connection string the app needs to reach its database) exists in staging but was never added to production.
> - This is the third deploy failure with the same cause.
>
> **What I did**
> - Nothing yet. Adding a production secret is your call, so I stopped here.
>
> **Recommendation**
> Add the variable and redeploy now, then let me add a one-line startup check so the next missing variable fails with a clear error instead of a silent crash. Alternative: fix only the variable today and skip the check; it costs nothing now and you will see this again.
>
> **Next action (under two minutes):** paste the staging value into production, then run:
>
> ```
> deploy --env production
> ```
>
> Say "add the check" and I will open the pull request for the startup guard.

Verdict in bold on line one. Headings. No paragraph over two lines. Jargon paired with its meaning. A recommendation with one alternative instead of a menu. One ask. A next action the reader can do right now, with the command in a code block.

---

## The same test on your own agent

Paste the question and the facts above into a fresh conversation and compare. If the reply looks like the first version, install the profile (`INSTALL.md`). If it looks like the second, you are done.
