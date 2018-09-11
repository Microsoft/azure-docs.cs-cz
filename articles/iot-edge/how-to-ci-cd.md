---
title: Azure IoT Edge průběžnou integraci a průběžné nasazování | Dokumentace Microsoftu
description: Přehled průběžné integrace a průběžného nasazování pro Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302567"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

Tento článek popisuje, jak můžete použít tak průběžnou integraci a průběžné nasazování funkce služeb Azure DevOps a Microsoft Team Foundation Server (TFS) pro vytváření, testování a rychle a efektivně nasadit aplikace pro vaše Azure IoT Edge. 

V tomto článku se dozvíte, jak:
* Vytvoření a testy se změnami ukázku jednotku obsahující řešení IoT Edge.
* Nainstalujte rozšíření Azure IoT Edge pro Azure DevOps.
* Nakonfiguruje kontinuální integraci (CI) k sestavení řešení a spustit testy jednotek.
* Konfigurace průběžného nasazování (CD) k nasazení řešení a zobrazování odpovědí.

Bude trvat 30 minut na dokončení kroků v tomto článku.

![CI a CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Vytvoření ukázkové řešení Azure IoT Edge pomocí Visual Studio Code

V této části vytvoříte ukázkové hraničních zařízeních IoT řešení obsahující testy jednotek, které můžete spouštět jako součást procesu sestavení. Než budete postupovat pokyny v této části, proveďte kroky v [vývoj řešení IoT Edge s několika moduly v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Paleta příkazů VS Code, zadejte a spusťte příkaz **Edge: nový IoT Edge řešení**. Vyberte si složku pracovního prostoru, zadejte název řešení (výchozí název je **EdgeSolution**) a vytvořit modulu jazyka C# (**FilterModule**) jako první modul uživatele v tomto řešení. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště imagí vychází z místního registru Dockeru (`localhost:5000/filtermodule`). Je třeba změnit ho do služby Azure Container Registry (`<your container registry address>/filtermodule`) nebo centra Dockeru pro další průběžnou integraci.

    ![Instalační program služby ACR](./media/how-to-ci-cd/acr.png)

2. Okna nástroje VS Code se načte pracovní prostor řešení IoT Edge. Volitelně zadejte a spustit **Edge: modul IoT Edge přidat** přidáte další moduly. Je `modules` složku, `.vscode` složku a soubor manifestu šablony nasazení v kořenové složce. Všechny kódy modulu uživatele bude podsložky ve složce `modules`. `deployment.template.json` Je v šabloně manifestu nasazení. Některé parametry v tomto souboru se získá analýzou z `module.json`, která existuje v všechny složky, které modul.

3. Ukázku řešení IoT Edge je teď připravený. Výchozí C# modul funguje jako zprávy modulu kanálu. V `deployment.template.json`, uvidíte toto řešení obsahuje dva moduly. Zpráva se budou generovat z `tempSensor` modulu a budou směrované přímo prostřednictvím `FilterModule`, pak odešlou do služby IoT hub. Nahraďte celým **Program.cs** soubor s následující obsah. Další informace o tento fragment kódu, mohou odkazovat na [projekt modul IoT Edge C# vytvořit](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
                var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                {
                    Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                        $"exceeds threshold {temperatureThreshold}");
                    var filteredMessage = new Message(messageBytes);
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. .Net Core vytvořit projekt testování částí. V Průzkumníku souborů VS Code, vytvořte novou složku **tests\FilterModuleTest** ve vašem pracovním prostoru. Potom v integrovaném terminálu VS Code (**Ctrl + "**), spusťte následující příkazy vytvořit projekt testů xunit a přidat odkaz na **FilterModule** projektu.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Struktura složek](./media/how-to-ci-cd/add-test-project.png)

5. V **FilterModuleTest** složky, aktualizujte název souboru **UnitTest1.cs** k **FilterModuleTest.cs**. Vyberte a otevřete **FilterModuleTest.cs**, nahraďte celý kód s následující fragment kódu, který obsahuje testy jednotek proti FilterModule projektu.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. V integrovaném terminálu můžete zadat následující příkazy pro spuštění testů jednotek místně. 
    ```cmd
    dotnet test
    ```

    ![Testování částí](./media/how-to-ci-cd/unit-test.png)

7. Uložte tyto projekty a potom vrátit se změnami do úložiště Azure DevOps nebo TFS.
    

> [!NOTE]
> Další informace o použití úložiště Azure najdete v tématu [sdílení kódu pomocí sady Visual Studio a úložiště Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Nakonfiguruje kontinuální integraci
V této části vytvoříte kanál sestavení, která je nakonfigurována na automatické spuštění při vrácení se změnami změny ukázkové řešení IoT Edge a automaticky spustí testy jednotek, které obsahuje.

1. Přihlaste se k vaší organizaci Azure DevOps (**https://**_svůj účet_**. visualstudio.com**) a otevřete projekt, ve kterém můžete zkontrolovat v ukázkové aplikaci.

    ![Vrácení se změnami kódu](./media/how-to-ci-cd/init-project.png)

1. Navštivte [Azure IoT Edge pro Azure DevOps](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) na Azure DevOps Marketplace. Klikněte na tlačítko **získání bezplatného** a postupujte podle pokynů průvodce a nainstalujte toto rozšíření pro vaši organizaci Azure DevOps nebo ke stažení pro TFS.

    ![Instalace rozšíření](./media/how-to-ci-cd/install-extension.png)

1. V Azure DevOps, otevřete **sestavení &amp; vydání** hub a v **sestavení** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro sestavování, vyberte **+ nová** tlačítko. 

    ![Nové sestavení](./media/how-to-ci-cd/add-new-build.png)

1. Pokud se zobrazí výzva, vyberte **Azure DevOps Git** typ zdroje; vyberte projekt, úložiště a větev, ve kterém se nachází váš kód. Zvolte **pokračovat**.

    ![Vyberte git Azure DevOps](./media/how-to-ci-cd/select-vsts-git.png)

1. V **vyberte šablonu** okně zvolte **začněte s prázdným procesem**.

    ![Prázdný](./media/how-to-ci-cd/start-with-empty.png)

1. Klikněte na tlačítko **+** na pravé straně **fáze 1** přidejte úkol do fáze. Poté vyhledejte a vyberte **.Net Core**a klikněte na tlačítko **přidat** tento úkol má přidat do fáze.

    ![DotNet test](./media/how-to-ci-cd/add-dot-net-core.png)

1. Aktualizace **zobrazovaný název** k **příkazu dotnet test**a **příkaz** rozevíracího seznamu vyberte **testování**. Přidat následující cestu k **cesta k projektům**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Nakonfigurujte příkazu dotnet test](./media/how-to-ci-cd/dotnet-test.png)

1. Klikněte na tlačítko **+** na pravé straně **fáze 1** přidejte úkol do fáze. Poté vyhledejte a vyberte **Azure IoT Edge**a klikněte na tlačítko **přidat** tlačítko **dvakrát** přidáte tyto úlohy na fázi.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. V první úloze Azure IoT Edge, aktualizujte **zobrazovaný název** k **modul sestavení a nabízených oznámení**a v **akce** rozevíracího seznamu vyberte **vytváření a nasdílení změn**. V **Module.json souboru** textové pole, přidat následující cestu k němu. Klikněte na tlačítko **typ registru kontejneru**, ujistěte se, že nakonfigurujete a vyberte stejné registr v kódu. Tato úloha bude sestavovat a push všechny moduly v řešení a publikování do registru kontejneru, který jste zadali. Pokud moduly se vloží do různých registrů, můžete mít více **modul sestavení a Push** úlohy.

    ```
    **/module.json
    ```

    ![Modul sestavení a odeslání](./media/how-to-ci-cd/module-build-push.png)

1. V druhé úloze Azure IoT Edge, aktualizujte **zobrazovaný název** k **nasadit do zařízení IoT Edge**a **akce** rozevíracího seznamu vyberte **nasazení do hraničních zařízení IoT zařízení**. Vyberte své předplatné Azure a zadejte název vašeho centra IoT. Můžete zadat ID nasazení IoT Edge a prioritu nasazení. Můžete také nasadit do jedné nebo více zařízení. Pokud provádíte nasazení na více zařízeních, budete muset zadat cílovou podmínku zařízení. Například pokud chcete používat značky zařízení jako podmínka, musíte aktualizovat odpovídající zařízení značky před nasazení. 

    ![Nasazení do hraničních zařízení](./media/how-to-ci-cd/deploy-to-edge.png)

1. Klikněte na tlačítko **procesu** a ujistěte se, že vaše **frontu agenta** je **hostované Linuxové verze Preview**.

    ![Konfigurace](./media/how-to-ci-cd/configure-env.png)

1. Otevřít **aktivační události** kartu a zapnout **kontinuální integrace** aktivační události. Ujistěte se, že větev, která obsahuje kód je součástí.

    ![Trigger](./media/how-to-ci-cd/configure-trigger.png)

1. Uložte nový kanál sestavení a nové sestavení do fronty. Klikněte na tlačítko **Uložit & frontu** tlačítko.

1. Na panelu zpráv, který se zobrazí, zvolte odkaz na sestavení. Můžete také přejít k vytvoření kanálu, pokud chcete zobrazit nejnovější sestavení ve frontě úloh.

    ![Sestavení](./media/how-to-ci-cd/build-def.png)

1. Po dokončení sestavení se zobrazí souhrnné informace pro každý úkol a výsledky v souboru protokolu za provozu. 
    
    ![Dokončit](./media/how-to-ci-cd/complete.png)

1. Můžete přejít zpět na VS Code a zkontrolovat v Průzkumníku zařízení centra IoT. Hraniční zařízení s modulem by měla začít spouštět (ujistěte se, že jste přidali přihlašovacích údajů registru do modulu runtime Edge).

    ![Edge, který běží](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Průběžné nasazování do zařízení IoT Edge

Pokud chcete povolit průběžné nasazování, v podstatě potřebujete nastavení položky konfigurace úlohy s použitím správné zařízení IoT Edge, povolení **aktivační události** větví ve vašem projektu. V klasické postupů DevOps projekt obsahuje dvě hlavní větve. Hlavní větve by měla být stabilní verzi kódu a vývoj větev obsahuje nejnovější změny kódu. Každý vývojář v týmu musí vytvořit fork větev vývoje tak, aby jeho nebo vlastní větev funkce při spuštění aktualizace kódu, což znamená, že všechna potvrzení změn dojde na funkci větve vývoje jiné větvi. A každý vložené potvrzení změny by měl být testován prostřednictvím systému CI. Po plně testovaný kód místně, mají vývoj větví prostřednictvím žádosti o přijetí změn sloučit větev funkce. Při testování kódu ve větvi pro vývojáře prostřednictvím systému CI, můžete sloučit do hlavní větví prostřednictvím žádosti o přijetí změn.

Proto při nasazování do zařízení IoT Edge, existují tři hlavní prostředí.
- Na větev funkce můžete použít simulované zařízení IoT Edge na vývojovém počítači nebo nasadit do fyzického zařízení IoT Edge.
- Na vývoj větve, měli byste nasadit do fyzického zařízení IoT Edge.
- V hlavní větvi cílová zařízení IoT Edge by měl být produkční zařízení.

## <a name="next-steps"></a>Další postup

* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
