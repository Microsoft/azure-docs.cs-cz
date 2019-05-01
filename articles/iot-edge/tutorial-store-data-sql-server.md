---
title: Kurz ukládání dat s modulem SQL – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak místně ukládat data na zařízení IoT Edge pomocí modulu SQL Serveru.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 86aab19eb0203e75fb8586adbdeb3f6fff9d14bd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575459"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Store dat na hraničních zařízeních s databází SQL serveru

Nasazení modulu SQL serveru k ukládání dat na zařízení s Linuxem s Azure IoT Edge.

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge má schopnosti základní úložiště do mezipaměti zprávy, pokud zařízení přejde do režimu offline a potom je předejte, když se obnoví připojení. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Zařízení IoT Edge můžete použít místní databáze provádět složitější výpočty, aniž byste museli udržovat připojení ke službě IoT Hub. 

Tento článek obsahuje pokyny k nasazení databáze SQL Serveru do zařízení IoT Edge. Služba Azure Functions spuštěná na zařízení IoT Edge strukturuje příchozí data a pak je odesílá do databáze. Kroky v tomto článku je možné použít i pro další databáze, které fungují v kontejnerech, jako je MySQL nebo PostgreSQL.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Nasazení databáze SQL do zařízení IoT Edge
> * Sestavení modulů a jejich nasazení do zařízení IoT Edge pomocí Visual Studio Code
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu, byste měli mít splněné následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linuxovému zařízení s Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovanou [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurované ke spuštění kontejnerů Linuxu.

Tento kurz používá modul služby Azure Functions k odesílání dat do SQL serveru. K vývoji modul IoT Edge s využitím Azure Functions, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [C#pro rozšíření Visual Studio Code (využívající omnisharp) pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí Visual Studio Code a nástroje Azure IoT.

1. Otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Do palety příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení, jako je třeba **SqlSolution**, nebo přijměte výchozí nastavení. |
   | Vyberte šablonu modulu | Zvolte **Azure Functions – C#** . |
   | Zadejte název modulu | Zadejte název modulu **sqlFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Konečný řetězec vypadá jako \<název registru\>.azurecr.io/sqlFunction. |

   V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet C moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat, jakou architekturu vývoji cílíte jednotlivých řešení, protože kontejneru je sestaven a jinak spusťte pro každý typ architektury. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku VS Code, Otevřít **moduly** > **sqlFunction** > **sqlFunction.cs**.

2. Celý obsah souboru nahraďte následujícím kódem:

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

3. V řádku 35, nahraďte řetězec **\<připojovací řetězec sql\>** s následující řetězec. **Zdroj dat** vlastnost odkazuje na kontejner SQL serveru, který ještě neexistuje, ale vytvoří s názvem **SQL** v další části. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Uložit **sqlFunction.cs** souboru. 

5. Otevřít **sqlFunction.csproj** souboru.

6. Najít skupinu odkazy na balíček a přidat nový zahrnout SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Uložit **sqlFunction.csproj** souboru.

## <a name="add-the-sql-server-container"></a>Přidat kontejner SQL serveru

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. Poskytuje kód, který vlastní modul funkce v předchozí části, ale modul SQL Server je již vytvořené a k dispozici na webu Azure Marketplace. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení. 

1. Ve Visual Studio Code otevřete paletu příkazů tak, že vyberete **zobrazení** > **paletu příkazů**.

2. Do palety příkazů zadejte a spusťte příkaz **Azure IoT Edge: Přidat modul IoT Edge**. V paletu příkazů zadejte následující informace, chcete-li přidat nový modul: 

   | Pole | Hodnota | 
   | ----- | ----- |
   | Vyberte soubor šablony nasazení | Paleta příkazů zvýrazní deployment.template.json soubor v aktuální složce řešení. Vyberte tento soubor.  |
   | Vyberte šablonu modulu | Vyberte **modulu z Azure Marketplace**. |

3. Na webu Marketplace modulu Azure IoT Edge, vyhledejte a vyberte **modul SQL Server**. 

4. Změňte název modulu, aby **sql**, malými písmeny. Tento název odpovídá názvu kontejneru deklarované v připojovacím řetězci v souboru sqlFunction.cs. 

5. Vyberte **Import** přidat modul do vašeho řešení. 

6. Ve složce řešení, otevřete **deployment.template.json** souboru. 

7. Najít **moduly** oddílu. Měli byste vidět tři moduly. V modulu *tempSensor* je zahrnuta v nové řešení ve výchozím nastavení a poskytuje testovací data pro použití s ostatní moduly. V modulu *sqlFunction* je modul, který se původně vytvořený a aktualizovaný s novým kódem. Nakonec modul *sql* byl importován z Azure Marketplace. 

   >[!Tip]
   >Modul SQL Server obsahuje výchozí heslo nastavit v proměnné prostředí v manifestu nasazení. Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zavřít **deployment.template.json** souboru.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. Modul SQL Server je veřejně hostovány společnosti Microsoft, ale budete muset kontejnerizace kódu v tomto modulu funkce. V této části sestavte řešení, vytvářet Image kontejneru pro modul sqlFunction a nasdílejte image do vašeho registru kontejneru. 

1. Ve Visual Studio Code otevřete integrovaný terminál tím, že vyberete **Zobrazit** > **Terminál**.  

1. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Použijte stejné přihlašovací údaje služby Azure Container Registry (ACR), které jste přidali do souboru .env. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Může se zobrazit upozornění zabezpečení doporučující použití parametru – heslo stdin. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenčních příkazu. 

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy vytváření kódu, kontejnerizace modulu a potom nasdílejte kód do registru kontejneru, který jste zadali při inicializaci řešení. 

Můžete ověřit, že byl modul sqlFunction byla úspěšně vložena do vašeho registru kontejneru. Na webu Azure Portal přejděte do vašeho registru kontejneru. Vyberte **úložišť** a vyhledejte **sqlFunction**. Další dva moduly, tempSensor a sql, nebude nahrány do vašeho registru kontejneru, vzhledem k tomu, že jste již odkazuje na jejich úložiště v registrů společnosti Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge. 

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

2. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

3. V Průzkumníku souborů přejděte **config** složky ve vašem řešení a zvolte **deployment.amd64**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

   Nepoužívejte deployment.template.json soubor jako manifest nasazení.

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. 

Aktualizujte stav vašeho zařízení v části zařízení Azure IoT Hub VS Code. Nové moduly jsou uvedené, se spustí do sestavy, jakoby běžely prostřednictvím několika dalších minut, jako jsou kontejnery nainstalovaný a spuštěný. Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. Modul tempSensor generuje simulovaná data prostředí. Modul sqlFunction přebírá data a formátuje je pro databázi. Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě. 

Spusťte následující příkazy na zařízení IoT Edge. Tyto příkazy připojení k **sql** modulu běžícího ve vašem zařízení a vytvořte databázi a tabulku pro uchovávání dat teploty odesílané do něj. 

1. V nástroji příkazového řádku na vašem zařízení IoT Edge připojení k vaší databázi. 
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

   ![Zobrazit obsah z místní databáze](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul Azure Functions obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

Pokud budete chtít zkusit jinou metodu úložiště na hraničních zařízeních, přečtěte si informace o tom, jak používat úložiště objektů Blob v Azure na hraničních zařízeních IoT. 

> [!div class="nextstepaction"]
> [Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT](how-to-store-data-blob.md)
