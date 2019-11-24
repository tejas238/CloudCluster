# CloudCluster

This guide will run you through setting up MongoDB on a cloud provider and connecting to the database instance remotely via C#. _**Note: You must whitelist your IP address for clusters to work correctly**_.

1. [Build / Run](#Build-/-Run)
2. [Visual Studio Setup](#Visual-Studio-Setup) ("Visual Studio" referred as "VS")
3. [Atlas Setup](#Atlas-Setup)
    * [Atlas Firewall](#Atlas-Firewall)
    * [Establish Connection](#Establish-Connection)
4. [GCP Setup](#GCP-Setup)
    * [GCP Firewall](#GCP-Firewall)
5. [TODOs](#TODOs)
6. [Stretch Goals](#Stretch-Goals)
7. [Resources](#Resources)
    * [Tooling Versions](#Tooling-Versions)
<br>
<hr/>

## Build / Run
* Clone the project: `git clone git@github.com:PieMyth/CloudCluster.git`
* Setup the solution in [Visual Studio](#Visual-Studio-Setup) and Clusters prior to running the project
* Run the project: `dotnet run`
* Test the project: `dotnet test`
<br>
<hr/>

## Visual Studio Setup
1. Select the solution in order to build the driver correctly. This can be found in the `CloudCluster` directory: `CloudCluster.sln`
2. Copy the connection string and put it in the connection_string variable in the program file
3. Via the Nuget package manager in VS, install relevant packages:
    - Mongo C# Driver Packages
      * ***MongoDB.Driver.Core***
      * ***MongoDB.Bson***
      * ***MongoDB.Driver***
    - Logging Packages
      - ***NLog***
      - ***NLog.Config***
    - If importing cluster data with C#
      - ***CsvHelper***
4. If importing .csv or .json files from a folder, edit the sources variable to point to the import folder location
    - Note: Do not have the imported file open in any other programs, or VS can't get a lock on the file
<br>
<hr/>

## Atlas Setup
1. Navigate to the MongoDB Atlas website
1. Build a new cluster
1. Choose a cloud provider (AWS/GCP/Azure)
1. Choose a region that has FREE TIER AVAILABLE
1. Create Cluster

**Wait for it to deploy**
(You should get an e-mail when it is complete)

### Atlas Firewall
*For external access to database*
1. In MongoDB Atlas, go to Security > Database Access
1. Add a new admin user, remember the password
1. Go to Security > Network Access
1. Add IP Address
1. Press 'Add Current IP Address'
1. If you want to accept a range of IPs, use a CIDR mask and refer to the lowest ip in the range
      - Ex: For anywhere on PSU, do 131.252.0.0/16

### Establish Connection
1. In MongoDB Atlas, go to Altas > Clusters
1. Click *Connect*
1. For connection via cmd or shell:
      1. Select *Connect with the Mongo Shell*
      1. Copy the string from step 3
      1. Paste into cmd or shell, enter password when prompted
1. For connection via C# or Python
      1. Select *Connect Your Application*
      1. Select the appropriate Driver (C#; 2.5 or later)
      1. Copy the connection string
      1. Replace \<password\> with your cluster user's password
1. You do not need to turn on or turn off clusters as Atlas manages this for you
<br>
<hr/>

## GCP Setup
From the first link, 'MongoDB on Compute Engine'...
1. Click *Launch on Compute Engine*
    - It should bring you to a page with *New MongoDB deployment*
1. Zone: **us-west1-x**

**Servers Tier**
1. Instances Count: 2
1. Machine type: small (1 shared vCPU)
1. Data disk size in GB (20)

**Arbiters Tier**
1. Instances Count: 1
1. Machine type: small (1 shared vCPU)`

**Wait for it to deploy...**
(You should get an e-mail when it is complete)

1. SSH connect to any node and run `mongo`; `rs.status()` to check the replica staus
1. Power off all machines when you are done

### GCP Firewall
*For external access to the database*

1. In GCP, expand the hanburger menu
1. Under Networking, VPC network > Firewall rules
1. Create firewall rule
1. Enter some name, like `allow-psu`
1. Direction of traffic > ingress
1. Targets > All instances in the network
1. Source filter > IP range
1. For PSU, enter **131.252.0.0/16** _(16 is the CIDR subnet mask for 255.255.0.0, 24 is for 255.255.255.0)_
    - Else, go to **https://who.is/** to see your public IP
1. Protocols and ports > Specified protocols and ports
1. In the box, enter: **tcp:27017**
1. Save
<br>
<hr/>

## TODOs
- Try and catch blocks for queries and db connection attempts
- Set up tests for TDD?
- Set up Atlas clusters on AWS, GCP, and Azure
- Use cursors to parse through large query results
- Set up indexes after importing data
- Add our report .pdfs onto github
- Add our presentation onto github
- Implement at least 4 queries
- Utilize .explain() to get query result metadata
- Add code for reporting query performance (ex. python's pychart library)
<br>
<hr/>

## Stretch Goals
- Execute import & queries asynchronously
- Log errors onto a local file (set this up in the Nlog.Config file)
- Create a front end application to connect to & display results from our driver
- Use google sentiment analysis API to to get  reviewer sentiment from listings (ex. How do users generally feel about this host/listing?)
- Explore various data models (ex. w/ embedded documents for grouping host info, geospatial info, etc.)
    - Multikey indexes on embedded documents
- Explore using GeoJSON file
- Explore sharding
- Explore replication & performance penalty during an election
- Use a service account to connect to the db rather than IP whitelisting
- **Set up identical clusters on AWS, GCP, Azure and compare performance vs. Altas**
    - If we figure out the service account, this is very do-able! It would just be a change of connection string.
<br>
<hr/>

## Resources
<i>

[Airbnb data sets](http://insideairbnb.com/get-the-data.html)

[MongoDB on Compute Engine](https://console.cloud.google.com/marketplace/details/click-to-deploy-images/mongodb)

[MongoDB Compute Engine Tutorial](https://blog.codecentric.de/en/2018/03/cloud-launcher-mongodb-google-compute-engine/)

[MongoDB Atlas](https://cloud.mongodb.com)

[C# MongoDB Examples](https://www.codementor.io/pmbanugo/working-with-mongodb-in-net-1-basics-g4frivcvz)

[C# Async & Await Explanation](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/)

</i>

### Tooling Versions
* Visual Studio 2019
<hr/>
