---
title: Ukládání dat pomocí modulu SQL pro Azure IoT Edge | Microsoft Docs
description: Zjistěte, jak místně ukládat data na zařízení IoT Edge pomocí modulu SQL Serveru.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5c3b8d350b69996e2bbff4958dd0a3600c1b7518
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566077"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Kurz: Ukládání dat na hraničních zařízeních s využitím databází SQL Serveru

Pomocí Azure IoT Edge a SQL Serveru můžete ukládat a dotazovat data na hraničních zařízeních. Azure IoT Edge obsahuje integrované základní funkce úložiště, které ukládají zprávy do mezipaměti, když zařízení přejde do offline režimu, a po obnovení připojení je pak předávají dál. Můžete však chtít pokročilejší možnosti úložiště, jako je možnost dotazovat data místně. Díky začlenění místních databází můžou vaše zařízení IoT Edge provádět složitější výpočty, aniž by musela udržovat připojení ke službě IoT Hub. Terénní technik například může na místním počítači vizualizovat data snímačů za posledních několik dní, přestože se data za účelem pomoci s vylepšováním modelu strojového učení nahrávají do cloudu pouze jednou za měsíc.

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
* Rozšíření jazyka [C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pro Visual Studio Code. 
* Rozšíření [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

    ![vytvoření registru kontejneru](./media/tutorial-deploy-function/create-container-registry.png)

2. Zadejte název registru a zvolte předplatné.
3. Jako skupinu prostředků se doporučuje použít název skupiny prostředků, která obsahuje službu IoT Hub. Když umístíte všechny prostředky do stejné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků sloužící k testování se odstraní všechny testovací prostředky, které tato skupina obsahuje. 
4. Nastavte skladovou položku na **Basic** a přepněte možnost **Uživatel s rolí správce** na **Povolit**. 
5. Klikněte na možnost **Vytvořit**.
6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Pokud chcete odeslat data do databáze, potřebujete modul, který dokáže data správně strukturovat a následně je uložit do tabulky. 

Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí Visual Studio Code a rozšíření Azure IoT Edge.

1. Otevřete Visual Studio Code.
2. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code.
3. Výběrem **View** (Zobrazit)  > **Command palette** (Paleta příkazů) otevřete paletu příkazů VS Code.
4. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 
   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **Azure Functions – C#**. 
   4. Zadejte název modulu **sqlFunction**. 
   5. Jako úložiště imagí pro první modul určete službu Azure Container Registry, kterou jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Konečný řetězec vypadá takto: **\<název_registru\>.azurecr.io/sqlFunction**.

4. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Obsahuje složku **modules**, složku **.vscode** a soubor šablony manifestu nasazení. Otevřete **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Obsah souboru nahraďte následujícím kódem:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
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
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
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
   ```

6. Na řádku 24 nahraďte řetězec **\<sql connection string\>** následujícím řetězcem. Vlastnost **Data Source** (Zdroj dat) odkazuje na název kontejneru SQL Serveru, který vytvoříte v další části a pojmenujete **SQL**. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Uložte soubor **run.csx**. 

## <a name="add-a-sql-server-container"></a>Přidání kontejneru SQL Serveru

[Manifest nasazení](module-composition.md) deklaruje, které moduly nainstaluje modul runtime IoT Edge na vaše zařízení IoT Edge. V předchozí části jste přidali kód pro vytvoření přizpůsobeného modulu Functions, ale modul SQL Serveru je již vytvořený. Stačí pouze sdělit modulu runtime IoT Edge, aby ho zahrnul, a pak ho nakonfigurovat na zařízení. 

1. V průzkumníku Visual Studio Code otevřete soubor **deployment.template.json**. 
2. Vyhledejte část **moduleContent.$edgeAgent.properties.desired.modules**. Měla by obsahovat dva moduly: modul**tempSensor**, který generuje simulovaná data, a váš modul **sqlFunction**.
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
           "createOptions": ""
       }
   }
   ```

   Tady je příklad, pokud by vznikly nejasnosti ohledně přidání prvku JSON. ![Přidání kontejneru SQL Serveru](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. V závislosti na typu kontejnerů Docker vašeho zařízení IoT Edge aktualizujte parametry **sql.settings** tímto kódem:

   * Kontejnery Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linuxové kontejnery:

      ```json
      "image": "mcr.microsoft.com/mssql/server:latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Po vytvoření kontejneru SQL Serveru v produkčním prostředí byste vždy měli [změnit výchozí heslo správce systému](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Uložte soubor **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozích částech jste vytvořili řešení s jedním modulem a pak jste přidali další modul do šablony manifestu nasazení. Teď je potřeba sestavit řešení, vytvořit pro moduly image kontejnerů a odeslat tyto image do registru kontejneru. 

1. V souboru .env zadejte pro modul runtime IoT Edge své přihlašovací údaje k registru, aby získal přístup k imagím vašich modulů. Najděte sekce **CONTAINER_REGISTRY_USERNAME** a **CONTAINER_REGISTRY_PASSWORD** a vložte své přihlašovací údaje za symbol rovnítka: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourContainerReg=<username>
   CONTAINER_REGISTRY_PASSWORD_yourContainerReg=<password>
   ```

2. Uložte soubor .env.
3. Ve Visual Studio Code se přihlaste ke svému registru kontejneru, abyste do něj mohli odeslat své image. Použijte stejné přihlašovací údaje, které jste přidali do souboru .env. V integrovaném terminálu zadejte následující příkaz:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Zobrazí se výzva k zadání hesla. Vložte na příkazový řádek své heslo a stiskněte **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

4. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

## <a name="deploy-the-solution-to-a-device"></a>Nasazení řešení do zařízení

Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge. 

1. Na paletě příkazů VS Code vyberte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).
2. Podle pokynů se přihlaste ke svému účtu Azure. 
3. Na paletě příkazů vyberte své předplatné Azure a pak vaši službu IoT Hub. 
4. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 
5. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 
6. V Průzkumníku souborů přejděte do složky **config** uvnitř vašeho řešení a zvolte soubor **deployment.json**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. 

Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. Může to trvat několik minut.

   ```PowerShell
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Když pro své zařízení použijete manifest nasazení, získáte tři spuštěné moduly. Modul tempSensor generuje simulovaná data prostředí. Modul sqlFunction přebírá data a formátuje je pro databázi. 

Tato část vás provede nastavením databáze SQL pro ukládání údajů o teplotě. 

1. Opět na příkazovém řádku se připojte ke své databázi. 
   * Kontejner Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Kontejner Linuxu: 

      ```bash
      docker exec -it sql bash
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