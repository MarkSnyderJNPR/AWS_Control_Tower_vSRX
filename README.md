# AWS Control Tower with vSRX as Central Security VPC

##### Summary
> The AWS Control Tower with vSRX as Central Security VPC sollution, provides an automated account, organization, security and governance workflow with a centralized juniper firewall. This solluion provides administration team the ease of adding accounts in a AWS multiple account envorment, while automataticaly creating orgazational security policies within AWS's infrstructure.

  >The Next-Generation Firewall (NGFW) has evolved to become the ideal solution for visibility, control, and prevention at the network edge. Threat protection begins with complete visibility into who and what is traversing the network. Combined with behavior and the ability to detect threats in real time, Juniper delivers the most effective NGFW in the industry, reliably safeguarding users, applications, and devices, delivered on-premises, in the cloud, or as a service.

>  AWS Control Tower provides an easy way to set up and govern a secure, multi-account AWS environment with Control Tower's landing zones. It provides AWS Organizations, ongoing account management and governance as well as implementation of AWS's best practices. Architects, system builders and adminstrators can provision new AWS accounts with a few steps while conforming to your company policies.

>  Combining Juniper's SRX firewalls with AWS Control Tower, extends security and visibility accross multiple AWS accounts by creating a cetralyzed security infrasturture VPC. All traffic from newly created AWS accounts are now atuomatacaly routed through Juniper's security infrastruture VPC. In addition to foreceble passing organizational traffic through the central Juniper security VPC, all default VPC networks, IP newtorks, internet gatways, security groups are removed. This combined sollution of Juniper's SRX firewall, AWS Control Tower Organiozational service control policies and Guardrails, organizational security polices are inforced reducing overal organization risk.

>  By combining Juniper Networks Security Director management solution, organization can extend their security policy mangement from AWS cloud, to a multicloud, hypbrid cloud, private cloud, colocation or even traditional infracturuture. With Security Director, organizations can now have a single centralized security mangement source accross their entire organization.

![](./img/Arch.png)

##### Expected archetectual design principles
> ###### Security Infrastructure VPC
  >>To provide a security VPC infrastrucure were all other AWS account's Organizational units traverse through a specified security VPC. THe security infrasturture VPC acts as a headend egress and ingrees point for all traffic comming into AWS.  Traffic is forced through the SRX firewall.  All tradioanl firewall features can be ussed to inspect, secure and inforce trafic.  This includes typical, traffic filtering rules, IPS, URLfiltering, traffic inspection, in addtion to advance features such as Juniper's encrypted traffic anylsis, threat intelengance and protection services.
  >>> ##### Security Infrasturture VPC - Network segments
  >>>> Four Network Segments are created. Public Internet Segmnet is used for all Public egress and ingress traffic. Management for Juniper SRX management, amd two private segments, east and west.  The public subnet is directly publicly facing and should be considered an unsecure segmnet. THe default configuration, is to allow all established session out. This may need to be addjusted on orgaizational desings. The management subnets, AWS security groups default is to allow any ssh public access. Which can be edited at time of initial deployment.  East and west subnets are for Transit Gateway connectivity, and for future other expansion considerations. Expansions can include, zone segmentations (levels of security) or for third party and appliance integrations
  >>> ##### Scaling, and High Avaviliblity
  >>>> AutoScalling
  >>>>> The SRX can be scaled horizontialy for larger workload and traffic flows. Scalling is acheived by utilizing AWS AutoScalling groups. For integration of Autoscalling, this requires manual setup and configuration outside of the deployment steps here.

  >>>> High Avialibity
  >>>>> High Avavilibity can be achieved by ussing AWS application load balancers. For integration of Autoscalling, this requires manual setup and configuration outside of the deployment steps here.

  > ##### Organization units
  >> ###### Organizational structure - Parent and Child
  >>  All Organizational units that are to be included as part off the security VPC, are to be created as a child organization under the parent (security infrastrucure VPC). Metatags are in place that will allow future, multi security infrastrute to allow for sepatations of security domains and traffic. Addtionaly to allow, deployment checks of what security domain that child OU should follow

>> ##### Account creations
>>>  Durring new account creation, all accounts will have default VPC networking, Internet gateways, subnets and security groups deleted and removed, replaced with new architected elements helping to enusre traffic is sent through the security infrastructure VPC.

> ##### Traffic flow - routing
>>All child organizational unit's routing and traffic flows are handled through Transit Gateway assiocations and subnetting and subnet assiocations at the time of automated creation. The Parent Security VPC is included with the exption of default gateway segment that is exprilatly created.

> ##### Network Addressing - IP CIDR
>> Security Infrasture VPC
>>> All traffic is passed through this Parent VPC. Since traffic only traverses, with this desgin all addresses for the parent VPC are static addresses and are assinged durring deployment. Future revisons may allow customization.
>> Child Organizational units
>>> All child organizational units have a VPC CDIR block created and assigned at time of account creation. The account is assigned a major CIDR range with a 16 bit mask. The first 8 bit mask is static, with the rmaining bits incremented for each account creation. For example account A 10.a.x.x and account B 10.b.xx. Each VPC is further segmented into four 24 bit equaliy dived masks subnets. For example, account B would have 10.b.a'.x/24, 10.b.b'.x/24, 10.b.c'.x/24 and 10.a.d'.x/24.     Four subnets are created

>>  IPv4
>>> IPv4 only

##### Methodology and workflow
>The workflow intent is for the initial configuration for the Parent Security Infrasture VPC to deployed once, and the child Organizational units several times where the child organizational units proccess are all automated.

Deployment, Setup and Installation

Related and Suporting links

Current and known issues
