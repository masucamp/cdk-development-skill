# Debugging CI Errors

## GitHub Actions

### View failed workflow run logs
```bash
gh run list --limit 5
gh run view <RUN_ID> --log-failed
```

### Search for error markers
```bash
gh run view <RUN_ID> --log | grep -i "error" | head -20
```

### Getting Run ID

From the GitHub Actions URL:
```
https://github.com/<owner>/<repo>/actions/runs/12345678
                                                ^^^^^^^^
                                                RUN_ID
```

Or list recent runs:
```bash
gh run list --branch <BRANCH_NAME> --limit 5
```

### Re-run failed jobs
```bash
gh run rerun <RUN_ID> --failed
```
