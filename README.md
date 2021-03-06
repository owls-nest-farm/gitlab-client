# GitLab Client

## What does this do?

The goal of this project is a simple one: quickly and easily create as many `GitLab` projects as needed within an **existing** group.

## What does this support?

This project **only** supports the *creation* of the following `GitLab` objects:

- [Projects]
- [Branches]
- [Invites]
- [Issues]
- [Labels (project)]
- [Merge Requests]
- [Releases]
- [Wikis]
- [Subgroups]

Its intent is to **only** create them with **only** the required configuration parameters.  The reasoning is to support its only use case, which is just to "bootstrap" one or more projects with all the fixings.  It doesn't really matter what the data **is**, as long as it is **there**.

If you need something more customized, you'll have to do that yourself.

## Requirements

- [`GitLab` API token] with `api` scope
- A pre-existing group
    + The current API has [problems creating new groups]

## Notes

- To keep it simple, the given project's `name` will also be used as its `path`.
    + https://docs.gitlab.com/ee/api/index.html#namespaced-path-encoding

- All projects are created from a default template.
    + https://gitlab.com/gitlab-org/project-templates

- Sending invites will automatically add the invitee as a member to the project, if they have already created a `GitLab` account.  Otherwise, the invite will be pending.

- Both `yaml` and `json` data formats are supported.

## Fields

### Creating [Projects]

A list of `Project` objects composed of:

- `name` (string)
- `tpl_name` (string)
- `visibility` (string)
- [`invites`](#invites) (list of `Invites`)
- [`issues`](#issues) (list of `Issues`)
- [`releases`](#releases) (list of `Releases`)

### [`branches`]

A list of project `branches` objects composed of:

- `branch` (string)
- `ref` (string)

### [`invites`]

A list of `Invite` objects composed of:

- `access_level` (string)
    + These values mostly map directly to the [Members API values].
        - `None`
        - `Minimal`
        - `Guest`
        - `Reporter`
        - `Developer` (default)
        - `Maintainer`
        - `Owner`
- `email` (string)

### [`issues`]

A list of `Issue` objects composed of:

- `title` (string)
- `type` (string)
    + These values map directly to the `GitLab` API values.
        - `Incident`
        - `Issue` (default)
        - `TestCase`

### [`labels`]

A list of project `Label` objects composed of:

- `name` (string)
- `color` (string)
- `description` (string)
- `priority` (int)

### [`merge_requests`]

A list of project `MergeRequests` objects composed of:

- `source_branch` (string)
- `target_branch` (string)
- `title` (string)

### [`releases`]

A list of `Release` objects composed of:

- `name` (string)
- `ref` (\*string)
- `tag_name` (string)
- `description` (string)
- `milestones` ([]string)
- `assets` ([`gitlab.ReleaseAssetsOptions`])
- `released_at` ([`time.Time`])

### [`wikis`]

- `title` (string)
- `content` (string)

> For full examples in both `yaml` and `json`, see the `examples/` directory.

### [`subgroups`]

To create a subgroup, first make sure that the parent group has been defined.  The subgroup will reference its parent group through the `parent` field, so this ordering is a necessary requirement.

This method of referencing a parent was chosen to avoid nesting in the subgroups which would make the data formats harder to read and understand at a glance.

```
...
[
    {
        "group": "gl-group",
        "projects": [
            {
                ...
            }
        ]
    {
    },
    {
        "group": "gl-subgroup",
        "parent": "gl-group",
        "projects": [
            {
               ...
            }
        ]
    }
]
```

## Examples

There are several examples in the `./examples` folder of configs in both `json` and `yaml` formats, both neither are exhaustive.  They should, however, give a good idea of how the configuration should be structured.

### Creating Projects

```
$ gitlab-client -file examples/gitlab.yaml
$ gitlab-client -file examples/gitlab.json
```

### Deleting Projects

To teardown what was setup when creating the projects, simply pass the same config file with the `-destroy` flag.

Or, pass another file or make your changes in the same one.  Pick your poison.

```
$ gitlab-client -file examples/gitlab.yaml -destroy
```

> This will **not** ask for confirmation.

## Testing

If you don't want to compile, you can use `go run`:

```
$ go run *.go -file examples/gitlab.yaml
$ go run *.go -user btoll
```

## Acknowledgments

This project uses the [`go-gitlab`] client library.

[Projects]: https://docs.gitlab.com/ee/api/projects.html
[Branches]: https://docs.gitlab.com/ee/api/branches.html
[`branches`]: https://docs.gitlab.com/ee/api/branches.html
[Invites]: https://docs.gitlab.com/ee/api/invitations.html
[`invites`]: https://docs.gitlab.com/ee/api/invitations.html
[Issues]: https://docs.gitlab.com/ee/api/issues.html
[`issues`]: https://docs.gitlab.com/ee/api/issues.html
[Labels (project)]: https://docs.gitlab.com/ee/api/labels.html
[`labels`]: https://docs.gitlab.com/ee/api/labels.html
[Merge Requests]: https://docs.gitlab.com/ee/api/merge_requests.html
[`merge_requests`]: https://docs.gitlab.com/ee/api/merge_requests.html
[Releases]: https://docs.gitlab.com/ee/api/releases/
[`releases`]: https://docs.gitlab.com/ee/api/releases/
[Wikis]: https://docs.gitlab.com/ee/api/wikis.html
[`wikis`]: https://docs.gitlab.com/ee/api/wikis.html
[Subgroups]: https://docs.gitlab.com/ee/user/group/subgroups/
[`subgroups`]: https://docs.gitlab.com/ee/user/group/subgroups/
[`GitLab` API token]: https://docs.gitlab.com/ee/security/token_overview.html
[problems creating new groups]: https://gitlab.com/gitlab-org/gitlab/-/issues/244345
[Members API values]: https://docs.gitlab.com/ee/development/permissions.html#members
[`go-gitlab`]: https://github.com/xanzy/go-gitlab
[`gitlab.ReleaseAssetsOptions`]: https://pkg.go.dev/github.com/xanzy/go-gitlab#ReleaseAssetsOptions
[`time.Time`]: https://pkg.go.dev/time#Time

