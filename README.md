[//]: # (SPDX-License-Identifier: CC-BY-4.0)

# Using Self-Encryption to safeguard data security in Fabric's smart contract

## Overview
The prototype of a Hyperledger Fabric's smart contract merged with self-encryption for the Delft University of Technology Bachelor's
Research project.

This source code is based on Hyperledger's Fabric Samples, https://github.com/hyperledger/fabric-samples. \
Self-Encryption library can be found here, https://github.com/maidsafe/self_encryption.

## Installation
* Docker v19.03.8
* Nodejs v14.18.2

## Run

#### First navigate the current directory to *test-network* folder

#### Start up the test network and create channel
```shell
./network.sh up createChannel
```
#### Deploy the smart contract
```shell
./network.sh deployCC -ccn basic -ccp ../self-encryption-chaincode -ccl javascript
```
#### Set environmental variables
```shell
export PATH=${PWD}/../bin:$PATH
export FABRIC_CFG_PATH=$PWD/../config/
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```
#### Users upload encrypted file chunks and data map retrieved
```shell
docker cp {path/to/file_chunks_and_data_map} {peer_1_container_id}:/etc/hyperledger/fabric/chunk_store_test
docker cp {path/to/file_chunks_and_data_map} {peer_2_container_id}:/etc/hyperledger/fabric/chunk_store_test

```
#### Initialise the ledger
```shell
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile 
${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem 
-C mychannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles 
${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses 
localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c 
'{"function":"InitLedger","Args":[]}'
```
#### Retrieve all the data saved in the ledger
```shell
peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}'
```
#### Retrieve the CID of the data map 
```shell
peer chaincode query -C mychannel -n basic -c '{"Args":["PutDataMapToIPFS"]}'
```
#### Shut down the network
```shell
./network.sh down 
```
