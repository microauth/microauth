# microauth
> Collection of authentication modules for zeit's micro.

<p align="center">
  <img src="https://github.com/microauth/microauth/blob/397dcd6f03d1620408a9607c552113208e1bee3c/media/logo.png" alt="covr" />
</p>

## List of modules

 - [microauth-twitter](https://github.com/microauth/microauth-twitter)
 - [microauth-github](https://github.com/microauth/microauth-github)
 - [microauth-facebook](https://github.com/microauth/microauth-facebook)
 - [microauth-slack](https://github.com/microauth/microauth-slack)
 
 More providers, examples and documentation soon.

## Usage example

Composing several `microauth` modules:

```js
const { send } = require('micro');
const compose = require('micro-compose');
const microAuthSlack = require('microauth-slack');
const microAuthGithub = require('microauth-github');

const githubOptions = {
  clientId: 'client_id',
  clientSecret: 'cleint_secret',
  callbackUrl: 'http://localhost:3000/auth/github/callback',
  path: '/auth/github',
  scope: 'user'
};

const slackOptions = {
  clientId: 'client_id',
  clientSecret: 'client_secret',
  callbackUrl: 'http://localhost:3000/auth/slack/callback',
  path: '/auth/slack',
  scope: 'identity.basic,identity.team,identity.avatar'
};

const slackAuth = microAuthSlack(slackOptions);
const githubAuth = microAuthGithub(githubOptions);

const handler = async (req, res, auth) => {

  if (!auth) {
    return send(res, 404, 'Not Found');
  }

  if (auth.err) {
    console.error(auth.err);
    return send(res, 403, 'Forbidden');
  }

  if (auth.result.provider === 'github') {
    return `${auth.result.provider} provider. Hello ${auth.result.info.login}`;
  } else if (auth.result.provider === 'slack') {
    return `${auth.result.provider} provider. Hello ${auth.result.info.user.name}`;
  } else {
    return 'Unknown provider';
  }

};

module.exports = compose(
  slackAuth,
  githubAuth
)(handler);

```

Now go to `http://localhost:3000/auth/github` for github authentication or go to `http://localhost:3000/auth/slack` for slack authentication.

