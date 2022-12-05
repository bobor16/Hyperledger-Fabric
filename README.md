## Assignment 3. Hyperledger Fabric

The files in this repository are changes that have been made to the existing reposiory of https://github.com/hyperledger/fabric-samples

It is expected that the user has installed and followed the Prerequisites, which can be found here https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html
 
### Configuring the project
To get expected results from these changes, start the test network and create two channels:

```
./network.sh up createChannel -c koebenhavn -ca
./network.sh createChannel -c fyn
```

next, the chaincode is deployed for koebenhavn and fyn, by first running:

```
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-typescript -ccl typescript -c koebenhavn
```
followed by
```
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-typescript -ccl typescript -c fyn
```

we need to check if the path variables have been sat, which can be done by:
```
peer -v
```
if the command is not recognized, run

```
export PATH=${PWD}/../bin:$PATH
```
followed by

```
export FABRIC_CFG_PATH=$PWD/../config/
```

Now, the user can specify which organisation to use

```
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```
Lastly, we initiate the ledger for fyn and for koebenhavn

```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C fyn -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"InitLedger","Args":[]}'
```

```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C koebenhavn -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"InitLedger","Args":[]}'
```
### Voting
When the configuration above has been done, the user can place his/her vote, by changing the `LOCATION`, `CANDIDATE`, `USER` arguments at the end of the following command.

```
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C LOCATION -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"Vote","Args":["CANDIDATE", "USER"]}'
```
## Getting the results
The voting result can be seen by querying the `GetAllAssets` and changing the `LOCATION` argument to the desired region

```
peer chaincode query -C LOCATION -n basic -c '{"Args":["GetAllAssets"]}'
```
