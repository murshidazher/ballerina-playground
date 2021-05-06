# [ballerina-playground](https://github.com/murshidazher/ballerina-playground)

> Learning ballerina 🍥 🍥

## Setup

```sh
> brew install ballerina
```

### Running

```sh
> ballerina run hello.bal
```

### Building

> :bulb: When running the final programs in a real-world production setup, always build the application first and then run the built executable separately. This makes sure we will not be re-compiling the source code each time we run the program, thus wasting time. In addition; at the time of execution, we reduce the possibility of external tampering with the program’s logic because of the availability of the source code.

To build the project and run. The `.jar` file is of the type Java archive. This represents the binary executable that
will run in the Java Virtual Machine (JVM), which is the target runtime environment used
for running Ballerina programs.

```sh
> ballerina build print_time.bal
> ballerina run print_time.jar
```

## Organizing Code

> TOML - (Tom’s Obvious, Minimal Language)

You can start by splitting high-level functionality into individual modules. A single module will contain independent functionality that can be brought together to build a larger Ballerina project.

```sh
> ballerina new calculator # creates a new project called calculator
```

- This will create the following folder structure, where the `Ballerina.toml` file represents project metadata.

```txt
.
├── calculator
│ ├── Ballerina.toml
│ └── src
```

## Importing modules

```bal
import <organization>/<module_name> [as <identifier>]
```

After a module is imported, you can use its identifier as a means of accessing the
data and functionality exposed by the module to the outside. This is done by using
the syntax `identifier:<symbol>`, where the symbol can be things such as constants,
functions, and objects.

## Java Dependencies

In certain situations, it is required to bundle Java libraries as dependencies, which
are also represented as .jar files. This would be required when we are using the
`ballerinax/java.jdbc` module, which is used for database access from Ballerina. In this
situation, a database driver is required for each separate database server type. This must
be packaged as a dependency in the module that requires the driver.
For example, if you are using a MySQL database, a dependency similar to the
following should be added in the Ballerina.toml file:

```toml
[platform]
target = "java8"
[[platform.libraries]]
path = "/home/laf/mysql-connector-java-8.0.18.jar"
modules = ["module1", "module2"]
```

For example, the following configuration
adds the MySQL and MSSQL JDBC drivers as dependencies to the module1 and module2
modules, respectively.

```toml
[platform]
target = "java8"

[[platform.libraries]]
path = "/home/laf/mysql-connector-java-8.0.18.jar"
modules = ["module1"]

[[platform.libraries]]
path = "/home/laf/mssql-jdbc-7.4.1.jre8.jar"
modules = ["module2"]
```

## Adding Modules to a Project

A new module can be added to a project using the ballerina add <module_name> command.

```sh
$ ballerina add calfunctions
Added new ballerina module at 'src/calfunctions'
$ ballerina add calparser
Added new ballerina module at 'src/calparser'
$ ballerina add calapp
```

The source .bal file inside the modules does not need to have a specific name;
rather, it just needs the extension .bal. Therefore, all the Ballerina source code in this
directory and any subdirectories (excluding `tests`/`resources`) will be considered as part of
the current module.


## Building Modules

- We have now implemented the source code for the modules in the project; now it’s time to build the project.
- The project consists of three modules. We can build the modules one by one, or we can build all the modules at once. A specific module can be built with the following command:

```sh
> ballerina build [-c] <module_name>
```

If the module on its own is not executable, you need to pass the `-c` switch to the compiler to only compile the module code and not create an executable `.jar` file. The compiler will only compile the source code and will create an intermediary file known as a `BALO` (Ballerina Object) file. This file simply contains the compiled result of the given source files, and it has not combined the dependencies to create a final executable.

```sh
> ballerina build -c calparser
> ballerina build -c calfunctions
> ballerina build calapp
```

- The preceding command has built an executable file because of it having a program entry point defined in its code. 

> :bulb: Even simply building `calapp` will build the other modules automatically if they are not already built, since they have been identified as required dependencies with the import statements.

- If we want to build all the modules in a project at once, we can use the following command. It will build all the modules and will also create the final executable if there is a module with a program entry point.

```sh
> ballerina build -a
```

## Running the Application

```sh
> ballerina run target/bin/calapp.jar
Enter calculator request: fact 14
Result: 87178291200
Enter calculator request: sort 54 593 03 95 915 94 125 35
Result: 3 35 54 94 95 125 593 915
```

A project executable can also be run by directly executing the `ballerina run <module_name> `command. This will build the executable and run it at the same time. But this is not the preferred way in a production environment.

```sh
> ballerina run calapp
```

## Documentation

- Ballerina has defined a documentation syntax to be used in describing Ballerina language constructs and also provides the tools to generate the documentation content.
- The syntax to be used is based on Markdown, called `Ballerina Flavored Markdown (BFM)`. With this format, we can provide richer formatting support to our code documentation.
- A source code construct is generally documented using the following syntax:

```txt
# <description>
# ...
# + <param/field> - <description>
#
# ...
# + <param/field> - <description>
# ...
# + return - <description>
```

- The `Module.md` file in each Ballerina module can also be used to give additional documentation content regarding the module. This content will be combined with the Ballerina code documentation to generate the final documentation page content.

```sh
> ballerina doc -a
Generating API Documentation
 target/apidocs
```

## Coding Convention

> For a full reference guide on the Ballerina coding conventions, please refer to [https://ballerina.io/learn/style-guide/](https://ballerina.io/learn/style-guide/).

### Indentation

The indentation length must be `four spaces`, and the source code should not use any tab characters. The maximum number of characters allowed per line is 120 characters.


## Testing 

### Unit testing

- The idea behind unit tests is that if we make sure the **lowest-level functionality is working properly**, and we can verify this, it will be **easier** to implement and **verify the higher-level features** that are **built on top of the unit-level features**.

## Integration Testing

- Integration testing is done to make sure that the lower-level modules work correctly with each other. This **basically tests the module’s interfaces** and whether they are still compatible with each other when cooperating to build up high-level features.
- CallAppTesting, we will test the overall application functionality; thus, this is an integration test between `calparser`, `calfunctions`, and `calapp`.

## Sharing and Reuse

- This is done using a central module repository called Ballerina Central. This also allows us to conveniently reuse these modules for our different projects.
- Using Ballerina Central, we can browse the existing modules that are available for use. This is possible using the ballerina tool or from the web UI, which can be reached at [http://central.ballerina.io](http://central.ballerina.io).
- After a module search, we can pull a specific module to the local module cache by running the `ballerina pull <module>` command. Note that even if we don’t perform this step beforehand, when we import this module, and at compile time, it will be automatically pulled from Ballerina Central.

```sh
> ballerina search email
> ballerina pull hemikak/smtp
```

## Publishing Modules

- Create an account in Ballerina Central
- As per the instructions given on the page, we need to copy the Ballerina token and paste it inside <USER_HOME>/.ballerina/Settings.toml.
- Here, we can create an organization that we would like to use to publish our modules. The name of the organization we create here should match the organization name we put in the Ballerina project’s Ballerina.toml file.

```sh
> ballerina build -c calparser
> ballerina push calparser
```
