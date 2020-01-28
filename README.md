GitLab CI YAML files containing useful template pipelines
=========================================================

As we built out the GitLab CI infrastructure for pyQuil and the Forest SDK, soon there was a lot
of repeated YAML code so that each individual repository could run Python- or Lisp-based tests,
and produce Docker images for all commits, pull requests, and releases. Fortunately, GitLab CI
YAMLs contain the useful `include` and `extends` keywords, which allow for template jobs to be 
defined in separate files, and for the jobs in individual project YAMLs to inherit from them.
Therefore, this repository was created to house these template YAMLs.

To take advantage of the Docker-related job templates, add the following to the top of the
`gitlab-ci.yml` for the target repository:

```yaml
include:
  - project: rigetti/forest/gitlab-pipelines
    file: docker.gitlab-ci.yml
```

**NOTE**: There is an additional `forest` directory included in the project path, as the
GitLab mirror version of the repository is used by the `include` keyword.

To use the Docker-related templates (`.docker-branch`, `.docker-edge`, `.docker-stable`), add
the following entries to the YAML:

```yaml
docker-branch:
  extends: .docker-branch

docker-edge:
  extends: .docker-edge

docker-stable:
  extends: .docker-stable
```

In addition to providing those job entries, the jobs themselves depend on the existence of an
`IMAGE` variable to operate correctly. This name of the desired Docker image, which will be
built as part of the CI pipeline. For example, pyQuil has the following `variables`:

```yaml
variables:
  IMAGE: rigetti/forest
```

To also take advantage of the Python-related job templates, add the following additional entry
to the `include` section of the `gitlab-ci.yml`:

```yaml
include:
  - project: rigetti/forest/gitlab-pipelines
    file: docker.gitlab-ci.yml
  - project: rigetti/forest/gitlab-pipelines
    file: python.gitlab-ci.yml
```

The `.python` template pulls out the shared installation commands into a `before_script` section,
and assumes that your project has a `Makefile` with `install` and `requirements` targets defined.
See the `Makefile` in the pyQuil project for more information.
