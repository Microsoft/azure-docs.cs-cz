---
title: Ukládání dat pomocí modulu SQL pro Azure IoT Edge | Microsoft Docs
description: Zjistěte, jak místně ukládat data na zařízení IoT Edge pomocí modulu SQL Serveru.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fc83546080111554446cb8f7b7ca97026f99e02e
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283418"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Ukládání dat na hraničních zařízeních s využitím databází SQL Serveru

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge má schopnosti základní úložiště do mezipaměti zprávy, pokud zařízení přejde do režimu offline a potom je předejte, když se obnoví připojení. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Díky začlenění místních databází můžou vaše zařízení IoT Edge provádět složitější výpočty, aniž by musela udržovat připojení ke službě IoT Hub. Například senzoru na počítači odesílá data do cloudu jednou za měsíc pro vytváření sestav a vylepšení modulu strojového učení. Ale pokud terénní technik pracuje na počítači, bude moct za posledních několik dnů dat ze snímačů místně.

Tento článek obsahuje pokyny k nasazení databáze SQL Serveru do zařízení IoT Edge. Služba Azure Functions spuštěná na zařízení IoT Edge strukturuje příchozí data a pak je odesílá do databáze. Kroky v tomto článku je možné použít i pro další databáze, které fungují v kontejnerech, jako je MySQL nebo PostgreSQL.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Nasazení databáze SQL do zařízení IoT Edge
> * Sestavení modulů a jejich nasazení do zařízení IoT Edge pomocí Visual Studio Code
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C#pro rozšíření Visual Studio Code (využívající omnisharp) pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu pomocí rozšíření Azure IoT Edge pro Visual Studio Code sestavíte modul a vytvořte **image kontejneru** ze souborů. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro uložení imagí kontejnerů, můžete použít jakýkoli registr kompatibilní s Dockerem. Jsou dvě oblíbené služby registrů Dockeru [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

    ![Vytvoření registru kontejnerů](./media/tutorial-deploy-function/create-container-registry.png)

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota | 
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. | 

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 

7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu a zajistit tak přístup do registru kontejneru. 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky. 

Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí Visual Studio Code a rozšíření Azure IoT Edge.

1. Otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení, jako je třeba **SqlSolution**, nebo přijměte výchozí nastavení. |
   | Vyberte šablonu modulu | Zvolte **Azure Functions – C#** . |
   | Zadejte název modulu | Zadejte název modulu **sqlFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. Konečný řetězec vypadá jako \<název registru\>.azurecr.io/sqlFunction. |

   V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge: složka \.vscode, složka s moduly a soubor šablony manifestu nasazení. a soubor \.env. 
   
4. Pokaždé, když vytvoříte nové řešení IoT Edge, VS Code vás vyzve k zadání přihlašovacích údajů registru v \.souboru env. Tento soubor je ignorován git a rozšíření IoT Edge použije později poskytnout přístup k registru do zařízení IoT Edge. Otevřít \.souboru env. 

5. V souboru .env zadejte pro modul runtime IoT Edge své přihlašovací údaje k registru, aby získal přístup k imagím vašich modulů. Najděte sekce **CONTAINER_REGISTRY_USERNAME** a **CONTAINER_REGISTRY_PASSWORD** a vložte své přihlašovací údaje za symbol rovnítka: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Uložte soubor .env.

7. V Průzkumníku VS Code, Otevřít **moduly** > **sqlFunction** > **sqlFunction.cs**.

8. Obsah souboru nahraďte následujícím kódem:

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
                           log.Info($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
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

6. V řádku 35, nahraďte řetězec **\<připojovací řetězec sql\>** s následující řetězec. **Zdroj dat** vlastnost odkazuje na název kontejneru systému SQL Server, kterou vytvoříte s názvem **SQL** v další části. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Uložit **sqlFunction.cs** souboru. 

## <a name="add-a-sql-server-container"></a>Přidání kontejneru SQL Serveru

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. Poskytuje kód, který vlastní modul funkce v předchozí části, ale modul SQL Server je už vytvořili. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení. 

1. V průzkumníku Visual Studio Code otevřete soubor **deployment.template.json**. 

2. Najít **moduly** oddílu. Měla by obsahovat dva moduly: modul**tempSensor**, který generuje simulovaná data, a váš modul **sqlFunction**.

3. Pokud používáte kontejnery Windows, upravte sekci **sqlFunction.settings.image**.

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. Přidáním následujícího kódu deklarujte třetí modul. Za sekci sqlFunction přidejte čárku a vložte:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "environment": "",
           "createOptions": ""
       }
   }
   ```

   ![Přidání kontejneru SQL Serveru](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. V závislosti na typu kontejnerů Docker vašeho zařízení IoT Edge aktualizujte parametry **sql.settings** tímto kódem:

   * Kontejnery Windows:

        ```json
        {
            "image": "microsoft/mssql-server-windows-developer",
            "environment": {
                "ACCEPT_EULA": "Y",
                "SA_PASSWORD": "Strong!Passw0rd"
            },
            "createOptions": {
                "HostConfig": {
                    "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
                    "PortBindings": {
                        "1433/tcp": [{"HostPort": "1401"}]
                    }
                }
            }
        }
        ```
 

   * Linuxové kontejnery:

        ```json
        {
            "image": "mcr.microsoft.com/mssql/server:latest",
            "environment": {
                "ACCEPT_EULA": "Y",
                "SA_PASSWORD": "Strong!Passw0rd"
            },
            "createOptions": {
                "HostConfig": {
                    "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
                    "PortBindings": {
                        "1433/tcp": [{"HostPort": "1401"}]
                    }
                }
            }
        }
        ```
    
    
   >[!Tip]
   >Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Uložte soubor **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. Teď je potřeba sestavit řešení, vytvořit pro moduly image kontejnerů a odeslat tyto image do registru kontejneru. 

1. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Použijte stejné přihlašovací údaje, které jste přidali do souboru .env. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    Zobrazí se výzva k zadání hesla. Vložte heslo do řádku (heslem je skryté kvůli zabezpečení) a stisknutím klávesy **Enter**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy vytváření kódu, kontejnerizace modulu a potom nasdílejte kód do registru kontejneru, který jste zadali při inicializaci řešení. 

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge. 

1. Na paletě příkazů VS Code vyberte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).

2. Podle pokynů se přihlaste ke svému účtu Azure. 

3. Na paletě příkazů vyberte své předplatné Azure a pak vaši službu IoT Hub. 

4. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

5. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-store-data-sql-server/create-deployment.png)

6. V Průzkumníku souborů přejděte do složky **config** uvnitř vašeho řešení a zvolte soubor **deployment.json**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. 

Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. Může to trvat několik minut.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. Modul tempSensor generuje simulovaná data prostředí. Modul sqlFunction přebírá data a formátuje je pro databázi. 

Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě. 

1. V nástroji příkazového řádku připojení k vaší databázi. 
   * Kontejner Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Kontejner Linuxu: 

      ```bash
      sudo docker exec -it sql bash
      ```

2. Otevřete nástroj příkazového řádku SQL.
   * Kontejner Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Kontejner Linuxu: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Vytvořte databázi: 

   * Kontejner Windows
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Kontejner Linuxu
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

   ![Zobrazení místních dat](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul Azure Functions obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

Pokračujte dalšími kurzy, ve kterých se seznámíte s dalšími způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Filtrování dat snímačů pomocí kódu C#](tutorial-csharp-module.md)
