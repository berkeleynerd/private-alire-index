# Private Alire Index

This repository is Rebecca's private [Alire](https://alire.ada.dev/) crate
index. It follows Alire index format `1.4.0` and supplements the public Alire
community index with development releases used by private projects.

The private entries currently include:

- `common=0.1.0-dev`
- `language=0.1.0-dev`
- `lsp_client=0.1.0-dev`
- `shared=0.1.0-dev`

Access to this index does not automatically grant access to a crate's source
repository. Your Git credentials must be authorized for both this repository
and every private origin referenced by a crate manifest.

## Configure Alire

### 1. Set up GitHub SSH access

Add an SSH key to the GitHub account that has access to this repository, then
verify authentication:

```sh
ssh -T git@github.com
```

GitHub reports that it does not provide shell access even when authentication
succeeds; that message is expected.

### 2. Add the private index

Give the private index precedence over the community index so private releases
win if the same crate and version occur in both:

```sh
alr index \
  --add=git+ssh://git@github.com/berkeleynerd/private-alire-index.git \
  --name=private \
  --before=community
```

This changes the current user's Alire configuration. Confirm the result and
look up a private crate:

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
alr index --del=private
```

Adding the index again is safe only after removing an existing entry with the
same name.

## Add or update a private crate

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

- Prefer a read-only SSH deploy key or a dedicated machine-user key.
- Populate `known_hosts` from a verified GitHub host key before cloning.
- Never put personal access tokens, passwords, or deploy keys in crate
  manifests, repository URLs, workflow files, or Alire settings committed to
  source control.
- Grant credentials only to this index and the private crate origins required
  by the build.

## Synchronize community metadata

This repository contains community-index metadata as its base. When refreshing
that base, merge only a community branch compatible with the version declared
in [`index/index.toml`](index/index.toml), resolve conflicts without dropping
the private manifests, and run `alr index --check` before pushing.

Upstream Alire documentation is available at
[alire.ada.dev](https://alire.ada.dev/).
