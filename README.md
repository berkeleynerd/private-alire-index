# Supplemental Alire Index

This repository is Rebecca's publicly readable, independently maintained
[Alire](https://alire.ada.dev/) crate index. It follows Alire index format
`1.4.0` and supplements the official Alire community index with development
releases used by local projects.

This index is not submitted to or published through the official Alire
community index. It is still a public GitHub repository and may be discoverable
through GitHub profiles, search, APIs, and activity.

The locally maintained entries currently include:

| Crate | GitHub source | Gitea mirror |
| --- | --- | --- |
| `common=0.1.0-dev` | [`berkeleynerd/common`](https://github.com/berkeleynerd/common) | [`rebecca/common`](https://git.idril.is/rebecca/common) |
| `language=0.1.0-dev` | [`berkeleynerd/language`](https://github.com/berkeleynerd/language) | [`rebecca/language`](https://git.idril.is/rebecca/language) |
| `lsp_client=0.1.0-dev` | [`berkeleynerd/lsp_client`](https://github.com/berkeleynerd/lsp_client) | [`rebecca/lsp_client`](https://git.idril.is/rebecca/lsp_client) |
| `shared=0.1.0-dev` | [`berkeleynerd/shared`](https://github.com/berkeleynerd/shared) | [`rebecca/shared`](https://git.idril.is/rebecca/shared) |

The index and all four source repositories are publicly readable. No GitHub or
Gitea authentication is required to fetch them.

## Configure Alire

### Add the supplemental index

Give this index precedence over the community index so its releases win if the
same crate and version occur in both:

```sh
alr index \
  --add=git+https://github.com/berkeleynerd/private-alire-index.git \
  --name=berkeleynerd \
  --before=community
```

This changes the current user's Alire configuration. Confirm the result and
look up a locally maintained crate:

```sh
alr index --list
alr show common=0.1.0-dev
```

To fetch a crate into the current directory:

```sh
alr get common=0.1.0-dev
```

## Maintain the local index

Pull all configured repository-backed indexes:

```sh
alr index --update-all
```

Check the configured indexes for invalid or unknown metadata:

```sh
alr index --check
```

Remove this index from the local Alire configuration:

```sh
alr index --del=berkeleynerd
```

Adding the index again is safe only after removing an existing entry with the
same name.

## Add or update a locally maintained crate

1. Make sure the source repository is accessible to every intended user and CI
   runner.
2. Use an immutable commit hash in the manifest's `[origin]` section. Do not
   rely on a mutable branch name.
3. Store the manifest at:

   ```text
   index/<first-two-letters>/<crate>/<crate>-<version>.toml
   ```

   For example, `widget=1.2.3` belongs at
   `index/wi/widget/widget-1.2.3.toml`.
4. Start from [`templates/skeleton.toml`](templates/skeleton.toml) or a similar
   existing manifest, and fill in the crate metadata, dependencies, and origin.
5. Run `alr index --check`, then verify the exact release with
   `alr show <crate>=<version>` and, where practical, fetch it with
   `alr get <crate>=<version>`.
6. Commit the manifest and open a pull request in this repository. Keep one
   crate release per commit when practical.

When updating an existing development release, prefer publishing a new version.
If the version must remain unchanged, update its pinned origin commit and make
sure all consumers run `alr index --update-all` before resolving dependencies.

## CI and credential safety

- The index and its current crate origins can be cloned anonymously over HTTPS.
- If a private origin is added later, prefer a read-only SSH deploy key or a
  dedicated machine-user key.
- When using SSH, populate `known_hosts` from a verified GitHub host key before
  cloning.
- Never put personal access tokens, passwords, or deploy keys in crate
  manifests, repository URLs, workflow files, or Alire settings committed to
  source control.
- Grant credentials only to private crate origins required by the build.

## Synchronize community metadata

This repository contains community-index metadata as its base. When refreshing
that base, merge only a community branch compatible with the version declared
in [`index/index.toml`](index/index.toml), resolve conflicts without dropping
the locally maintained manifests, and run `alr index --check` before pushing.

Upstream Alire documentation is available at
[alire.ada.dev](https://alire.ada.dev/).
