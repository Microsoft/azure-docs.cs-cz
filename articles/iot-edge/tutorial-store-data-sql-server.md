---
title: Tutorial store data with SQL module - Azure IoT Edge | Microsoft Docs
description: Zjistěte, jak místně ukládat data na zařízení IoT Edge pomocí modulu SQL Serveru.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5a3133100621cee2e786c4001df02f2316b1e4ec
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457062"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Ukládání dat na hraničních zařízeních s využitím databází SQL Serveru

Deploy a SQL Server module to store data on a Linux device running Azure IoT Edge.

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge has basic storage capabilities to cache messages if a device goes offline, and then forward them when the connection is reestablished. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Your IoT Edge devices can use local databases to perform more complex computing without having to maintain a connection to IoT Hub. 

Tento článek obsahuje pokyny k nasazení databáze SQL Serveru do zařízení IoT Edge. Služba Azure Functions spuštěná na zařízení IoT Edge strukturuje příchozí data a pak je odesílá do databáze. Kroky v tomto článku je možné použít i pro další databáze, které fungují v kontejnerech, jako je MySQL nebo PostgreSQL.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Nasazení databáze SQL do zařízení IoT Edge
> * Sestavení modulů a jejich nasazení do zařízení IoT Edge pomocí Visual Studio Code
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

This tutorial uses an Azure Functions module to send data to the SQL Server. To develop an IoT Edge module with Azure Functions, install the following additional prerequisites on your development machine: 

* [C# for Visual Studio Code (powered by OmniSharp) extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

The following steps show you how to create an IoT Edge function using Visual Studio Code and the Azure IoT Tools.

1. Otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Enter a descriptive name for your solution, like **SqlSolution**, or accept the default. |
   | Vyberte šablonu modulu | Choose **Azure Functions - C#** . |
   | Zadejte název modulu | Zadejte název modulu **sqlFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>The final string looks like \<registry name\>.azurecr.io/sqlfunction. |

   V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. In the VS Code explorer, open **modules** > **sqlFunction** > **sqlFunction.cs**.

2. Replace the entire contents of the file with the following code:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.       
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

3. In line 35, replace the string **\<sql connection string\>** with the following string. The **Data Source** property references the SQL Server container, which doesn't exist yet but you will create it with the name **SQL** in the next section. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Save the **sqlFunction.cs** file. 

5. Open the **sqlFunction.csproj** file.

6. Find the group of package references, and add a new one to include SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Save the **sqlFunction.csproj** file.

## <a name="add-the-sql-server-container"></a>Add the SQL Server container

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. You provided the code to make a customized Function module in the previous section, but the SQL Server module is already built and available in the Azure Marketplace. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení. 

1. In Visual Studio Code, open the command palette by selecting **View** > **Command palette**.

2. In the command palette, type and run the command **Azure IoT Edge: Add IoT Edge module**. In the command palette, provide the following information to add a new module: 

   | Pole | Hodnota | 
   | ----- | ----- |
   | Vyberte soubor šablony nasazení | The command palette highlights the deployment.template.json file in your current solution folder. Select that file.  |
   | Vyberte šablonu modulu | Select **Module from Azure Marketplace**. |

3. In the Azure IoT Edge module marketplace, search for and select **SQL Server Module**. 

4. Change the module name to **sql**, all lowercase. This name matches the container name declared in the connection string in the sqlFunction.cs file. 

5. Select **Import** to add the module to your solution. 

6. In your solution folder, open the **deployment.template.json** file. 

7. Find the **modules** section. You should see three modules. The module *SimulatedTemperatureSensor* is included by default in new solutions, and provides test data to use with your other modules. The module *sqlFunction* is the module that you initially created and updated with new code. Finally, the module *sql* was imported from the Azure Marketplace. 

   >[!Tip]
   >The SQL Server module comes with a default password set in the environment variables of the deployment manifest. Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Close the **deployment.template.json** file.

## <a name="build-your-iot-edge-solution"></a>Vytvoření řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. The SQL Server module is hosted publicly by Microsoft, but you need to containerize the code in the Functions module. In this section, you build the solution, create container images for the sqlFunction module, and push the image to your container registry. 

1. Ve Visual Studio Code otevřete integrovaný terminál tím, že vyberete **Zobrazit** > **Terminál**.  

1. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Use the same Azure Container Registry (ACR) credentials that you added to the .env file. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    You might see a security warning recommending the use of the --password-stdin parameter. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. For more information, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) command reference. 

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

When you tell Visual Studio Code to build your solution, it first takes the information in the deployment template and generates a deployment.json file in a new folder named **config**. Then, it runs two commands in the integrated terminal: `docker build` and `docker push`. These two commands build your code, containerize the module, and then push the code to the container registry that you specified when you initialized the solution. 

You can verify that the sqlFunction module was successfully pushed to your container registry. In the Azure portal, navigate to your container registry. Select **repositories** and search for **sqlFunction**. The other two modules, SimulatedTemperatureSensor and sql, won't be pushed to your container registry because you're already pointing to their repositories in the Microsoft registries.

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge. 

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

2. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

3. In the file explorer, navigate to the **config** folder inside your solution and choose **deployment.amd64**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

   Do not use the deployment.template.json file as a deployment manifest.

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. 

Refresh the status of your device in the Azure IoT Hub Devices section of VS Code. The new modules are listed and will start to report as running over the next few minutes as the containers are installed and started. Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. The SimulatedTemperatureSensor module generates simulated environment data. Modul sqlFunction přebírá data a formátuje je pro databázi. Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě. 

Run the following commands on your IoT Edge device. These commands connect to the **sql** module running on your device and create a database and table to hold the temperature data being sent to it. 

1. In a command-line tool on your IoT Edge device, connect to your database. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Otevřete nástroj příkazového řádku SQL.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Vytvořte databázi: 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definujte tabulku.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Souboru Dockeru pro SQL Server můžete upravit tak, aby automaticky nastavil nasazení SQL Serveru do několika zařízení IoT Edge. Další informace najdete v [ukázkovém projektu kontejneru Microsoft SQL Serveru](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Zobrazení místních dat

Po vytvoření tabulky začne modul sqlFunction ukládat data do místní databáze SQL Serveru 2017 na vašem zařízení IoT Edge. 

Spuštěním následujícího příkazu v nástroji příkazového řádku SQL zobrazte formátovaná tabulková data: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![View contents of local database](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul Azure Functions obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

If you want to try another storage method at the edge, read about how to use Azure Blob Storage on IoT Edge. 

> [!div class="nextstepaction"]
> [Ukládání dat na hraničních zařízeních s využitím služby Azure Blob Storage ve službě IoT Edge](how-to-store-data-blob.md)
