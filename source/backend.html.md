---
title: Backend ECMS

toc_footers:
  - copyright <a href="https://www.ggtech.global" target="_blank">GGTech</a> 2021 ©

search: true

code_clipboard: true
---

# Introduction

Backend system its distributed in 4 microservices:

* [Auth](#Auth-ms)
* [Global API](#Global-API)
* [Competition](#Competition-ms)
* ~~Socket Sever~~ (WIP)

All modules use same structure and requirements

# Getting Started
## Requirements

* [NodeJs](https://nodejs.org) >=14.15.1
* [Typescript](https://www.typescriptlang.org)
* [Yarn](https://yarnpkg.com)
* Gitlab Personal token for install [ECMS Packages](packages.html)

## Build and deploy requirements

* [Docker](https://docker.com) 19^
* [AWS CLI](https://aws.amazon.com/cli/)
* [KubeCtl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Require configurate `aws cli` and `kubectl`

## Scripts

### Install

To install require `NPM_TOKEN` enviroment var in your system, to do this, you can run `sh .hooks/npm-token`

```bash
yarn install
```

### Run

To run the server you need configure your [enviroment](#enviroment) vars

> Dev

```bash
yarn dev
```

> Production

```bash
yarn build

yarn start
```

## Enviroment

Duplicate `.env.example` file and rename `.env`

The `.env` file readed by package [`@ggtech/ecms-utils`](packages.html#Utils-package)

```bash
NODE_ENV=development
PORT=3000
DEBUG=app:*
MONGO_CLUSTER=
ADMIN_COLLECTION=
SENTRY_DSN=
SENTRY_ENV=
REDIS_SERVER=
REDIS_PWD=
S3_BUCKET=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=
CDN_URL=
SECRET_DB=
SIGN_DB=
```

> Use env vars

```typescript
import env, { isDev } from '@ggtech/ecms-utils/config'
```

## Files structure

```
|- /src
  |- index.ts # Server index
  |- bootstrap.ts # Use to set inital settings
  |- redis.ts # Initialize redis client
  |- /controllers
    |- /<admin | common | project>
      |- /<module> | <module>.ts # If the controller require modules, use a path to group this
  |- /routeRules
    |- /<admin | project>
      |- index.ts
      |- <module>.ts
  |- /routes
    |- index.ts
    |- /<admin | project>
      |- index.ts
      |- <module>.ts
```

# Controllers

## Database access

To keep databases synchronized with another services, has managment in packge [`@ggtech/ecms-db`](packages.html#database-package)

Work like:

> index.ts (server file)

```ts
import Database from '@ggtech/ecms-db';

const database = new Database();

await database.loadProjectsDBs();

global._adminDb = database.adminDBl
global._dbs = database.databases;
```

With the `database` you have access to `adminDB` and `projctsDBs`, `projectsDBs` it's a `Map` and the projects slug it's the key to get access to the db connection. You have access to this objets in node global.

```ts
const db = global._dbs.get(<key>);
```

### Access to models

Exist two types of database

* Admin DB
* Project DB

This because they have a direferents models

> Admin DB

```ts
interface AdminDB extends Connection {
  models: {
    adminUser: IAdminUserModel;
    credential: IAdminCredentialModel;
    project: IProjectModel;
    expiredToken: IExpiredTokenModel;
    ...
  }
}
```

> Project DB

```ts
interface ProjectDB extends Connection {
  models: {
    user: IUserModel;
    expiredToken: IExpiredTokenModel;
    ...
  }
}
```

### Work with models

The middleware `scope` search a project db by the header scope (The middleware are in [`@ggtech/ecms-utils`](packages.html#utils-package)).
The middleware set the database in `Request` param of `express`, in the function of request you have access to the connection.

> Work with models example:

```ts
route.get('/', (req: Request, res: Response) => {
  try {
    const db = req.isAdmin ? req.adb : req.db;

    // access to model
    db.models.<name>.<method>...;
  } catch (err) {
    ...
  }
});
```

## Controller method

> `src/controllers/admin/faq.ts`

```typescript
import { handleError } from '@ggtech/ecms-utils/errorHandler'

async function createFaq(req: Request, res: Response): Promise<Response | void> {
  try {
    const body: IFaq = req.body
    const Faq: FaqModel = req.db.models.faq
    const faq: IFaq = await Faq.createFaq(body)

    return res.status(200).send({ faq })
  } catch (err) {
    return handleError(err, res)
  }
}

export {
	createFaq
}
```

## Validate body request

To validate body request use the package [`express-validator`](https://express-validator.github.io/docs/) using the middleware [`validateBody`](packages.html#validate-body) in [`@ggtech/ecms-utils`](packages.html#utils-package), add in controller file:

```typescript
import { body as Body } from 'express-validator'

const updateFaqFelds = [
  Body(['title', 'description']).optional().isString(),
  Body(['order', 'visibility']).optional().isNumeric()
]
const createFaqFields = [
  ...updateFaqFelds,
  Body(['title', 'visibility', 'description'], GeneralErrors.fieldRequired).exists()
]

export {
  ...
  updateFaqFelds,
  createFaqFields
}
```
