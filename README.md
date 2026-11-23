# ML-Arena challenge template

Keep an [ML-Arena](https://ml-arena.com) challenge in a GitHub repository: every
commit on `main` is pushed to the platform. No API key, no secret in the
repository.

This template is a working **file challenge**: participants upload a
`submission.csv` of house prices, `env.py` scores it by RMSE. Push it as it is
first, see it work, then make it yours.

> `y_test.csv` is the ground truth. Keep this repository **private**.

**Do you need a repository?** Not for a plain CSV challenge: in the
[Studio](https://ml-arena.com/studio), a file challenge's **Scoring** tab builds
the same thing from a form (ground truth, metric, baseline), with no code. Use
this template when you want to write the scoring yourself in Python, or keep the
challenge under version control.

## Where things happen

| Where | What you do there |
|---|---|
| This repository | Write the challenge: `env.py`, the data, `challenge.toml`, `overview.md` |
| GitHub Actions | Each commit on `main` sends the folder to ML-Arena (`.github/workflows/push.yml`) |
| [ML-Arena Studio](https://ml-arena.com/studio) | Create the challenge, test it, start it, follow it. Its tabs: **Overview** (readiness checklist, then how it is going), **Page** (what participants read), **Scoring** (the files, the ground truth, the benchmark), **Results**, **Settings** |

## Before you start

- **Creator access.** Creating a challenge needs creator access on your
  ML-Arena account today. In the Studio, **New** lists *Create a challenge*
  with its kinds; if it says *Creating challenges is not open to your account
  yet*, ask for access through [ml-arena.com/contact](https://ml-arena.com/contact).
  (A course author without it can still add short-answer and multiple-choice
  exercises to a course, from the course's **Content** tab.)
- **GitHub connected.** On ML-Arena, **Profile → GitHub → Connect GitHub**,
  once. A repository you do not own (an organisation's, a class repository)
  must also be added under **Profile → GitHub → Trusted repositories**.

## Set up (once)

1. On GitHub, click **Use this template** and make the new repository private.
2. In the [Studio](https://ml-arena.com/studio): **New → Create a challenge →
   Upload a file**, give it a name. You land on its workspace,
   `ml-arena.com/studio/challenges/<id>/overview`: note the **id**, the number
   in that address (also under **Settings → Code and automation → About**).
3. In `.github/workflows/push.yml`, replace `your-challenge-id` with that
   number, then commit to `main`.
4. Open the run on the repository's **Actions** tab. Its **summary** lists the
   files sent, the `challenge.toml` keys applied and the check of `env.py`. An
   invalid `challenge.toml` or `env.py` fails the run with the platform's
   message, and nothing changes.

To check a change without sending it, run the workflow from the **Actions** tab
(*Run workflow*): `dry_run` is ticked by default. Pull-request runs are refused by
the platform, so there is no check on pull requests.

## From the first push to a started challenge

All in the challenge's Studio workspace:

1. **Scoring.** *Files* holds `env.py` and its data files; *Ground truth*
   shows the columns the platform read from `y_test.csv`. The scoring form
   says it is read-only: expected, your `env.py` decides the score. Edit the
   code in the repository, not in the Studio: the next push replaces the files.
2. **Scoring → Benchmark.** Drop `sample_submission.csv` (it is stored under
   the participants' file name) and click **Run test**. A passing test with a
   score is required to start; the benchmark is your baseline and ranks on no
   board.
3. **Page.** Write the card blurb (*Name and card blurb*). Upload a cover
   image here or put `image.png` in the repository. Under *Data participants
   download*, add what participants need, such as `sample_submission.csv`:
   the files of this repository are the scoring files, and participants never
   see them.
4. **Overview.** The readiness checklist lists what still blocks **Start**,
   each item linked to where it is fixed. Click **Start**.
5. **Settings → Visibility.** A challenge starts private. Share its link, make
   it public, or add it to a course from the Studio's **New → Run a challenge
   with my class**.

## Make it yours

| File | What to change |
|---|---|
| `y_test.csv` | Your ground truth. **Keep this name**: the platform reads `y_test.csv` to check every upload before it runs (the same header, the first column as the id, the same ids) and will not start without it. The check can be turned off under **Scoring → Ground truth** |
| `env.py` | The scoring. Keep `class Env` and `evaluate(self, submission_path)`; change the column names, the metric, the error messages |
| `challenge.toml` | The metric's `key`, `label` and `order` (`"asc"`: smaller ranks first; `"desc"`: larger), the submission file name, the limits. A key written here is read-only in the Studio; a key left out stays the Studio's |
| `sample_submission.csv` | A valid submission: your benchmark and the participants' example |
| `overview.md` | The challenge page's text: the task, the file format, the metric |
| `test_local.py` | `EXPECTED_SCORE`: the range your sample submission must score in |

**What `evaluate` returns.** A dict with one row in `agent_results`:

- a scored submission: `{"agent_index": 0, "score": <float>, "info_message": "…"}`.
  `score` is the value of the metric whose `source` is `"score"` in
  `challenge.toml`;
- a submission in the wrong shape: `{"agent_index": 0, "is_agent_code_error": True,
  "agent_code_error_message": "…"}`. The participant sees the message, and the
  failure is theirs. An exception raised in `env.py` is counted as the
  challenge's failure, not the participant's.

`python test_local.py` scores `sample_submission.csv` with `env.py` on your
machine, without installing anything; the workflow runs it before each push.

## Folder layout

| File | Becomes |
|---|---|
| `env.py` | The challenge's scoring: `class Env` with `evaluate(self, submission_path)`. Required |
| `challenge.toml` | The config: submission file name, metric, limits |
| `overview.md` | The challenge page's overview |
| `image.png` (or `.jpg`, `.jpeg`, `.webp`) | The challenge's cover image. Optional |
| `agent.py` | Code challenges only: the agent template participants start from. A file challenge refuses it |
| anything else (`y_test.csv`, `sample_submission.csv`, sub-folders) | Scoring files, next to `env.py`. Never shown to participants |
| `README.md`, `LICENSE`, `test_local.py`, `.github/`, `.gitignore` | Stay in the repository |

The repository is the whole set of scoring files: a file deleted here is deleted
on the platform at the next push.

## Changing a started challenge

A push still works once the challenge is started:

- Changed scoring files are an **update**: the platform holds new runs, waits
  for the running ones to end, puts the new files in place, then runs the
  benchmark on them first. If the benchmark fails, the previous files come back
  by themselves. The run's summary shows the update's state.
- Submissions already scored keep their scores. To score them again with the
  new files, use **Update scoring** in the challenge's Studio header.
- The metric and the submission file name are locked while it runs: a push that
  changes them in `challenge.toml` is refused. `overview.md`, the image and the
  keys still editable while running (a metric's label, the limits) apply at once.
- On a GPU (`vm`) machine, changed scoring files are refused while the
  challenge runs: stop it first.

## Several challenges in one repository

Give each challenge a folder and one step per challenge:

```yaml
      - uses: ml-arena/mlarena-action@v1
        with:
          command: challenge-push
          challenge: 123
          path: challenges/house-prices
```

All the options: [ml-arena/mlarena-action](https://github.com/ml-arena/mlarena-action).
The same push from your machine, with an API key:
`mlarena challenge push . --challenge 123 --dry-run`.
