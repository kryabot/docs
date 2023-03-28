# General architecture
https://lucid.app/documents/view/2decc665-ba92-42af-9cc3-7b24bac174c8

# CICD
As I was the only developer and project was closed, no pull-requests were used.
There are no tests, as most of the logic depends on external services (twitch/telegram). It is not a reason for not writing tests, but due to laziness it was skipped.

When building in DockerHub was free, docker hub was used for building core docker image.
Then it was migrated to build on Bitbucket-pipelines. 

However, during more active months, I usually run out of free 50 mins in Bitbucket pipelines.
Ended up building on local pc via script `build.bat`

Also started to investigation on CircleCI to automate build process, but it was not finished.

# Build of kryabot-core
Based on docker file single docker image is pushed to dockerhub.io
On server, pulled docker image is used to create 4 container with different entry points which results in 4 containers:

- kryabot-telegram
  - Telegram bot @TwitchKryaBot via user account
  - Telegram bot @KryaAuthBot via bot account
- irc-adapter
  - Communication with Twitch chat websockets
    - Send parsed and filtered data to twitch-handler via redis queues
    - Send response to websocket from redis queue
- twitch-handler
  - Handle received data from irc-dataper (process Twitc chat command, notice etc)
- infomanager
  - Telegram bot @KryaInfoBot via bot account
  
# Secrets
Secrets from host server are mounted as file volumes to containers.
On host server, secrets are managed manually.
One of secret file is telegram session - container need to have write access and need to make sure that sesson is used only once (dont start multiple containers with same sessions)

# Build of frontend apps
All frontend apps where built on local PC and moved to managed hosting.
