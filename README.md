# ML-Arena challenge template

Keep an [ML-Arena](https://ml-arena.com) challenge in a repository: every commit
on `main` is pushed to the platform. No API key, no secret in the repository.

This template is a working **file challenge**: participants upload a
`submission.csv` of house prices, `env.py` scores it by RMSE.

> `y_test.csv` is the ground truth. Keep this repository **private** (private
> repositories work the same way).

## Setup

1. Click **Use this template** (private).
2. On ML-Arena, **create the challenge once** in the console, as a *File
   challenge*, and copy its slug: the end of its URL
   (`ml-arena.com/challenges/<slug>`).
3. Open your Profile and **connect your GitHub account**. A repository owned by an
   organisation must also be added under **Profile → Trusted repositories**.
4. In `.github/workflows/push.yml`, replace `your-challenge-slug`, then commit to
   `main`.

The run's **summary** lists what changed, the config keys set by
`challenge.toml` and the check of `env.py`. An invalid `challenge.toml` or
`env.py` fails the job with the platform's message.

To check without changing anything, run the workflow from the **Actions** tab
(*Run workflow*): `dry_run` is ticked by default. Pull-request runs are refused by
the platform, so there is no check on pull requests.

## Folder layout

| File | Becomes |
|---|---|
| `env.py` | The challenge's environment: `class Env` with `evaluate(self, submission_path)`. Required |
| `challenge.toml` | The config: submission file name, metric, limits. A key written here is read-only in the console; a key left out stays the console's |
| `overview.md` | The challenge page's text |
| `image.png` (or `.jpg`, `.jpeg`, `.webp`) | The challenge's image. Optional |
| `agent.py` | Code challenges only: the agent template participants start from. A file challenge refuses it |
| anything else (`y_test.csv`, `sample_submission.csv`, sub-folders) | Environment files, next to `env.py` |
| `README.md`, `LICENSE`, `test_local.py`, `.github/`, `.gitignore` | Stay in the repository |

The repository is the whole environment: a file deleted here is deleted on the
platform at the next push.

`python test_local.py` scores `sample_submission.csv` with `env.py` on your
machine; the workflow runs it before each push.

## Once the challenge is started

The environment is frozen, so that every submission is scored by the same code:

- a push that changes, adds or removes an environment file **fails**; stop the
  challenge first;
- `overview.md`, the image and the config keys that stay editable while a
  challenge runs (for example a metric's label) **still sync**.

## Several challenges in one repository

Give each challenge a folder and one step per challenge:

```yaml
      - uses: ml-arena/mlarena-action@v1
        with:
          command: challenge-push
          challenge: house-prices
          path: challenges/house-prices
```

All the options: [ml-arena/mlarena-action](https://github.com/ml-arena/mlarena-action).
