# Known issues and limitations

Summary: This is a list of known issues and limiations with the integrations here.  This list is not a complete nor exhusting list but issues that are known at the time of writing
## Setup
For HSST1 you will want to change your cidr for access. It curently allows any address ssh
You will want to create your preshared keys and advance and  updaet the key name.


## 1. Security Groups, Route tables, Transit Gateways
Not all security groups are mapped to Network segments correctly. At the moment, only the last security group (SECG2) is configured. SECG0, SEC1 and SECG2 ... Same for Route Tables.  The reason for this behavor, the core of the project was an import (copy paste & make work and continue) from another project that I was working on.  the Transit gateway is only conected to the last network segment for each Org VPC 10.vpc.192.0/24.

## 1. One Central Security VPC
There is only one central security VPC for the momont. Future plan may include TAGs that can be reviewed to allow more than one Central VPC.

## 2. Moving of accounts
For the moment any account move with trigger Envent Bridge and WILL delete the default VPC for that account!!!!!

## 3. ReRegister
For the moment the reregistration for any OU will triger the event bridage and will create a stake set and address group.  Testing need to happen to determine what happens to an exist OU, if reregisterd.  A check tag if exist needs to be written.

## 4. Updating Landing Zones
Updating lanading zone maty trigger Event bridge
