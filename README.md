# Connect-A-Thon

Follow these steps and quickly join a Marbles Trading Network.

## Prerequisites

_Note: It is highly recommended to open any hyperlinks in a new browser tab; if not, you will be navigated away from this page_
- [Git client](https://git-scm.com/downloads)
- [Docker v1.12 or higher](https://www.docker.com/products/overview)
- [Docker-Compose v1.8 or higher](https://docs.docker.com/compose/overview/)

Check your Docker and Docker-Compose versions with the following commands:
  ```bash
docker version
```
  ```bash
docker-compose version
```

## Register a user

- Go to the [User Registration Website](http://connectathon-cop.blockchain.ibm.com)
- To circumvent the firewall, use the credentials that you received from __IBM Blockchain__.  _Note: These credentials are solely for accessing the Registration Website. You will NOT reuse them in the forthcoming steps._
- Next, you will be prompted for an `enrollID` and your `email`.
- Please enter a unique `enrollID` and the email address where you want your `enrollSecret` sent.  Your `enrollID` can be alphanumeric, but __CANNOT__ contain special characters.  For example, `IBM123` is acceptable, whereas `IBM456$$` is not.
- These administrative credentials, which are comprised of your `enrollID` and an auto-generated
`enrollSecret`, will be sent by __IBM Blockchain__ to your provided `email`. Save these credentials; you will need the `enrollID` and `enrollSecret` to properly authenticate your marbles application onto the network.  

### What occurred during registration?

Behind the scenes a REST call was issued to the publicly-hosted COP server to register a user with the unique `enrollID` you provided.  This user represents the admin for your organization, and will be used by the
application to authenticate to the chain.


## Clone the repo and navigate to the marbles directory

  ```bash
  git clone https://github.com/IBM-Blockchain/connectathon.git
  ```
  ```bash
  cd ./connectathon/marbles
  ```

## Use the credentials to join the chain

__IMPORTANT: Please read this entire section before executing the marbles shell script__

  First, ensure that you are in the correct working directory:
  ```bash
  ls
  ```
  This should display the `marbles` directory:
  ```bash
  README.md                       marbles.yml
  docker-compose-no-cdb.yml       mycreds.json
  marbles.sh                      peer.yml
  ```
  The syntax for the `marbles.sh` shell script is presented in the code block below. This script will pull
  the dependent fabric images from [Docker Hub](https://hub.docker.com/u/connectathon/) and spin up two containers - one for your endorsing peer, and one for the marbles Node.js application.  This process takes a few minutes, during which you will see the various images being downloaded and extracted onto your local machine.  Depending on the configuration of your machine, you may be prompted several times for your root password. The `enrollID` and `enrollSecret` which you received in your email upon registration are to be used as input parameters in the script below:
  ```bash
  #enrollId is what you used alongside your email when you registered with the COP Server
  #enrollSecret is what was returned to you via email. It is a 12 digit alphanumeric string
  #company is the name of your organization, and is how you will be represented on the chain
  #user1, user2, and user3 are the users registered under your organization

  ./marbles.sh up <enrollID> <enrollSecret> <company> <user1> <user2> <user3>
  ```
  Ensure that you populate all of the required fields and that you identically match the `enrollID` and `enrollSecret`. If for some reason you supply an incorrect `enrollSecret`, you will need to re-register and request fresh credentials.  __These inputs ARE case-sensitive.  Additionally, be sure to only use
  alphanumeric strings for your enrollID, company, and users.__

  Recall that this demo is simulating an organizational admin who authenticates to a blockchain network, and then permissions users to transact on the network via their organization's endorsing peer.  A sample command line prompt might look similar to the following:
  ```bash
  ./marbles.sh up Admin1 xYzAAbbc1234 JPM eric kenny stan
  ```
  To reiterate, you will likely be prompted for your machine's root password at least one time after you execute the marbles script.  Simply supply the root password for your machine and the script will continue to run.  You will see something similar to the following:
  ```bash
  Status: Downloaded newer image for connectathon/fabric-ccenv:latest
Password:
  ```
  Now, run the `marbles.sh` script in accordance with the instructions.  Once the images have finished downloading and extracting, move on to the next section.

## View the Marbles UI

  By executing the shell script you have kicked off the Marbles application.  It is running as a container on your local machine.  To see your currently-running containers:
  ```bash
  docker ps
  ```
  Assuming you have no other processes running, you should see two distinct containers - one for your peer and one for the marbles application.

  Open up a browser and visit `localhost:3000`.  This will take you to an initial administrative screen. Login as the `admin` at the bottom of the page, after which three processes will take place.  

  * You will be logged in as the admin.
  * Your endorsing peer obtains the current state of the ledger from the publicly-hosted Ordering Service.  The ledger already contains the chaincode used by the marbles application.  At this point your machine will spin up a new container for the marbles chaincode.
  * Your users will be registered and authenticated to the chain.

  You should now have three distinct containers - one for your peer, one for the marbles application, and one for the marbles chaincode.  View your containers with a `docker ps` command.

  Upon success of these three processes, you will be able to view the entire marbles trading market. Organizations and users represented within those organizations will appear on the screen.  You will also notice that you have the ability to transfer assets (marbles) for your registered users, but cannot distribute another organization's assets.  __Happy trading!__

  If you want to see the full transaction lifecycle for an invocation, click the __Settings__
  button in the upper left portion of your screen.  Then click the __Enable__ button next to __Story Mode__.
  Now, when you create or trade an asset, you will be able to see the full architectural flow - proposal, endorsement, ordering, and validation/commitment - and an ensuing success/failure for your transaction.

  To see the logs and peer processes for your endorsing peer:
  ```bash
  # This will allow you to see real-time invocations and block commits happening on your network's chain
  docker logs peer
  ```
  To see the logs for your marbles application:
  ```bash
  docker logs mtc-01
  ```

## Troubleshooting

  If you see an error similar to:
  ```
  ERROR: In file './docker-compose-no-cdb.yml' service 'version' doesn't have
  any configuration options. All top level keys in your docker-compose.yml must
  map to a dictionary of configuration options.
  ```
  You will need to upgrade your Docker-Compose version in order to achieve compatibility with the docker-compose.yaml scripts. Upgrade your Docker-Compose version in one of the following ways:
  ```bash
  apt install docker.io
  ```
  OR
  ```bash
  sudo -i
  curl -L "https://github.com/docker/compose/releases/download/1.9.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  chmod +x /usr/local/bin/docker-compose
  docker-compose --version
  exit
  ```

## Helpful Docker Commands

1. View running containers:

  ```
docker ps
```
2. View all containers (active and non-active):

  ```
docker ps -a
```
3. Stop all Docker containers:

  ```
docker stop $(docker ps -a -q)
```
4. Remove all containers.  Adding the `-f` will issue a "force" removal:

  ```
docker rm -f $(docker ps -aq)
```
5. Remove all images:

  ```
docker rmi -f $(docker images -q)
```
6. Remove all images except for hyperledger/fabric-baseimage:

  ```
docker rmi $(docker images | grep -v 'hyperledger/fabric-baseimage:latest' | awk {'print $3'})
```
7. Start a container:

  ```
docker start <containerID>
```
8. Stop a containerID:

  ```
docker stop <containerID>
```
9. View network settings for a specific container:

   ```
docker inspect <containerID>
```
10. View logs for a specific containerID:

  ```
docker logs -f <containerID>
```


## Stop Peer and Application

  ```bash
  ./marbles.sh down
  ```
