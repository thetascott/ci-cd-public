# GitHub Actions CI-CD pipeline 

Set up a CI/CD pipeline for a PHP project that extracts PDF metadata and exports it to an Excel spreadsheet. The pipeline includes:

	•	Dockerized environment for hosting the PHP program.
	•	A self-hosted runner.
	•	PHPStan for static analysis.

The contents of the GitHub Actions YAML file are included below:

```
name: PHP Composer

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read

jobs:
  build:

    runs-on: [ci-cd]

    steps:
    - uses: actions/checkout@v4

    - name: Validate composer.json and composer.lock
      run: composer validate 

    - name: Cache Composer packages
      id: composer-cache
      uses: actions/cache@v3
      with:
        path: vendor
        key: ${{ runner.os }}-php-${{ hashFiles('**/composer.lock') }}
        restore-keys: |
          ${{ runner.os }}-php-

    - name: Install dependencies
      run: composer install --prefer-dist --no-progress

    - name: PHPStan Scan
      uses: php-actions/phpstan@v3
      with:
         command: /ci-cd/vendor/bin/phpstan analyse -c /ci-cd/phpstan.neon

    - name: Deploy
      uses: actions/cache@v3
      env:
        EMAIL_KEY: ${{ secrets.EMAIL_KEY }}
```
<p>
The following displays a completed build:
</p>
<img width="856" alt="Screenshot 2024-11-09 at 10 49 16 PM" src="https://github.com/user-attachments/assets/d639d1d7-af69-4483-8515-86ee969bab60">
