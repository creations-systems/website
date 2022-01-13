# 200 - 🚀 Deploy

The second GitHub action deploys the site. First, it determines whether the changes are deployable to begin with. If the only thing that changed was content, then there's no reason to bother redeploying thanks to the refresh content action. The vast majority of my commits on my old site were content-only changes so this helps save the trees 🌲🌴🌳

Once it's determined that we have deployable changes, then we kick off multiple steps in parallel:

- ⬣ ESLint: Linting the project for simple mistakes
- ʦ TypeScript: Type checking the project for type errors
- 🃏 Jest: Running component and unit tests
- ⚫️ Cypress: Running end-to-end tests
- 🐳 Build: Building the docker image

The Cypress step is further parallelized by splitting my E2E tests into three individual containers which split the tests between them to run them as quickly as possible.

You might notice that there are no arrows coming out of the Cypress step. This is intentional and temporary. Currently I do not fail the build if the E2E tests fail. So far, I haven't been worried about deploying something that's broken and I didn't want to hold up a deploy because I accidentally busted something for the 0 users who expect things to be working. The E2E tests are also the slowest part of the deployment pipeline and I want to get things deployed quickly. Eventually I'll probably care more about whether I break the site, but for now I'd rather have things deploy faster. I do know when those tests fail.

Once ESLint, TypeScript, Jest, and the Build all successfully complete, then we can move on to the deploy step. On my end this bit is simple. I simply use the Fly CLI to deploy the docker container that was created in the build step. From there Fly takes care of the rest. It starts up the docker container in each of the regions I've configured for my Node server: Dallas, Santiago, Sydney, Hong Kong, Chennai, and Amsterdam. When they're ready to receive traffic, Fly switches traffic to the new instance and then shuts down the old one. If there's a startup failure in any region, it rolls back automatically.

Additionally, this step of the deploy uses prisma's migrate feature to apply any migrations I've created since the last migration (it stores info on the last migration in my Postgres DB). Prisma performs the migration on the Dallas instance of my Postgres cluster and Fly automatically propagates those changes to all other regions instantly.

And that's what happens when I say: git push or click the "Merge" button 😄
