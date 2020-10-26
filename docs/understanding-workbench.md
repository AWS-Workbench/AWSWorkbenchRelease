


# Understanding AWS Workbench 

AWS Workbench is based on [AWS CDK](https://aws.amazon.com/cdk/) and [Eclipse Sirius](https://www.eclipse.org/sirius/). The workbench offers an Eclipse based [IDE](https://en.wikipedia.org/wiki/Integrated_development_environment) for designing and deploying AWS Services. 


Users of this workbench are typically AWS Solution Architects or Engineers. The workbench makes it easy for the users to define the AWS infrastructure using simply dropping the required AWS services on a canvas. Each service can be configured using property editors. 

## The AWS Workbench workflow

![Workflow](../images/getting-started-images/workflow.png)

```java 

package in.rkvsraman.archopsdemo;

import java.util.*;
import software.amazon.awscdk.core.*;
import software.amazon.awscdk.services.apigateway.*;
import software.amazon.awscdk.services.autoscaling.*;
import software.amazon.awscdk.services.ec2.*;
import software.amazon.awscdk.services.elasticloadbalancingv2.*;
import software.amazon.awscdk.services.rds.*;
import software.amazon.awsconstructs.services.cloudfrontapigateway.*;

public class ArchOpsDemo {
	public static software.amazon.awscdk.core.App app1;
	public static software.amazon.awscdk.core.Environment mainStackEnv;
	public static software.amazon.awscdk.core.Stack mainStack;
	public static software.amazon.awscdk.services.ec2.Vpc default_vpc;
	public static software.amazon.awscdk.services.ec2.Subnet subnetUS1A;
	public static software.amazon.awscdk.services.autoscaling.AutoScalingGroup autoScalingGroup16;
	public static software.amazon.awscdk.services.ec2.Subnet subnetUS1B;
	public static software.amazon.awscdk.services.apigateway.RestApi sampleRestAPI;
	public static software.amazon.awscdk.services.elasticloadbalancingv2.ApplicationLoadBalancer applicationLoadBalancer11;
	public static software.amazon.awsconstructs.services.cloudfrontapigateway.CloudFrontToApiGateway cloudfront_api_gateway;
	public static software.amazon.awscdk.services.apigateway.Method genericMethod;
	public static software.amazon.awscdk.services.rds.DatabaseInstance rdsBackend;

	public static void main(String args[]) {
		app1 = software.amazon.awscdk.core.App.Builder.create().autoSynth(true).runtimeInfo(true).stackTraces(true)
				.treeMetadata(true).build();

		mainStackEnv = (new software.amazon.awscdk.core.Environment.Builder()).region("us-east-1").build();

		mainStack = software.amazon.awscdk.core.Stack.Builder.create(app1, "MAINSTACK").env(mainStackEnv)
				.stackName("mainStack").terminationProtection(true).build();

		default_vpc = software.amazon.awscdk.services.ec2.Vpc.Builder.create(mainStack, "DEFAULT_VPC")
				.cidr("10.0. 0.0/16").enableDnsHostnames(true).enableDnsSupport(true).maxAzs(2).natGateways(1)
				.vpnGateway(true).build();

		subnetUS1A = software.amazon.awscdk.services.ec2.Subnet.Builder.create(mainStack, "SUBNETUS1A")
				.availabilityZone("us-east-1a").cidrBlock("10.0. 0.0/16").vpcId(default_vpc.getVpcId())
				.mapPublicIpOnLaunch(true).build();

		autoScalingGroup16 = software.amazon.awscdk.services.autoscaling.AutoScalingGroup.Builder
				.create(mainStack, "AUTOSCALINGGROUP16").allowAllOutbound(true).maxCapacity(2)
				.replacingUpdateMinSuccessfulInstancesPercent(20).resourceSignalCount(4)
				.instanceType(InstanceType.of(InstanceClass.BURSTABLE2, InstanceSize.MEDIUM))
				.machineImage(new AmazonLinuxImage()).vpc(default_vpc).build();

		subnetUS1B = software.amazon.awscdk.services.ec2.Subnet.Builder.create(mainStack, "SUBNETUS1B")
				.availabilityZone("us-east-1b").cidrBlock("10.0. 0.0/16").vpcId(default_vpc.getVpcId()).build();

		sampleRestAPI = software.amazon.awscdk.services.apigateway.RestApi.Builder.create(mainStack, "RESTAPI")
				.restApiName("sampleAPI").build();

		applicationLoadBalancer11 = software.amazon.awscdk.services.elasticloadbalancingv2.ApplicationLoadBalancer.Builder
				.create(mainStack, "APPLICATIONLOADBALANCER11").vpc(default_vpc).deletionProtection(true)
				.internetFacing(true).http2Enabled(true).build();

		cloudfront_api_gateway = software.amazon.awsconstructs.services.cloudfrontapigateway.CloudFrontToApiGateway.Builder
				.create(mainStack, "CLOUDFRONT_API_GATEWAY").existingApiGatewayObj(sampleRestAPI).build();

		genericMethod = software.amazon.awscdk.services.apigateway.Method.Builder.create(mainStack, "GENERICMETHOD")
				.httpMethod("ANY").resource(sampleRestAPI.getRoot()).build();

		rdsBackend = software.amazon.awscdk.services.rds.DatabaseInstance.Builder.create(mainStack, "RDSBACKEND")
				.vpc(default_vpc).deleteAutomatedBackups(true).deletionProtection(true).iops(1200).multiAz(true)
				.port(3306).engine(DatabaseInstanceEngine.MYSQL).databaseName("mysqlMainDB").masterUsername("root")
				.build();

		ArchOpsDemoHelper.setup();
		app1.synth();
	}
}
```

## The User Interface

Following screenshot shows the main components of the workbench
![AWS Workbench screenshot](../images/Screenshot.png)
The main components of workbench are:- 
1. The project explorer for a heirarchical view of defined services
2. Draw canvas for architectural view of the AWS services 
3. Property editor used to configure each service. 
4. Tools Palette listing all the services and aws components available in the workbench. Users can drag any component and drop it into a ```Block``` or ```Sub Block``` area.  

## The user workflow 

1. The user (an AWS architect) creates a workbench project and define


## The Diagram components

![Diagram components](../images/getting-started-images/ComponentHeirarchy.png)

- ```App``` is the topmost component in any project. An App contains one or more ```Stack``` , ```Enviroment``` and ```DefaultStackSynthesizer``` structures. The *Environment* and *DefaultStackSynthesizer* are used to configure the *Stack* 
- Each Stack consists of one or more ```Block``` structures. Each *Block* may contain one or more ```Sub Block``` structures. 
- Each *Block* and *Sub Block* contain AWS Service components.   
    