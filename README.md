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


> Written by [Scott Johnson](https://github.com/IlScottlI).