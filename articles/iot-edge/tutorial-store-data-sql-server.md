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
ms.custom: mvc
ms.openlocfilehash: 5a3133100621cee2e786c4001df02f2316b1e4ec
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457062"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Ukládání dat na hraničních zařízeních s využitím databází SQL Serveru

Nasaďte modul SQL Server pro ukládání dat do zařízení se systémem Linux se systémem Azure IoT Edge.

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge má schopnosti základní úložiště do mezipaměti zprávy, pokud zařízení přejde do režimu offline a potom je předejte, když se obnoví připojení. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Vaše zařízení IoT Edge můžou pomocí místních databází provádět složitější výpočetní prostředí, aniž by bylo nutné navázání připojení k IoT Hub. 

Tento článek obsahuje pokyny k nasazení databáze SQL Serveru do zařízení IoT Edge. Služba Azure Functions spuštěná na zařízení IoT Edge strukturuje příchozí data a pak je odesílá do databáze. Kroky v tomto článku je možné použít i pro další databáze, které fungují v kontejnerech, jako je MySQL nebo PostgreSQL.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Nasazení databáze SQL do zařízení IoT Edge
> * Sestavení modulů a jejich nasazení do zařízení IoT Edge pomocí Visual Studio Code
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste si měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu byste měli mít následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Linux se spuštěným Azure IoT Edge](quickstart-linux.md)
* Registr kontejneru, například [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

V tomto kurzu se k posílání dat do SQL Server používá modul Azure Functions. Pokud chcete vytvořit modul IoT Edge s Azure Functions, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [pro Visual Studio Code rozšíření Visual Studio Code (využívá OmniSharp). C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí Visual Studio Code a nástrojů Azure IoT.

1. Otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení, třeba **SqlSolution**, nebo přijměte výchozí nastavení. |
   | Vyberte šablonu modulu | Vyberte **Azure Functions – C#** . |
   | Zadejte název modulu | Zadejte název modulu **sqlFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Výsledný řetězec vypadá jako \<název registru\>. azurecr.io/sqlfunction. |

   V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly C pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge**nebo vyberte ikonu zástupce na bočním panelu v dolní části okna. 

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku VS Code otevřete **moduly** > **sqlFunction** > **sqlFunction.cs**.

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

3. Na řádku 35 nahraďte řetězec **\<připojovací řetězec sql\>** následujícím řetězcem. Vlastnost **zdroje dat** odkazuje na kontejner SQL Server, který ještě neexistuje, ale v další části ho vytvoříte s názvem **SQL** . 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Uložte soubor **sqlFunction.cs** . 

5. Otevřete soubor **sqlFunction. csproj** .

6. Vyhledejte skupinu odkazů na balíčky a přidejte novou pro zahrnutí SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Uložte soubor **sqlFunction. csproj** .

## <a name="add-the-sql-server-container"></a>Přidat kontejner SQL Server

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. Zadali jste kód pro vytvoření přizpůsobeného modulu funkcí v předchozí části, ale modul SQL Server je již sestaven a dostupný v Azure Marketplace. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení. 

1. V Visual Studio Code otevřete paletu příkazů výběrem možnosti **zobrazit** > **paleta příkazů**.

2. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: Add IoT Edge Module**. V paletě příkazů zadejte následující informace, chcete-li přidat nový modul: 

   | Pole | Hodnota | 
   | ----- | ----- |
   | Vyberte soubor šablony nasazení | Paleta příkazů zvýrazní soubor Deployment. template. JSON ve vaší aktuální složce řešení. Vyberte tento soubor.  |
   | Vyberte šablonu modulu | Vyberte **modul z Azure Marketplace**. |

3. V tržišti Azure IoT Edge modulu vyhledejte a vyberte **SQL Server modul**. 

4. Změňte název modulu na **SQL**, malými písmeny. Tento název odpovídá názvu kontejneru deklarovanému v připojovacím řetězci v souboru sqlFunction.cs. 

5. Vyberte **importovat** a přidejte modul do svého řešení. 

6. Ve složce řešení otevřete soubor **Deployment. template. JSON** . 

7. Vyhledejte část **moduly** . Měli byste vidět tři moduly. Modul *SimulatedTemperatureSensor* je ve výchozím nastavení zahrnutý v nových řešeních a poskytuje testovací data pro použití s ostatními moduly. Modul *sqlFunction* je modul, který jste původně vytvořili a aktualizovali pomocí nového kódu. Nakonec byl modul *SQL* importován z Azure Marketplace. 

   >[!Tip]
   >Modul SQL Server obsahuje výchozí heslo nastavené v proměnných prostředí manifestu nasazení. Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zavřete soubor **Deployment. template. JSON** .

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. Modul SQL Server je veřejně hostovaný Microsoftem, ale je potřeba kontejnerizace kód v modulu Functions. V této části sestavíte řešení, vytvoříte image kontejneru pro modul sqlFunction a nahrajete image do registru kontejneru. 

1. Ve Visual Studio Code otevřete integrovaný terminál tím, že vyberete **Zobrazit** > **Terminál**.  

1. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Použijte stejné přihlašovací údaje Azure Container Registry (ACR), které jste přidali do souboru. env. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Může se zobrazit upozornění zabezpečení, které doporučuje použití parametru--password-stdin. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v referenčních informacích k příkazu [Docker Login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) . 

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když Visual Studio Code vystavíte řešení, nejprve převezme informace v šabloně nasazení a vygeneruje soubor Deployment. JSON v nové složce s názvem **config**. Potom spustí dva příkazy v integrovaném terminálu: `docker build` a `docker push`. Tyto dva příkazy vytváření kódu, kontejnerizace modulu a potom nasdílejte kód do registru kontejneru, který jste zadali při inicializaci řešení. 

Můžete ověřit, zda byl modul sqlFunction úspěšně vložen do registru kontejneru. V Azure Portal přejděte do registru kontejneru. Vyberte **úložiště** a vyhledejte **sqlFunction**. Ostatní dva moduly, SimulatedTemperatureSensor a SQL, se do registru kontejnerů nevloží, protože už odkazujete na úložiště v registrech Microsoft Registry.

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge. 

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

2. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

3. V Průzkumníku souborů přejděte do složky **Konfigurace** v rámci řešení a vyberte **Deployment. amd64**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

   Nepoužívejte soubor Deployment. template. JSON jako manifest nasazení.

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. 

Aktualizujte stav vašeho zařízení v části zařízení Azure IoT Hub VS Code. V seznamu jsou uvedené nové moduly, které se při instalaci a spuštění kontejnerů začnou nahlásit jako spuštěné v několika dalších minutách. Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. Modul SimulatedTemperatureSensor generuje Simulovaná data prostředí. Modul sqlFunction přebírá data a formátuje je pro databázi. Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě. 

Spusťte následující příkazy na zařízení IoT Edge. Tyto příkazy se připojí k modulu **SQL** běžícímu na vašem zařízení a vytvoří databázi a tabulku pro ukládání dat o teplotě, která se do ní odesílají. 

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


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul Azure Functions obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

Pokud chcete na hraničních zařízeních vyzkoušet jinou metodu úložiště, přečtěte si informace o tom, jak používat Azure Blob Storage v IoT Edge. 

> [!div class="nextstepaction"]
> [Ukládání dat na hraničních zařízeních s využitím služby Azure Blob Storage ve službě IoT Edge](how-to-store-data-blob.md)
