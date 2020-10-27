
[TOCM]

# AWS Workbench Properties Editor

Understanding properties editor is critical to use this tool effectively. 

The properties editor define the characteristics of the AWS Service. For example following code would be generated 

```java
java.util.Map<java.lang.String, java.lang.String> mainStack_tags = new java.util.HashMap<java.lang.String, java.lang.String>();

		mainStack_tags.put("name", "demoStack");
		mainStack_tags.put("env", "primary");
		mainStack_tags.put("region", "us-east-1");

		mainStack = software.amazon.awscdk.core.Stack.Builder.create(app1, "MAINSTACK")
				.description("A Demo stack for ArchOps Demo").env(mainStackEnv).stackName("mainStack")
				.tags(mainStack_tags).terminationProtection(true).build();

```

using the following properties described in Workbench UI 

![Properties view](../images/getting-started-images/propertiesView.png)

