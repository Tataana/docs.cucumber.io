---
title: Cucumber JVM Configuration
---


# Type Registry Configuration

The type registry is used to configure parameter and data table types. It can be configured by placing an implementation 
of `cucumber.api.TypeRegistryConfigurer` on the glue path.


```java
public class TypeRegistryConfiguration implements TypeRegistryConfigurer {

    @Override
    public Locale locale() {
        return ENGLISH;
    }

    @Override
    public void configureTypeRegistry(TypeRegistry typeRegistry) {
        typeRegistry.defineParameterType(new ParameterType<Integer>(
            "digit",
            "[0-9]",
            Integer.class,
            new Transformer<Integer>() {
                @Override
                public Integer transform(String s) throws Throwable {
                    return Integer.parseInt(s);
                }
            })
        );

        typeRegistry.defineDataTableType(new DataTableType(
            ItemQuantity.class,
            new TableCellTransformer<ItemQuantity>() {
                @Override
                public ItemQuantity transform(String s) {
                    return new ItemQuantity(s);
                }
            })
        );
    }
}
```

# JUnit

To use JUnit to execute cucumber scenarios add the `cucumber-junit` dependency to your pom.

```xml
<dependencies>
  [...]
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-junit</artifactId>
        <version>${cucumber.version}</version>
        <scope>test</scope>
    </dependency>
  [...]
</dependencies>
```

Create an empty class that uses the Cucumber JUnit runner.

```java
package com.example;

import cucumber.api.CucumberOptions;
import cucumber.api.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
public class RunCukesTest {
}
```

This will execute all scenarios in same package as the runner, by default glue code is also assumed to be in the same 
package. 

The `@CucumberOptions` can be used to provide
[additional configuration](https://cucumber.io/docs/reference/jvm#list-configuration-options) to the runner. 


For example if you want to tell Cucumber to use the two formatter plugins `pretty` and `html`, you can specify it like this:

```java
package mypackage;

import cucumber.api.CucumberOptions;
import cucumber.api.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(plugin = {"pretty", "html:target/cucumber"})
public class RunCukesTest {
}
```

Usually, this class will be empty. You can, however, specify several JUnit rules.

{{% note "Supported JUnit annotations"%}}
Cucumber supports JUnits `@ClassRule`, `@BeforeClass` and `@AfterClass` annotations.
These will executed before and after all scenarios. Using these is not recommended, as it limits the portability between different runners;
they may not execute correctly when using the commandline, [IntelliJ IDEA](https://www.jetbrains.com/help/idea/cucumber.html) or
[Cucumber-Eclipse](https://github.com/cucumber/cucumber-eclipse). Instead it is recommended to use Cucumbers `Before`
and `After` [hooks](#hooks).
{{% /note %}}

The Cucumber runner acts like a suite of a JUnit tests. As such other JUnit features such as Categories, Custom JUnit
Listeners and Reporters can all be expected to work.

For more information on JUnit, see the [JUnit web site](http://www.junit.org).

