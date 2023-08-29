#	MES Ignition Development Using Docker

## Getting Started

###  Apple Silicon Mac 

*	Download Docker Desktop 
		https://www.docker.com/products/docker-desktop/ 

*	Choose Apple Chip
	*	<img src="https://i.ibb.co/HYfqX5k/image.png" alt="image" border="0">

*	Drag and Drop Docker to Application Folder
	*	<img src="https://i.ibb.co/TP4G1km/image.png" alt="image" border="0">

Open Docker Desktop to initialize 

## Install Ignition (8.1.28) Hub and Spoke & SQL Database (Azure SQL Edge)
*	Docker Compose Hub and Spoke Command
```bash
ignition % docker compose up -d
```

## Download Azure Data Studio
*	[Azure Data Studio (Apple Silicon)](https://go.microsoft.com/fwlink/?linkid=2242554)
*	Add ***localhost*** server to Azure Data Studio
	*	Use the following login details to connect to the database

|  |  |
|--|--|
| Server | localhost |
| User name | sa |
| Password | yourStrong(!)Password |
| | |


*	Right click on Database and add New Database ***SCADA***, ***MES*** and ***MES_ENT***
	<img src="https://i.ibb.co/jkjB9v9/image.png" alt="image" border="0">

#	Setup Ignition Gateways

## Database Connection amd Sepasoft Module Configuration

* 	Configure database **Connect URL** `jdbc:sqlserver://mssql`
	<img src="https://i.ibb.co/Jpy28dn/image.png" alt="image" border="0">
###	Spoke
* Launch Spoke Gateway and Configure database connections for ***SCADA*** and ***MES***  
http://localhost:9088/web/config/database.connections

	<img src="https://i.ibb.co/7XdfwdS/image.png" alt="image" border="0">

*	Config `->` Mes `->` ***MES Module Settings*** 
	*	Choose MES for ***Runtime Database*** and ***Analysis Database*** then save changes.
	<img src="https://i.ibb.co/QfDdx0G/image.png" alt="image" border="0">

### Hub
* Launch Hub Gateway and Configure database connections for ***SCADA*** and ***MES_ENT***  
http://localhost:9888/web/config/database.connections

	<img src="https://i.ibb.co/GWKhhyH/image.png" alt="image" border="0">

*	Config `->` Mes `->` ***MES Module Settings*** 
	*	Choose MES_ENT for ***Runtime Database*** and ***Analysis Database*** then save changes.
	<img src="https://i.ibb.co/7zr30mb/image.png" alt="image" border="0">



## Sepasoft Constituting Hub and Spoke

## Open Designer
*	Open Hub `http://localhost:9888` using Ignition Designer and use this python code in the script console.

```python
enabled = 1
originalEnterpriseName = 'New Enterprise' #You need this to get the current Enterprise object to rename it. 
enterpriseName = 'JLG'
siteName = 'McConnellsburg'
areaName = 'McConnellsburg Assembly'
processCellName = 'McConnellsburg Final Assembly Processes' 
unitName = '50005'
phaseClassMain = '0000347'
phaseClassDocs = 'EQC Phases'
unitClass = '50005' 
recipeClassName = 'Master Routes'
# ON HUB
if enabled == 1:
	eqpLink = system.mes.getMESObjectLinkByName('Enterprise',originalEnterpriseName) 
	eqp = eqpLink.getMESObject()
	eqp.setName(enterpriseName)
	eqp.save()
# ON HUB
if enabled == 2:
	eqp = system.mes.createMESObject('Site')
	eqp.setName(siteName)
	eqp.setEquipmentPath(enterpriseName+'\\'+siteName)
	eqp.setActive(True)
	parentLink = system.mes.getMESObjectLinkByName('Enterprise',enterpriseName) 
	parent = parentLink.getMESObject()
	eqp.addParent(parent)
	eqp.save()
# ON HUB
if enabled == 6:
	phaseRoot = system.mes.batch.phase.getRootLink()
	eqcPhaseClass = system.mes.batch.phase.createClass(phaseClassMain, phaseRoot) 
	eqcPhaseClass.save()
	eqcPhasesPhaseClass = system.mes.batch.phase.createClass(phaseClassDocs, phaseRoot) 
	eqcPhasesPhaseClass.save()
# ON HUB
if enabled == 7:
	unitClassRoot = system.mes.batch.unitclass.getRootLink()
	unitClass = system.mes.batch.unitclass.create(unitClass, unitClassRoot) 
	noActionPhaseLink = system.mes.batch.phase.getPhaseLink('No Action') 
	unitClass.addPhase(noActionPhaseLink)
	unitClass.save()
# ON HUB
if enabled == 8:
	unitLink = system.mes.batch.unit.getLink(unitName) 
	unitClassLink = system.mes.batch.unitclass.getLink(unitClass) 
	system.mes.batch.unit.assignUnitClass(unitLink, unitClassLink)
# ON HUB
if enabled == 9:
	rootRecipeLink = system.mes.batch.recipe.getRootRecipeLink()
	recipeClass = system.mes.batch.recipe.createRecipeClass(recipeClassName,rootRecipeLink) 
	recipeClass.save()
```
*	Execute code in script console: 
	* Run script `enabled = 1`
	![Alt text](images/image.png)
	* Run script `enabled = 2`
	![Alt text](images/image-2.png)
	*	Verify script worked by launching the Hub MESGateway project and checking the MES Equiptment Manager
			![Alt text](images/image-1.png)
* 	Connect Gateway Network
	*	Config `->` NetworkGateway `->` Network Settings `->` Incoming Connections
	* Approve Connection
	![Alt text](images/image-3.png)
	* Approve Again
	![Alt text](images/image-4.png)
	
	* After Approving the second time your page should look like this. 
	![Alt text](images/image-5.png)

* 	Config `->` Mes `->` ***Enterprise Settings*** 
	*	Set as **Enterprise Root** then save changes.
	![Alt text](images/image-6.png)

* On the Spoke http://localhost:9088/web/config/mes.enterprisesettings Enterprise Settings `->` Hierarchy `->` Parent Gateway `->` choose ***Parent Gateway*** and type `McConnellsburg` under **Equipment Name**.
![Alt text](images/image-7.png)
	* Click on Add Equipment
		![Alt text](images/image-8.png)
		*	Click OK on the popup!
			![Alt text](images/image-10.png)
		#### SAVE CHANGES

	*	Verify Spoke has Synced Hub Equiptment 
	![Alt text](images/image-11.png)
## Open Designer
*	Open Spoke `http://localhost:9088` using Ignition Designer and use this python code in the script console.	

```python
enabled = 3
originalEnterpriseName = 'New Enterprise' #You need this to get the current Enterprise object to rename it. 
enterpriseName = 'JLG'
siteName = 'McConnellsburg'
areaName = 'McConnellsburg Assembly'
processCellName = 'McConnellsburg Final Assembly Processes' 
unitName = '50005'
phaseClassMain = '0000347'
phaseClassDocs = 'EQC Phases'
unitClass = '50005' 
recipeClassName = 'Master Routes'
# ON spoke
if enabled == 3:
	eqp = system.mes.createMESObject('Area')
	eqp.setName(areaName) 
	eqp.setEquipmentPath(enterpriseName+'\\'+siteName+'\\'+areaName) 
	eqp.setActive(True)
	parentLink = system.mes.getMESObjectLinkByName('Site',siteName) 
	parent = parentLink.getMESObject()
	eqp.addParent(parent)
	eqp.save()
#ON spoke
if enabled == 4:
	eqp = system.mes.createMESObject('ProcessCell')
	eqp.setName(processCellName) 
	eqp.setEquipmentPath(enterpriseName+'\\'+siteName+'\\'+areaName+'\\'+processCellName) 
	eqp.setActive(True)
	parentLink = system.mes.getMESObjectLinkByName('Area',areaName)
	parent = parentLink.getMESObject()
	eqp.addParent(parent)
	eqp.save()
# ON spoke
if enabled == 5:
	eqp = system.mes.createMESObject('Unit')
	eqp.setName(unitName) 
	eqp.setEquipmentPath(enterpriseName+'\\'+siteName+'\\'+areaName+'\\'+processCellName+'\\'+unitName) 
	eqp.setActive(True)
	parentLink = system.mes.getMESObjectLinkByName('ProcessCell',processCellName)
	parent = parentLink.getMESObject()
	eqp.addParent(parent)
	eqp.save()
```
*	Run Script `enabled = 3`
	![Alt text](images/image-12.png)
*	Run Script `enabled = 4`
	![Alt text](images/image-13.png)
*	Run Script `enabled = 5`
	![Alt text](images/image-14.png)

*	Verify script worked by launching the Hub MESGateway project and checking the MES Equiptment Manager
	![Alt text](images/image-15.png)

* Open Hub Designer Script Console
	* Run Script `enabled = 6`
	![Alt text](images/image-16.png)
	* Run Script `enabled = 7`
	![Alt text](images/image-17.png)
	* Run Script `enabled = 8`
	![Alt text](images/image-18.png)
	* Run Script `enabled = 9`
	![Alt text](images/image-19.png)
> Written by [Scott Johnson](https://github.com/IlScottlI).