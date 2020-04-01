---
title: Kurz – ukládání dat pomocí modulu SQL pomocí Azure IoT Edge
description: Tento kurz ukazuje, jak místně ukládat data na zařízení IoT Edge pomocí modulu SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944276"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Ukládání dat na hraničních zařízeních s využitím databází SQL Serveru

Nasazení modulu SQL Serveru pro ukládání dat na zařízení S Linuxem se systémem Azure IoT Edge.

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge má základní možnosti úložiště pro ukládání zpráv do mezipaměti, pokud zařízení přejde do režimu offline, a pak je přepošlete, když se připojení znovu navádí. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Vaše zařízení IoT Edge můžou používat místní databáze k provádění složitějších výpočtů, aniž by bylo třeba udržovat připojení k IoT Hubu.

Tento článek obsahuje pokyny k nasazení databáze SQL Serveru do zařízení IoT Edge. Služba Azure Functions spuštěná na zařízení IoT Edge strukturuje příchozí data a pak je odesílá do databáze. Kroky v tomto článku je možné použít i pro další databáze, které fungují v kontejnerech, jako je MySQL nebo PostgreSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Nasazení databáze SQL do zařízení IoT Edge
> * Sestavení modulů a jejich nasazení do zařízení IoT Edge pomocí Visual Studio Code
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Dokončením tohoto kurzu byste měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Zařízení AMD64 [s Linuxem se systémem Azure IoT Edge](quickstart-linux.md).
  * Arm zařízení, jako je Raspberry Pis, nelze spustit SQL Server. Pokud chcete používat SQL na zařízení ARM, můžete se zaregistrovat a vyzkoušet [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) ve verzi Preview.
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Tento kurz používá modul Azure Functions k odesílání dat na SQL Server. Chcete-li vyvinout modul IoT Edge s funkcemi Azure, nainstalujte do vývojového počítače následující další předpoklady:

* [C# pro Visual Studio Code (powered by OmniSharp) rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí kódu Visual Studia a nástrojů Azure IoT.

1. Otevřete Visual Studio Code.

2. Otevřete paletu příkazů VS Kód výběrem**palety Příkazy** **zobrazení** > .

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení, například **SqlSolution**, nebo přijměte výchozí hodnotu. |
   | Vyberte šablonu modulu | Zvolte **funkce Azure – C#**. |
   | Zadejte název modulu | Zadejte název modulu **sqlFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Konečný řetězec vypadá \<jako\>název registru .azurecr.io/sqlfunction. |

   V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době visual studio kód můžete vyvíjet moduly C pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku kódu VS otevřete **moduly** > **sqlFunction** > **sqlFunction.cs**.

2. Nahraďte celý obsah souboru následujícím kódem:

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

3. V řádku 35 nahraďte ** \<připojovací řetězec\> ** SQL řetězce řetězce množením. Vlastnost **Zdroj dat** odkazuje na kontejner serveru SQL Server, který ještě neexistuje. Vytvoříte jej s názvem **SQL** v další části.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Uložte **soubor sqlFunction.cs.**

5. Otevřete soubor **sqlFunction.csproj.**

6. Najděte skupinu odkazů na balíčky a přidejte nový, který bude obsahovat sqlclient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Uložte soubor **sqlFunction.csproj.**

## <a name="add-the-sql-server-container"></a>Přidání kontejneru serveru SQL Server

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. Zadali jste kód pro vytvoření modulu vlastní funkce v předchozí části, ale modul SQL Server je již vytvořený a dostupný na Azure Marketplace. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení.

1. V kódu sady Visual Studio otevřete paletu příkazů výběrem**palety Příkazy** **zobrazení** > .

2. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: Add IoT Edge module**. V paletě příkazů zadejte následující informace pro přidání nového modulu:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte soubor šablony nasazení | Paleta příkazů zvýrazní soubor deployment.template.json v aktuální složce řešení. Vyberte tento soubor.  |
   | Vyberte šablonu modulu | Vyberte **Modul z Azure Marketplace**. |

3. Na trhu modulu Azure IoT Edge vyhledejte a vyberte **modul SQL Serveru**.

4. Změňte název modulu na **sql**, všechna malá písmena. Tento název odpovídá názvu kontejneru deklarovanému v připojovacím řetězci v souboru sqlFunction.cs.

5. Vyberte **Importovat,** chcete-li přidat modul do vašeho řešení.

6. Ve složce řešení otevřete soubor **deployment.template.json.**

7. Najděte sekci **modulů.** Měli byste vidět tři moduly. Modul *SimulatedTemperatureSensor* je standardně součástí nových řešení a poskytuje testovací data pro použití s ostatními moduly. Modul *sqlFunction* je modul, který jste původně vytvořili a aktualizovali novým kódem. Nakonec byl modul *SQL* importován z Azure Marketplace.

   >[!Tip]
   >Modul SQL Server je dodáván s výchozím heslem nastaveným v proměnných prostředí manifestu nasazení. Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Zavřete soubor **deployment.template.json.**

## <a name="build-your-iot-edge-solution"></a>Vytvoření řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. Modul SQL Server je hostován veřejně společností Microsoft, ale je třeba kontejnerizovat kód v modulu Functions. V této části vytvoříte řešení, vytvoříte image kontejneru pro modul sqlFunction a předáte bitovou kopii do registru kontejnerů.

1. V kódu sady Visual Studio otevřete integrovaný terminál výběrem možnosti **Zobrazit** > **terminál**.  

1. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Použijte stejné přihlašovací údaje registru kontejnerů Azure (ACR), které jste přidali do souboru ENV. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Může se zobrazit upozornění zabezpečení doporučující použití parametru --password-stdin. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace naleznete v odkazu příkazu [přihlášení do dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. V průzkumníku kódu VS klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

Když sdělíte kódu sady Visual Studio, aby vytvořil i řešení, nejprve převezme informace v šabloně nasazení a vygeneruje soubor deployment.json v nové složce s názvem **config**. Poté spustí dva příkazy v integrovaném terminálu: `docker build` a `docker push`. Příkaz sestavení vytvoří váš kód a kontejnerizuje modul. Příkaz push pak odešle kód do registru kontejneru, který jste zadali při inicializace řešení.

Můžete ověřit, že modul sqlFunction byl úspěšně zasunut do registru kontejneru. Na webu Azure Portal přejděte do registru kontejnerů. Vyberte **repozitáře** a vyhledejte **sqlFunction**. Další dva moduly, SimulatedTemperatureSensor a SQL, nebudou zatlačeny do registru kontejneru, protože jejich repozitáře jsou již v registrech společnosti Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge.

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub).

2. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení).

3. V průzkumníku souborů přejděte do složky **konfigurace** uvnitř řešení a zvolte **deployment.amd64**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge).

   Nepoužívejte soubor deployment.template.json jako manifest nasazení.

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva.

Aktualizujte stav svého zařízení v části Zařízení Služby Azure IoT Hub v kódu VS. Nové moduly jsou uvedeny a začne hlásit jako spuštěné v průběhu několika příštích minut, jak jsou nainstalovány a spuštěny kontejnery. Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. Modul SimulatedTemperatureSensor generuje simulovaná data prostředí. Modul sqlFunction přebírá data a formátuje je pro databázi. Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě.

Spusťte následující příkazy na zařízení IoT Edge. Tyto příkazy se připojují k modulu **SQL** spuštěnému v zařízení a vytvářejí databázi a tabulku pro uložení teplotních dat, která jsou do něj odesílána.

1. V nástroji příkazového řádku na zařízení IoT Edge se připojte k databázi.

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

   ![Zobrazit obsah místní databáze](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul Azure Functions obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md).

Pokud chcete vyzkoušet jinou metodu úložiště na hraničních zařízeních, přečtěte si o tom, jak používat Azure Blob Storage na IoT Edge.

> [!div class="nextstepaction"]
> [Ukládání dat na hraničních zařízeních s využitím služby Azure Blob Storage ve službě IoT Edge](how-to-store-data-blob.md)
