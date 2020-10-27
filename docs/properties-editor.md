

# AWS Workbench Properties Editor

The properties editor define the characteristics of the AWS Service. 

For example a stack defined by properties as below 
![Properties view](../images/getting-started-images/propertiesView.png)

would generate the following code. 

```java
java.util.Map<java.lang.String, java.lang.String> mainStack_tags = new java.util.HashMap<java.lang.String, java.lang.String>();

		mainStack_tags.put("name", "demoStack");
		mainStack_tags.put("env", "primary");
		mainStack_tags.put("region", "us-east-1");

		mainStack = software.amazon.awscdk.core.Stack.Builder.create(app1, "MAINSTACK")
				.description("A Demo stack for ArchOps Demo").env(mainStackEnv).stackName("mainStack")
				.tags(mainStack_tags).terminationProtection(true).build();

```

***Understanding properties editor is critical to use this tool effectively.***


Properties of AWS Services are modelled based on [Builder pattern](https://en.wikipedia.org/wiki/Builder_pattern) . 

For example this is the [Builder](https://docs.aws.amazon.com/cdk/api/latest/java/software/amazon/awscdk/core/Stack.Builder.html) for ```Stack```. The names of the methods in the Builder become the property names in the Property editor. 


## Mandatory properties 

- For ***App*** object, ```projectName```, ```packageName```, ```mainClassName```, ```varName``` and ```identifier``` are mandatory properties.
- For ***Block*** and ***Sub Block***, ```label``` is mandatory.
- For ***Stack*** and all other ***AWS Service Components***, ```varName``` and ```identifier``` are mandatory properties.

## Property types
- Properties can be of type ```String```, ```Number```,```Boolean```,```Enum```,```Reference```,```List``` and ```Map``` 
- To know the expected value for the property, hover on the ```?``` next to the property name. The information is available as a tooltip on the ```?``` symbol. 

    ![tooltip](../images/getting-started-images/tooltip.png) 

- For ```String```, ```Number``` and ```Reference```, a **Textbox** is provided to enter the values

    ![textbox](../images/getting-started-images/textbox.png)

- A ```Boolean``` is represented by **Checkbox**  . 

    ![checkbox](../images/getting-started-images/checkbox.png)

- ```Enum``` are represented by **Radio Buttons**

    ![radio button](../images/getting-started-images/radiobutton.png)

- For ```List``` and ```Map``` , a **List Box** is provided.

    ![list](../images/getting-started-images/map.png)

Clicking ```Add value``` button pops up a dialog box prompting to enter the list or map value 

List Item Dialog:
    ![list item](../images/getting-started-images/listDialog.png)

Map Item Dialog
    ![map item](../images/getting-started-images/mapDialog.png)

# Using References

There are 3 ways that you can access other objects and classes in **Direct variable access** , **Access a method of a variable** and **Invoke a static method on ca class**

## Direct variable access

Every AWS Service instance has a ```varName``` property. This is the reference to the service which can be used in other AWS Services where the service instance needs to be referenced. For example; if there is a instance of *Environment* object **mainStackEnv** is being reference by *Stack* instance **mainStack** 

Environment Properties:
    ![env props](../images/getting-started-images/envProps.png)

Stack Properties: 
    ![stack props](../images/getting-started-images/stackProps.png)

The generated code would be 

```java

        mainStackEnv = (new software.amazon.awscdk.core.Environment.Builder()).region("us-east-1").build();

		java.util.Map<java.lang.String, java.lang.String> mainStack_tags = new java.util.HashMap<java.lang.String, java.lang.String>();

		mainStack_tags.put("name", "demoStack");
		mainStack_tags.put("env", "primary");
		mainStack_tags.put("region", "us-east-1");

		mainStack = software.amazon.awscdk.core.Stack.Builder.create(app1, "MAINSTACK")
				.description("A Demo stack for ArchOps Demo").env(mainStackEnv).stackName("mainStack")
				.tags(mainStack_tags).terminationProtection(true).build();
```
## Access a method of a variable

At times you would also need to access amethod of a declared service. For example [Subnet Builder](https://docs.aws.amazon.com/cdk/api/latest/java/software/amazon/awscdk/services/ec2/Subnet.Builder.html) needs access to ```vpcId``` which can be accessed thru [getVpcId()](https://docs.aws.amazon.com/cdk/api/latest/java/software/amazon/awscdk/services/ec2/Vpc.html#getVpcId--) method of [Vpc](https://docs.aws.amazon.com/cdk/api/latest/java/software/amazon/awscdk/services/ec2/Vpc.html) service. 

To reference this method of a variable in a property, we can surround it by ```~``` . See example below. 

**VPC Properties**

![stack props](../images/getting-started-images/vpcProps.png)

**Subnet Properties**

![stack props](../images/getting-started-images/subnetProps.png)

The generated code would be 

```java

default_vpc = software.amazon.awscdk.services.ec2.Vpc.Builder.create(mainStack, "DEFAULT_VPC")
				.cidr("10.0. 0.0/16").enableDnsHostnames(true).enableDnsSupport(true).maxAzs(2).natGateways(1)
				.vpnGateway(true).build();

subnetUS1A = software.amazon.awscdk.services.ec2.Subnet.Builder.create(mainStack, "SUBNETUS1A")
				.availabilityZone("us-east-1a").cidrBlock("10.0. 0.0/16").vpcId(default_vpc.getVpcId())
				.mapPublicIpOnLaunch(true).build();

```







