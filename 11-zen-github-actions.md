# The Zen School of Github Actions

2022-05-11 18:00
Jonathan Ruckwood
Open Creedo
@jon-ruckwood

## Github Actions are the opposite of Zen

To follow up: The Ogmios School of Zenmotoring

- Frustrating, unpredictable and unforgiving.

However this is a familiar experience with CI tools. You have a long feedback cycle from seeing what you've changed.

## Why?

- Low Friction
- Benefits smaller teams
- Thriving eco-system
- Well documented

Good discoverability with the market place tools.

## Build a Maven Project

Commit -> Checkout -> Setup Java 17 -> Maven Build -> Upload Test Results

GitHub actions gives you a declarative YAML file in .github/workflows/build.yaml.

## Tips

### Caching

Save on compute and your team. It's free and get you get up to 10GB per repository.
The oldest entries get evicted first, unused ones also get evicted.
Advanced usage: actions/cache@v3
    - For more nuanced or specific use cases.

Avoid doing the round trips to the internet.

```yaml
- uses: action/setup-java@v3
  with:
    java-version: 17
    distribution: 'zulu'
    cache: 'maven'
```

The other main use case would be to cache with Docker. This means that if you're building an image with layering. If you're only changing the source code everything else can be retrieved from cache.

This can all be enabled by default but it's worth profiling to make sure you're seeing the improvements.

### Limits

You want to minimise the execution of a job or a step. If tests overrun or theres issues with GitHub actions, you need to put in tighter limits to get the feedback faster.

Concurrency. You can use a fixed key or a dynamic key.

- Fixed keys lets only one instance of a workflow run at once.
- A key with an expression key let's you do things like make it per PR for example (ci-${{github.ref}})

You can have situation with race conditions. If several PRs are merged in together you might miss which tag the docker image got. So be careful!

### Reuse & Composability

- You can use a local action, defined in the repository. This is shared amongst workflows in your repository.
- A composite action is a sequence of actions you want to reuse.
- You can publish actions publicly or within an organisation.
- Reuse workflow from a different repository
    - workflow_call

### Debug

Add these secrets with value true:

- ACTIONS_RUNNER_DEBUG
- ACTIONS_STEP_DEBUG

This gives you additional outputs (if the authors of the actions have been helpful)

### Act

Allows you to run your workflows locally.

```
act 
act pull_request
act -j teset
act -n
act -s GITHUB_TOKEN=...
```

Ultimately no substitution but can help you catch some issues early.

## The Design of the Workflows

We need to put as much time and effort into our CI actions as we do with normal code.

Keep It Simple Stupid.

- Eliminate complexity where you can and try not to be too clever.
- Keep moving parts to an absolute minimum

Don't bake in too much. Use external scripts and lean on build tools like the Spring Boot plugin.

## You need a backup plan!

There have been issues with GitHub actions. Have a run-book for doing the essential things your workflow(s) do but performed manually.

Print this out or document it in a wiki.

## Treat your repo as an API

Particularly if you have to do cross-repo calls.

Treat the workflow as the interface

- Ask a repository to perform action - don't do it yourself!

Chaining workflows across repositories

- repository_dispatch & workflow_dispatch

We might have a service repo where the application lives and then an operations repo with the kubernetes configuration. We might want to have a workflow in the service repo to build the image, and then push the version to the operations repo to deploy that version and put it onto the cluster.

### Coupling - Beware!

- Cross-repository invocation
- Shared actions
- Shared workflows

These are good as a stop gap but do find a better solution. Avoid premature optimisation. Some duplication is okay before you start centralising things.

## Security

- Do not trust the marketplace
- You **must** follow GitHub's security hardening guide.
- Review your repository settings
    - Don't allow workflows from fork PRs.
        - In that case, people will have access to your secrets.

You can pin an action to a commit hash. Tags can move and don't have a history.

`example/action@$shacode` instead of `example/action@v4.0.2`

Limit GITHUB_TOKEN permissions for the running workflows. Set default for repository and GitHub organisation.

Use a "Serivce Account" for things the GITHUB_TOKEN cannot do. This is useful for cross-repository stuff. Instead of a personal access token use a service account.

Avoid long lived secrets for interacting with things like AWS
    - OIDC provider for temporary session tokens

Really security is a whole talk in and of itself.

"How to use GitHub Actions with security in mind" - Rob Bos

Supply chain attacks are a growing threat.

## Summary

Understand there will be pain.
Don't sweat the small stuff.

github.com/jon-ruckwood/devoxx-2022 on GitHub.
