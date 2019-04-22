# xmlclimodeler
Framework that enables modelling command line interface utilities as XML files. Currently in design phase.

Functional and Design Specification

XML modeling of Command Line Interface (CLI)

1.	Introduction
The purpose of this document is to outline the design of a tool, which would help in simplifying the definition and design of command line interfaces. At present, every command line interface is being coded individually and the CLI command handling and input validations are all done using code which is written separately for each option in the command line interface. 

This tool is intended to reduce the amount of coding required each time a new command line tool is built and this tool also makes the CLI capable of reusing the command handling and validation code.

2.	What is the significance of this tool ?

	This tool is aimed at reducing the development and maintenance cost of coding command line interfaces for any application.
	This tool will be the SDK for generating easy-to-use command line interfaces.
	Eases the basic modification/deletion of command line options without touching any of the code written for it.
	Avoid writing duplicate code for common input validations and only define it in the XML.
	Enables CLI tool options to be written in any programming language or interpreted scripting languages and integrate it all into one install-anywhere archive.
	Tool is very lightweight and has a very low footprint on the development environment.

3.	Industrial benefits

	Huge impact in developer productivity. By using this tool, developer can now focus more on the business logic rather than investing time on coding monotonous user interface logic.

	Brings in a big advantage that any application in the industry can expose their command line options through an archive, which is simple to develop, build, install and use.

	Through this tool, applications can build CLIs which internally uses their own published SDK APIs.

	The base definition in XML can consolidate the CLI operations written in various languages which makes it much easier for the developer.

4.	Requirements

1.	Use an XML configuration file to configure the main CLI utility and its options in the XML format.
2.	Able to configure the command line option execution and validation modules.
3.	Standard modules for handling common input validations.
4.	Ability to use CLI option handling modules in any language like Java, C languages, Shell/Python scripts, Ant targets, Webservice calls, Database queries, PL/SQL etc.
5.	Optional and configurable user privilege management features to limit the use and scope of certain verbs.
6.	Option to temporarily disable the command line option or action verb.
7.	Native commands built in the tool to build and deploy the CLI.

5.	Terminologies used in this document

1.	Command Line Interface or CLI – This is the base command line interface. Example “empmanager”.

2.	CLI action – The command line interface action. For example the action “add” in “empmanager add”

6.	Design
6.1	CLI definition in XML

To illustrate the design, let us take the example of a command line tool empmanager which would facilitate the CRUD operations on an employee database.

$ empmanager  add –name=john.doe –email=john.doe@acme.com

$ empmanager delete –name=john.doe

$ empmanager notify –name=john.doe –content “Hello John !!”


Following is the XML snippet which models the above command line utility as an XML.


Line 1: <cli name=”empmanager”>

Line 2:    <cliaction name=”add”
     type=”java” 
     class=”com.example.AddEmp” 
     archiveLoc=”/tmp/emp1.jar”/>

Line 3:	       <input name=”name” type=”string” nullable=”false”/>
		       <input name=”age” type=”number” nullable=”false”/>
		       <input name=”email” type=”string” nullable=”false”/>
		       <input name=”dob” type=”date” nullable=”true”/>
		       <input name=”zipcode” type=”number” nullable=”true”/>
</cliaction>

<cliaction name=”delete” 
    type=”java” 
    class=”com.example.DeleteEmp”      
    archiveLoc=”application.classpath”/>

		      <input name=”name” type=”string” nullable=”false”/>
	        </cliaction>

Line 4:   <cliaction name=”notify” 
                                 type=”exe” 
    executable=”/scratch/send_mail_to_user.ksh”/>
		    <input name=”name” type=”string” nullable=”false”/>
	        </cliaction>
             </cli>


Line 0 – <cli> This is the tag which defines the main command line interface 		           which is “smpcc” in our case.

Line 2 – 	<cliaction> This tag defines the action to be performed through the command line interface and its type. This can be “java”, “exe”, “perl”, “.sql”, “PLSQL” and so on. “classname” attribute of this tag would define the java class which implements the “Add Employee” operation. The implementation java classes will be loaded as part of the tool classloader which will be explained in a later section.

	This tag can also have an attribute setting archiveLoc=”application.classpath” which would facilitate the command line action to use the business logic from the main application.
	

Line 3 - 	<input> This tag defines the input arguments that the cliaction operates on.

      Line 4 - 	<cliaction> tag is defining an action “notify” which would use the “executable” to send a notification to the user using a shell script.

6.2	CLI Definition XML parser
XML parser based on clibuilder.xsd will parse the CLI definition XML.

XML parser features:
a.	Parse the XML and validate the dependencies like class, exe path etc.

b.	Create a compiled version of the XML in binary which would be bundled with the CLI archive. 

This is done so that no accidental changes can creep into the XML definition once it is compiled.
6.3	Building the CLI archive

a.	The tool which is published as an archive, clibuilder.jar can be unpacked into any location. This tool archive comes with a utility build_cli, which is an executable.

b.	build_cli would parse the XML and build a CLI archive. For e.g., empmanager.jar in our example below.

c.	CLI archive would collect all the business logic implementation code from the definition XML and add it to the output CLI archive.

d.	Following directory structure is built into the CLI command archive.

Sample directory structure for the “empmanager” CLI.

empmanager |
		 |__ bin
			 |__ empmanager (This is the executable which the tool generates.)
		 |__ java
		 |__ lib
		 |__ perl
		 |__ shell
6.4	Tool at runtime – Steps for end-user to use the CLI archive.

	It is basically as simple as “unpack and use”.

	Install the CLI archive jar file into any folder.

	The CLI executable is bundled in the CLI archive. (“empmanager” in our example)

	CLI executable will be doing the java class loading at runtime, which would be required for loading the java based actions.





7.	Tool flow chart

7.1	Top level flow chart for the development time activities



