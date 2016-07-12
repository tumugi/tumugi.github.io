![logo](./images/logo.png)

## What is tumugi ?

Tumugi is a ruby library to build, run and manage complex workflows. Tumugi enables you to define workflows as a ruby code.

tumugi has following features:

- Define workflow using internal DSL, it means just ruby code
- Scheduling task from the given dependencies
- Parallel task execution on a local machine
- Error handling and retry
- Small core and enhance menbe by plugins released on RubyGems.org

tumugi has not following features:

- Distribution processing, it run in a local machine
- Scheduler, so you need to run tumugi cron, jenkins and so on.

## Table of Contents

- [Getting Started](getting_started)
- [Architecture](architecture)
- Workflow Examples
    - [Recipe1: Download file and save it as local file](recipe1)
- [List of tumugi plugins](plugins)

## Releases

You can find releases in [RubyGems.org](https://rubygems.org/gems/tumugi) and can see changelogs at [HERE](https://github.com/tumugi/tumugi/blob/master/CHANGELOG.md).

Latest version is [v0.6.1](https://github.com/tumugi/tumugi/blob/master/CHANGELOG.md#061-2016-07-11)
