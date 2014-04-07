<table width="100%">
<tr>
<td>
<img src="http://homepages.cwi.nl/~hills/images/logo.png" height="50px"/>
</td>
<td align="right">
<img src="http://homepages.cwi.nl/~pietrzak/img/1b_CWI_LogoPMS193.png" height="50px"/>
</td>
</tr>
</table>
## Hack your DSL with Rascal
### Introduction to the Rascal IDE
---
### Setting up Rascal
1.	Download the Java JDK 7, available at:<http://www.oracle.com/technetwork/es/java/javasebusiness/downloads/index.html>2.	Download standard Eclipse Kepler available at: <http://www.eclipse.org/downloads/> 3.	Follow these steps to install the plugin into Eclipse:
	1. Start Eclipse.	2. Select *Help → Install New Software*.	3. Make sure that the tick for *Contact all update sites during install to find required software* is enabled.	4. Type: <http://update.rascal-mpl.org/unstable/> in the *Work with* edit box.	5. Select the feature *Rascal*.	6. Select *Next* (several times) and accept the software license. The process may take a few minutes!	7. Once these features have been installed, restart Eclipse.
4. The Rascal perspective should be displayed. If not, go to* Window → Open Perspective → Other...* and select **Rascal**.

### Setting up the Questionnaire projectWe provide the Questionnaire project in a Git repository. It is possible to directly import it from Eclipse, or using your favorite Git client.#### Using Git from Eclipse1.	Select *File → Import…*2.	At the Import dialog, select *Git→ Projects from Git* and select *Next*.4.	At the *Select Repository Source* dialog, select *URI*, and then press *Next*.
5.	At the *Source Git Repository dialog*, type <https://github.com/cwi-swat/rascal-ql-tutorial-prototype.git> in the URI text box, and then click on the *Finish* button (if the button *Finish* is not enabled, press *Next* several times until you reach the end of the wizard).
6.	The project **RascalQLTutorial** should be now in your workspace.#### Using Git with the command line or another GIT client1.	Clone the repository at <https://github.com/cwi-swat/rascal-ql-tutorial-prototype.git>2.	Select *File → Import…*3.	At the Import dialog, select *General → Existing Projects into Workspace*, and then press *Next*,4.	Being checked the *Select root directory* radio button, click on the *Browse* button next to it.5.	Browse your file system to search the root folder of the project you just cloned.
6.	Click on the *Finish* button.7.	The project **RascalQLTutorial** should be now in your workspace.### Rascal IDE OverviewThis is a snapshot of the **Rascal IDE**:
<div align="left"><img src="https://raw.githubusercontent.com/cwi-swat/rascal-ql-tutorial/master/Handouts/images/rascal-ide.png" width="90%"
</span>

On the left, the *Rascal Navigator* shows a list of the current projects (in this case, the Rascal Tutorial project). On the central area, a questionnaire file has been opened featuring syntax highlighting. An outline view is presented on the right.

A very important feature for the Rascal developer is the **Console**, where Rascal code can be directly evaluated in the interpreter. A console instance is associated to a given project, so in this case the user should select a file in the *RascalQLTutorial* project and then press the *Start Console* button (see in the image where the cursor points at).

Whenever the language being developed in the editor has been altered, it is possible to press the refresh button to reflect the changes in the IDE.





