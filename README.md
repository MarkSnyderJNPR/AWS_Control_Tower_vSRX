# AWS Control Tower with vSRX as Central Security VPC

##### Summary
> The AWS Control Tower with vSRX as Central Security VPC solution, provides an automated account, organization, security and governance workflow with a centralized juniper firewall. This solution provides administration team the ease of adding accounts in a AWS multiple account environment, while automatically creating organizational security policies within AWS's infrastructure.

  >The Next-Generation Firewall (NGFW) has evolved to become the ideal solution for visibility, control, and prevention at the network edge. Threat protection begins with complete visibility into who and what is traversing the network. Combined with behavior and the ability to detect threats in real time, Juniper delivers the most effective NGFW in the industry, reliably safeguarding users, applications, and devices, delivered on-premises, in the cloud, or as a service.

>  AWS Control Tower provides an easy way to set up and govern a secure, multi-account AWS environment with Control Tower's landing zones. It provides AWS Organizations, ongoing account management and governance as well as implementation of AWS's best practices. Architects, system builders and administrators can provision new AWS accounts with a few steps while conforming to your company policies.

>  Combining Juniper's SRX firewalls with AWS Control Tower, extends security and visibility across multiple AWS accounts by creating a centralized security infrastructure VPC. All traffic from newly created AWS accounts are now automatically routed through Juniper's security infrastructure VPC. In addition to foreseeable passing organizational traffic through the central Juniper security VPC, all default VPC networks, IP networks, internet gateways, security groups are removed. This combined solution of Juniper's SRX firewall, AWS Control Tower Organizational service control policies and Guardrails, organizational security polices are enforced reducing overall organization risk.

>  By combining Juniper Networks Security Director management solution, organization can extend their security policy management from AWS cloud, to a multi-cloud, hybrid cloud, private cloud, collocation or even traditional infrastructure. With Security Director, organizations can now have a single centralized security management source across their entire organization.

![](./img/Arch.png)

##### Expected architectural design principles
> ###### Security Infrastructure VPC
  >>To provide a security VPC infrastructure were all other AWS account's Organizational units traverse through a specified security VPC. THe security infrastructure VPC acts as a head-end egress and ingress point for all traffic coming into AWS.  Traffic is forced through the SRX firewall.  All traditional firewall features can be used to inspect, secure and enforce traffic.  This includes typical, traffic filtering rules, IPS, URL-filtering, traffic inspection, in addition to advance features such as Juniper's encrypted traffic analysis, threat intelligence and protection services.
  >>> ##### Security Infrastructure VPC - Network segments
  >>>> Four Network Segments are created. Public Internet Segment is used for all Public egress and ingress traffic. Management for Juniper SRX management, and two private segments, east and west.  The public subnet is directly publicly facing and should be considered an insecure segment. The default configuration, is to allow all established session out. This may need to be adjusted on organizational designs. The management subnets, AWS security groups default is to allow any ssh public access. Which can be edited at time of initial deployment.  East and west subnets are for Transit Gateway connectivity, and for future other expansion considerations. Expansions can include, zone segmentation (levels of security) or for third party and appliance integrations
  >>> ##### Scaling, and High Availability
  >>>> Auto-Scaling
  >>>>> The SRX can be scaled horizontally for larger workload and traffic flows. Scaling is achieved by utilizing AWS Auto-Scaling groups. For integration of Auto-scaling, this requires manual setup and configuration outside of the deployment steps here.

  >>>> High Availability
  >>>>> High Availability can be achieved by using AWS application load balancers. For integration of Auto-scaling, this requires manual setup and configuration outside of the deployment steps here.

  > ##### Organization units
  >> ###### Organizational structure - Parent and Child
  >>  All Organizational units that are to be included as part off the security VPC, are to be created as a child organization under the parent (security infrastructure VPC). Metatags are in place that will allow future, multi security infrastructure to allow for separations of security domains and traffic. Additionally to allow, deployment checks of what security domain that child OU should follow

>> ##### Account creations
>>>  During new account creation, all accounts will have default VPC networking, Internet gateways, subnets and security groups deleted and removed, replaced with new architect  elements helping to ensure traffic is sent through the security infrastructure VPC.

> ##### Traffic flow - routing
>>All child organizational unit's routing and traffic flows are handled through Transit Gateway associations and subneting and subnet associations at the time of automated creation. The Parent Security VPC is included with the exception of default gateway segment that is explicitly created.

> ##### Network Addressing - IP CIDR
>> Security Infrastructure VPC
>>> All traffic is passed through this Parent VPC. Since traffic only traverses, with this design all addresses for the parent VPC are static addresses and are assigned during deployment. Future revisions may allow customization.
>> Child Organizational units
>>> All child organizational units have a VPC CIDR block created and assigned at time of account creation. The account is assigned a major CIDR range with a 16 bit mask. The first 8 bit mask is static, with the remaining bits incremented for each account creation. For example account A 10.a.x.x and account B 10.b.xx. Each VPC is further segmented into four 24 bit equally dived masks subnets. For example, account B would have 10.b.a'.x/24, 10.b.b'.x/24, 10.b.c'.x/24 and 10.a.d'.x/24.     Four subnets are created

>>  IPv4
>>> IPv4 only

##### Methodology and workflow
>The workflow intent is for the initial configuration for the Parent Security Infrastructure VPC to deployed once, and the child Organizational units several times where the child organizational units process are all automated.

![](./img/WorkFlow.png)

##### Deployment, Setup and Installation
>> ##### Compononets Used
>> Juniper Networks vSRX from the AWS Market Place, AWS Control Tower, Organizations. Clouformation, Lambda, EventBridge, Systems Prarmeter Store, S3 and Resource Access Maanger are used for this solution.  AWS Control Tower AccountFactory or Organization can be used for creaating Accounts and for Organizations.

>> ##### Market Place Subscriptions
>> An AWS Market Place subscription for the Juniper vSRX is required for this sollution. Both BYOL, or PAYG will be  needed. It is recomended to use the latest vSRX version.

>> ##### Prerequisit setup and settings
>> AWS Control Tower must have been previously deployed with the AWS IAM roles, Control Tower and CloudFormation, Excucution and adminstration must be deployed for all Organizations manged by Control Tower.  [https://docs.aws.amazon.com/controltower/latest/userguide/deployment.html]()

>> S3 - Simple Storage Service
>>> An S3 bucket must be created for the storing of Cloud Formation Templates. AWS Lambda will refer to these scripts for deployment.

>> IAM - Identity and Access Management
>>> An IAM role for lambda will need to be created. The lambda role will need to have access to the S3 bucket were CloudFormation templates are stored and the ability to deploy cloudformation templates.
>>> This solution, addtionaly creates an IAM role in each child Organization account. This role is created for the deletetion of the default VPC and Internet Gateway and all the dependancies. This role can be deleted after the child OU is creaated. However, can be used for later adminstration uses and fine tunning.

>> Lambda - functions
>>> Two lambda functions need to be created. One for Organization registrataions, and another for account move and regitrastion.
>>> The Organization Lambda function, lambda_main.py, is responsible for the core infrastucture and all address management for child organizational units. This lambda function must be called by the Organization registration EventBridge. It should be noted that Organization unit can either be created with Control Tower or within Organizations and then registered in Control Tower.
>>> The second Lambda script, ddvpc_function.py, is responsible for the deletion on the default VPC and Internet Gateway. This function is called from one of two EventBridge ruels. Either when a new account is created in Account Factory, or if the accountis created in organizations and the account is moved.
>>>> #### Extreme caution
>>>> Extreme caution needs to be practiced for ddvpc_function.py and when it is called. Currenty, there are NO checks for "if" the account being moved, is in an account that is within a child organizational unit under the Security Infrastuture VPC.  This check is planed for future releases. There are currently no tags in organizans to check and needs coding to determine, parent Organization, child Organizational Unit, and "managed" account relationships...

>>>Lambda functions must ensure all timeouts are at least 10 minutes or scipts "may" fail to complete.

>> EventBridge
>>> Three EventBridge rules need to be created. Each rule trigers lambda functions specific to organizational unit registration in Control Tower, AWS Account creation within Account Factory, or an accoun that was created within Organizations, and moved into a child Organizational Unit.  It should be noted, that technically this account is not "really" managed by Control Tower.

>>>  EventBridge Organizational Unit Registration triger
```
{
  "source": ["aws.controltower"],
  "detail-type": ["AWS Service Event via CloudTrail"],
  "detail": {
    "eventName": ["RegisterOrganizationalUnit"]
  }
}
```
>>> EventBridge Organization account created and moved triger
```
{
  "source": ["aws.organizations"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["organizations.amazonaws.com"],
    "eventName": ["MoveAccount"]
  }
}
```
>>> EventBridge Account Factory / Control Tower account creaton
```
{
  "source": ["aws.controltower"],
  "detail-type": ["AWS Service Event via CloudTrail"],
  "detail": {
    "eventName": ["CreateManagedAccount"]
  }
}
```

>>>Currently, it is recomended that the EventBridge rule instance setting allow for only one instance can be run at one time.

>> There are two major portions for the setup and deployment of the Control Tower with vSRX sollution. First section is the head-end or the Security Infrastructure VPC. The Second portion is the remote or the child Organizational Units.  Please revew the workflow here for a visual discription. [./img/WorkFlow.png]().

>> Head-end Security Infrastructure VPC
>> For the head-end portion, the AWS CloudFormation Create Stactset will be used. S

##### Related and Suporting links
>> [https://www.juniper.net/us/en/solutions/next-gen-firewall.html]()
>>[https://www.juniper.net/documentation/us/en/software/vsrx/vsrx-consolidated-deployment-guide/vsrx-aws/topics/concept/security-vsrx-aws-overview.html]()

##### Current and known issues
>> [./KNOWN.md]()
