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
