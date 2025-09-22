# Full Stack Open CI/CD

This repository is used for the CI/CD module of the Full Stack Open course.

## :warning: Notice! :warning:

While studying [the course](https://fullstackopen.com/en/part11),
I also applied the teachings on a
[personal side project](https://github.com/gustafla/zmarkgen),
which I developed alongside the course exercises.
This is because I'm not very keen on Javascript and node, and I wanted to
enjoy the process of using GitHub Actions for something I actually like.

## Commands

Start by running `npm install` inside the project folder

`npm start` to run the webpack dev server
`npm test` to run tests
`npm run eslint` to run eslint
`npm run build` to make a production build
`npm run start-prod` to run your production build

## Development workflow

The `main` branch has been protected so that all changes must be submitted via
a pull request instead of allowing for direct pushes.
CI workflows must pass before any PR can be merged.

Note: I had to turn off the requirement for code review, because I cannot review
my own changes (GitHub doesn't let me).
