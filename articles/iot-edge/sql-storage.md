---
title: Modul Azure IoT Edge SQL | Microsoft Docs
description: Ukládání dat na hranici s Microsoft SQL modulů s Azure Functions k formátování data.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c755d171b34d59d2746a965ab3511a0df00c98db
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Ukládání dat na hranici s databází serveru SQL Server

Azure IoT hraniční zařízení použijte k ukládání dat, která se vygeneruje na hranici. Zařízení s přerušované připojení k Internetu můžete udržovat své vlastní databáze a sestava změny zpět do cloudu jenom v případě, že připojení. Zařízení, která byla naprogramovaný tak odesílání pouze důležitá data do cloudu můžete uložit zbytek data pro regulární hromadného ukládání. Jakmile se v cloudu, strukturovaná data je možné sdílet s jinými službami Azure, například k sestavení modelu strojového učení. 

Tento článek obsahuje pokyny pro nasazení v zařízení IoT Edge databázi systému SQL Server. Azure Functions, spuštěné na zařízení IoT Edge struktury příchozích dat a odešle ji do databáze. Kroky v tomto článku můžete také provést na jiné databáze, které fungují v kontejnerech, jako jsou databáze MySQL nebo PostgreSQL. 

## <a name="prerequisites"></a>Požadavky 

Než začnete podle pokynů v tomto článku, měli byste pokračovat následující kurzy:
* Nasazení v simulovaném zařízení v Azure IoT Edge [Windows](tutorial-simulate-device-windows.md) nebo [Linux](tutorial-simulate-device-linux.md)
* [Nasazení funkce Azure jako modul IoT Edge](tutorial-deploy-function.md)

V následujících článcích nejsou pro úspěšné dokončení tohoto kurzu vyžadovány, ale může poskytnout užitečné kontextu:
* [Spusťte kontejner bitovou kopii SQL serveru 2017 s Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Pomocí kódu v jazyce Visual Studio pro vývoj a nasazení Azure Functions okraj Azure IoT](how-to-vscode-develop-azure-function.md)

Po dokončení požadované kurzy, musí mít všechny požadované součásti připravené na vašem počítači: 
* Aktivní Azure IoT hub.
* IoT hraniční zařízení s alespoň 2 GB paměti RAM a disku 2 GB.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# pro rozšíření Visual Studio Code (používá technologii OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [Základní rozhraní .NET 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Skript IoT okraj ovládacího prvku](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Šablona AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Aktivní Centrum IoT se alespoň IoT hraniční zařízení.

Systém Windows a Linux kontejnerů v x64 architektury procesoru fungují pro účely tohoto kurzu. SQL Server nepodporuje procesory ARM.

## <a name="deploy-a-sql-server-container"></a>Nasazení kontejneru systému SQL Server

V této části přidáte k MS SQL database simulovaného zařízení IoT okraj. Použít SQL Server 2017 docker kontejneru bitovou kopii, k dispozici jako [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) kontejneru a jako [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/) kontejneru. 

### <a name="deploy-sql-server-2017"></a>Nasazení systému SQL Server 2017

Ve výchozím nastavení vytvoří kód v této části kontejner s bezplatná edice Developer SQL Server 2017. Pokud chcete místo toho spustit produkční edice, přečtěte si téma [spusťte kontejner Image produkční](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) podrobné informace. 

V kroku 3, přidáte vytvořit možnosti ke kontejneru systému SQL Server, které jsou důležité pro vytvoření proměnné prostředí a úložiště trvalá. Nakonfigurované [proměnné prostředí](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) přijmout licenční smlouvu s koncovým uživatelem a zadejte heslo. [Trvalá úložiště](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) je konfigurován pomocí [připojí](https://docs.docker.com/storage/volumes/). Připojení zařízení vytvořit kontejner 2017 serveru SQL s *sqlvolume* kontejneru svazků připojených tak, aby vaše data zachována i v případě, že je odstranit kontejner. 

1. Otevřete `deployment.json` soubor ve Visual Studio Code. 
2. Nahraďte **moduly** v souboru s následujícím kódem: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "<docker registry address>/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
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
        }
   ```

3. Nahraďte `<docker registry address>` s adresou vyplněno dokončené kurzu [nasazení funkce Azure jako modul IoT Edge – náhled](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-function)

   >[!NOTE]
   >Adresa registru kontejneru je stejný jako server přihlášení, který jste zkopírovali z registru. Musí být ve tvaru `<your container registry name>.azurecr.io`

4. V závislosti na operační systém, který používáte aktualizujte nastavení modulu jazyka SQL s následujícím kódem: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

5. Uložte soubor. 
6. Příkaz palety VS kód, vyberte **Edge: vytvoření nasazení pro hraniční zařízení**. 
7. Vyberte ID vašeho IoT hraniční zařízení.
8. Vyberte `deployment.json` aktualizovaný soubor. V okně výstupu uvidíte odpovídající výstupy pro vaše nasazení. 
9. Chcete-li spustit modul runtime vaší hraniční, vyberte **hraniční: spuštění hraniční** v příkazu palety.

>[!TIP]
>Vždy, když vytvoříte kontejner systému SQL Server v provozním prostředí, měli byste [změnit heslo správce systému výchozí](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Vytvoření databáze SQL

Tato část vás provede nastavení databáze SQL pro ukládání dat teploty přijatých ze senzorů, připojený k hraniční IoT zařízení. Pokud používáte simulované zařízení, tato data pocházejí z *tempSensor* kontejneru. 

V nástroji příkazového řádku připojení k vaší databázi: 

* Kontejneru systému Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Linux kontejneru
   ```bash
   docker exec -it sql bash
   ```

Otevřete nástroj SQL příkazu: 

* Kontejneru systému Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux kontejneru
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Vytvoření databáze: 

* Kontejneru systému Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux kontejneru
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Definujte tabulku: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Můžete přizpůsobit souboru systému SQL Server docker automaticky nastavit systému SQL Server k nasazení na více zařízení IoT okraj. Další informace najdete v tématu [Microsoft SQL Server kontejneru ukázkový projekt](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Pochopení připojení SQL.

V dalších kurzech použijeme trasy umožňující kontejnery pro komunikaci při zbývající od sebe navzájem oddělené. Při práci s databázi systému SQL Server, ale blíže relace je nutné. 

Okraj IoT automaticky vytvoří most (Linux) nebo sítě NAT (Windows) při spuštění. Je volána sítě **azure-iot-edge**. Pokud byste někdy potřebovali k ladění toto připojení, můžete vyhledat jeho vlastnosti na příkazovém řádku:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

Okraj IoT může také vyřešit DNS název kontejneru prostřednictvím docker, takže není nutné k odkazování na databázi SQL serveru podle jeho IP adresy. 

Jako příklad zde je připojovací řetězec, který používáme v další části: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Uvidíte, že odkazuje kontejneru připojovací řetězec na jeho název, **sql**. Pokud jste změnili název modulu na něco jiného, aktualizujte tento připojovací řetězec. Přejděte k další části. 

## <a name="update-your-azure-function"></a>Aktualizovat Azure funkce
Odesílání dat do databáze, aktualizujte FilterFunction funkce Azure, která jste udělali v předchozím kurzu. Tento soubor změňte, aby struktury dat přijatých vaší senzorů, a ukládá je v tabulce SQL. 

1. V sadě Visual Studio Code otevřete složku FilterFunction. 
2. Soubor run.csx nahraďte následující kód, který obsahuje připojovací řetězec SQL z předchozí části: 

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
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
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

## <a name="update-your-container-image"></a>Aktualizace bitové kopie kontejneru

Použít změny, které jste udělali, aktualizovat image kontejneru, publikovat a restartujte IoT okraj.

1. V sadě Visual Studio Code, rozbalte **Docker** složky. 
2. Podle platformy, kterou používáte, rozbalte **windows nano** nebo **linux x64** složky. 
3. Klikněte pravým tlačítkem myši **soubor Docker** soubor a vyberte **sestavení IoT Edge modulu Docker image**.
4. Přejděte na **FilterFunction** složky projektu a klikněte na tlačítko **vyberte složku jako EXE_DIR**.
5. Automaticky otevírané okno textového pole v horní části okna VS Code zadejte název bitové kopie. Například, `<your container registry address>/filterfunction:latest`. Pokud nasazujete do místního registru, musí být název `<localhost:5000/filterfunction:latest>`.
6. Příkaz palety VS Code, vyberte **Edge: Push IoT Edge modulu Docker image**. 
7. Automaticky otevírané okno textového pole zadejte stejný název bitové kopie. 
8. Příkaz palety VS Code, vyberte **Edge: restartujte Edge**.

## <a name="view-the-local-data"></a>Zobrazení místní data

Po restartování kontejnerů, dat přijatých ze senzory teploty uložený v místní databázi SQL serveru 2017 na vašem zařízení IoT okraj. 

V nástroji příkazového řádku připojení k vaší databázi: 

* Kontejneru systému Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Linux kontejneru
   ```bash
   docker exec -it sql bash
   ```

Otevřete nástroj SQL příkazu: 

* Kontejneru systému Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux kontejneru
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Zobrazení dat: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [nakonfigurovat SQL Server 2017 kontejneru Image na Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Přejděte [úložiště GitHub mssql docker](https://github.com/Microsoft/mssql-docker) pro prostředky, názory a známé problémy.
