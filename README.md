# Plugin Generic Webhook Trigger
Variable: text
Expression: $.text
JSONPath


# Script
```
BRANCH=${text:3:1000}
SRC="/home/miichi/projects/SPT_Biz06_Backend"
CORE_SRC="/home/miichi/projects/SPT_Debug/compose/core"

cd $SRC
git fetch --all

export CODEARTIFACT_AUTH_TOKEN=$CODEARTIFACT_AUTH_TOKEN

git ls-remote --exit-code --heads origin $BRANCH >/dev/null 2>&1
EXIT_CODE=$?
if [[ $EXIT_CODE == '0' ]]; then
  echo "Git branch '$BRANCH' exists in the remote repository"
  
  git checkout $BRANCH
  git reset --hard origin/$BRANCH
  git pull

  cd $CORE_SRC
  docker-compose --profile biz06 up -d --force-recreate --no-deps --build  biz06-backend-app
elif [[ $EXIT_CODE == '2' ]]; then
  echo "Git branch '$BRANCH' does not exist in the remote repository"
fi
```
