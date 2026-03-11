# Printout Your Workout

A lightweight workout builder that lets you create, share, and print 
custom workout routines — no account required.

## How Sharing Works (No Database Required)

Workouts are serialized, gzip-compressed, and encoded into the URL 
itself using query parameters. Share a link and the full workout 
travels with it — no backend, no storage, no login.

## Features

- Build custom workout routines with your chosen exercises
- Share workouts via a single URL
- Print-optimized layout for taking your routine to the gym

## Live App

[printoutyourworkout.com](https://www.printoutyourworkout.com/)

## Tech Stack

- Svelte
- Vite
- AWS Amplify (CI/CD via `main` branch auto-deploy)
- pako (gzip compression)

## Running Locally
```bash
git clone https://github.com/joseph4tw/PrintoutYourWorkout
npm install
npm run dev
```
