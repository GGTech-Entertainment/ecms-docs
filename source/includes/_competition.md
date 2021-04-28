# ECMS Global API

## Description

Competition MS it's a microservice focused on competition process, create tournaments, lobbies, load statistics and more.

### Anothers repos of ECMS
- [Global API](https://git.ggtech.global/ggtech/esports-cms/global-api)
- [Auth MS](https://git.ggtech.global/ggtech/esports-cms/auth-ms)

### Packages
- [Database](https://git.ggtech.global/ggtech/esports-cms/database)
- [Utils](https://git.ggtech.global/ggtech/esports-cms/utils)

## Requirements

* [NodeJS](https://nodejs.org) ^14.15.1
* [Typescript](https://www.typescriptlang.org)
* [Yarn](https://yarnpkg.com)
* Your `SSH key` configured to grant access to private packages
* GitLab [Access Token](https://git.ggtech.global/profile/personal_access_tokens) (api, read_api, read_registry, write_registry)

### Build and deploy requirements

* [Docker](https://docker.com) 19^
* [AWS CLI 2](https://aws.amazon.com/cli/)
* [KubeCtl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Install and run

### Install

```sh
yarn install
```

### Run

#### Dev

```sh
yarn dev
```

#### Prod

```sh
yarn build

yarn start
```

## Enviroment vars

Duplicate file `.env.example` and rename `.env`

```env
NODE_ENV=
MONGO_CLUSTER=
ADMIN_COLLECTION=
PORT= 3000
DEBUG= app:*
SENTRY_DSN=
SENTRY_ENV=
REDIS_SERVER=
REDIS_PWD=
S3_BUCKET=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=
CDN_URL=
```

The `.env` file readed by package [`@ggtech/ecms-utils`](https://git.ggtech.global/ggtech/esports-cms/utils/-/blob/master/src/config.ts) and imported like:

```ts
import env from '@ggtech/ecms-utils/config'
```

If you need check if it's a `development` env, you can use

```ts
import { isDev } from '@ggtech/ecms-utils/config'
```

## Database

To keep databases synchronized with another services, has managment in packge [`@ggtech/ecms-db`](https://git.ggtech.global/ggtech/esports-cms/database)

Work like:
```ts
import Database from '@ggtech/ecms-db'

const database = new Database()

await database.loadProjectsDBs()

global._adminDb = database.adminDBl
global._dbs = database.databases
```

With the `database` you have access to `adminDB` and `projctsDBs`, `projectsDBs` it's a `Map` and the projects slug it's the key to get access to the db connection. You have access to this objets in node global.

```ts
const db = global._dbs.get(<key>)
```

### Access to models

Exist two types of database
>  * Admin DB
>  * Project DB

This because they have a direferents models

>#### Admin DB
>```ts
>interface AdminDB extends Connection {
>  models: {
>    adminUser: IAdminUserModel
>    credential: IAdminCredentialModel
>    project: IProjectModel
>    expiredToken: IExpiredTokenModel
>    ...
>  }
>}
>```

>#### Project DB
>```ts
>interface ProjectDB extends Connection {
>  models: {
>    user: IUserModel
>    expiredToken: IExpiredTokenModel
>    ...
>  }
>}
>```

#### Work with models

The middleware `scope` search a project db by the header scope (The middleware are in [`@ggtech/ecms-utils`](https://git.ggtech.global/ggtech/esports-cms/utils)).
The middleware set the database in `Request` param of `express`, in the function of request you have access to the connection.

example:
```ts
route.get('/', (req: Request, res: Response) => {
  try {
    const db = req.isAdmin ? req.adb : req.db

    // access to model
    db.models.<name>.<method>...
  } catch (err) {
    ...
  }
})
```