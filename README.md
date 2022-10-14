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
>> WIP
##### Related and Suporting links
>> WIP
##### Current and known issues
>> WIP
