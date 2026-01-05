# Large File Notice

The `Actuarial_analysis1.gif` file (133MB) was removed due to GitHub's 100MB file size limit.

## To restore the file:

1. Ensure Git LFS is set up (already done in this repo)
2. Copy the original GIF file to this directory:
   ```bash
   cp /path/to/original/Actuarial_analysis1.gif docs/use-cases/actuarial-analysis-solution/images/
   ```
3. Add and commit:
   ```bash
   git add docs/use-cases/actuarial-analysis-solution/images/Actuarial_analysis1.gif
   git commit -m "Add large GIF using Git LFS"
   git push origin main
   ```

The file will be stored in Git LFS and won't cause push failures.
