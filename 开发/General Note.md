# Admin-API 

## run AdminDashBoard - 3 projs & 2 dockers

```shell
yarn install
yarn build

# Start Datastore Emulator
docker run -p 8081:8081 google/cloud-sdk:latest /bin/bash gcloud beta emulators datastore start --no-store-on-disk --project=datastore-testing --host-port=0.0.0.0:8081
# Start postgres
docker run -e POSTGRES_PASSWORD=test -e POSTGRES_USER=master -e POSTGRES_DB=app -p 54320:5432 postgres:12.6

cd apps/backend
yarn db:migrate
yarn start:dev:local

cd apps/backend-legacy
yarn start:dev:local

cd apps/dashboard
yarn start:dev:local




```

# Good way to rebase

```bash
git checkout main
git pull
git checkout <branchName>
git rebase main
git push --force-with-lease
```

