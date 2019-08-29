---
title: Vytvořit triggery plánu v Azure Data Factory | Microsoft Docs
description: Naučte se, jak vytvořit Trigger v Azure Data Factory, který v plánu spouští kanál.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: f693b04cb2a7166436497239dc7a874bdc5cbf46
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141627"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Vytvoření triggeru, který spustí kanál podle plánu
Tento článek poskytuje informace o aktivační události plánovače a postupu pro vytvoření, spuštění a monitorování aktivační události plánovače. Další typy triggerů najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md).

Při vytváření aktivační události plánovače zadáte plán (datum zahájení, opakování, koncové datum atd.) pro aktivační událost a přidružíte k kanálu. Mezi kanály a aktivačními událostmi existuje vztah n-m. Víc aktivačních událostí může aktivovat jeden kanál. Jedna aktivační událost může aktivovat více kanálů.

V následujících částech jsou uvedeny kroky k vytvoření triggeru plánu různými způsoby. 

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory
Můžete vytvořit **aktivační událost plánovače** a naplánovat pravidelné spouštění kanálu (každou hodinu, každý den atd.). 

> [!NOTE]
> Úplný návod k vytvoření kanálu a triggeru plánovače, přidružení triggeru k kanálu a spuštění a monitorování kanálu najdete v tématu [rychlý Start: vytvoření datové továrny pomocí data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md).

1. Přepněte na kartu **Upravit**. 

    ![Přepnutí na kartu Upravit](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. V nabídce klikněte na **Aktivační událost** a pak klikněte na **Nová / upravit**. 

    ![Nabídka Nová aktivační událost](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Na stránce **Přidat aktivační události** klikněte na **Zvolit aktivační událost...** a pak na **Nová**. 

    ![Přidat aktivační události – nová aktivační událost](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Na stránce **Nová aktivační událost** proveďte následující kroky: 

    1. Potvrďte, že je pro **typ**vybraný **plán** . 
    2. Zadejte datum a čas spuštění triggeru pro **počáteční datum (UTC)** . Ve výchozím nastavení je nastaveno na aktuální datum a čas. 
    3. Zadejte **opakování** pro aktivační událost. V rozevíracím seznamu vyberte jednu z hodnot (každou minutu, každou hodinu, denně, týdně a měsíčně). Do textového pole zadejte násobitel. Například pokud chcete, aby se aktivační událost spouštěla jednou za 15 minut, vyberte **každou minutu**a do textového pole zadejte **15** . 
    4. Pokud pro aktivační událost nechcete pro Trigger zadat koncovou hodnotu DateTime, vyberte možnost **bez ukončení**. Chcete-li zadat datum a čas ukončení, vyberte **Datum**a zadejte koncovou hodnotu DateTime a klikněte na tlačítko **použít**. Každé spuštění kanálu je zpoplatněno. Pokud testujete, možná budete chtít zajistit, aby se kanál aktivoval jenom několikrát. Zajistěte však, aby měl kanál mezi časem publikování a koncovým časem dostatek času na spuštění. Aktivační událost nabývá účinnosti po publikování řešení do služby Data Factory, a ne při uložení aktivační události v uživatelském rozhraní.

        ![Nastavení aktivační události](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. V okně **Nová aktivační událost** zaškrtněte možnost **aktivováno** a klikněte na tlačítko **Další**. Pomocí tohoto zaškrtávacího políčka můžete aktivovat Trigger později. 

    ![Nastavení aktivační události – tlačítko Další](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Na stránce **Nová aktivační událost** si přečtěte zprávu upozornění a klikněte na **Dokončit**.

    ![Nastavení aktivační události – tlačítko Dokončit](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kliknutím na **Publikovat** publikujte změny do služby Data Factory. Dokud nepublikujete změny Data Factory, Trigger nespustí spuštění kanálu. 

    ![Tlačítko Publikovat](./media/how-to-create-schedule-trigger/publish-2.png)
8. Vlevo přepněte na kartu **Monitorování**. Kliknutím na **Aktualizovat** seznam aktualizujte. Zobrazí se spuštění kanálu aktivované naplánovanou aktivační událostí. Všimněte si hodnot ve sloupci **Aktivoval(a)** . Pokud použijete možnost **aktivovat nyní** , zobrazí se v seznamu spuštění ruční aktivace. 

    ![Monitorování aktivovaných spuštění](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kliknutím na šipku dolů vedle **Spuštění kanálu** přepněte na zobrazení **Spuštění aktivační události**. 

    ![Monitorování spuštění aktivační události](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V této části se dozvíte, jak pomocí Azure PowerShell vytvořit, spustit a monitorovat aktivační událost plánovače. Pokud si chcete prohlédnout tuto ukázkovou práci, Projděte si [nejdřív rychlé zprovoznění: Vytvořte datovou továrnu pomocí Azure PowerShell](quickstart-create-data-factory-powershell.md). Potom do metody Main přidejte následující kód, který vytvoří a spustí aktivační událost Schedule, která se spouští každých 15 minut. Aktivační událost je přidružená k kanálu s názvem **Adfv2QuickStartPipeline** , který vytvoříte jako součást rychlého startu.

1. Ve složce C:\ADFv2QuickStartPSH\ vytvořte soubor JSON s názvem **MyTrigger. JSON** s následujícím obsahem:

    > [!IMPORTANT]
    > Před uložením souboru JSON nastavte hodnotu elementu **StartTime** na aktuální čas UTC. Nastavte hodnotu elementu **čas_ukončení** na jednu hodinu po aktuálním čase UTC.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    Ve fragmentu kódu JSON:
    - Prvek **Type** triggeru je nastaven na "ScheduleTrigger".
    - Prvek **frekvence** je nastaven na "Minute" a element **interval** je nastaven na hodnotu 15. Proto aktivační událost spouští kanál každých 15 minut mezi začátkem a koncovým časem.
    - Element **čas_ukončení** je jedna hodina po hodnotě elementu **StartTime** . Proto aktivační událost spouští kanál 15 minut, 30 minut a 45 minut po počátečním čase. Nezapomeňte aktualizovat počáteční čas na aktuální čas UTC a koncový čas do 1 hodiny po počátečním čase. 
    - Aktivační událost je přidružená k **Adfv2QuickStartPipeline** kanálu. K přidružení více kanálů k triggeru přidejte další oddíly **pipelineReference** .
    - Kanál v rychlém startu používá dvě hodnoty **parametrů** : **inputPath** a **outputPath**. Proto předáte hodnoty pro tyto parametry z triggeru.

2. Vytvořte Trigger pomocí rutiny **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Pomocí rutiny **Get-AzDataFactoryV2Trigger** ověřte, že se stav triggeru **zastavil** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Spusťte Trigger pomocí rutiny **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Pomocí rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je stav triggeru spuštěný:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Aktivační událost se spouští v Azure PowerShell pomocí rutiny **Get-AzDataFactoryV2TriggerRun** . Chcete-li získat informace o spuštění triggeru, spusťte následující příkaz pravidelně. Aktualizujte hodnoty **TriggerRunStartedAfter** a **TriggerRunStartedBefore** tak, aby odpovídaly hodnotám v definici triggeru:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Pokud chcete monitorovat spuštění triggerů a spuštění kanálu v Azure Portal, přečtěte si téma [monitorování spuštění kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
V této části se dozvíte, jak pomocí sady .NET SDK vytvořit, spustit a monitorovat Trigger. Pokud si chcete prohlédnout tuto ukázkovou práci, Projděte si [nejdřív rychlé zprovoznění: Vytvoření datové továrny pomocí sady .NET SDK](quickstart-create-data-factory-dot-net.md). Potom do metody Main přidejte následující kód, který vytvoří a spustí aktivační událost Schedule, která se spouští každých 15 minut. Aktivační událost je přidružená k kanálu s názvem **Adfv2QuickStartPipeline** , který vytvoříte jako součást rychlého startu.

Chcete-li vytvořit a spustit aktivační událost plánovače, která se spouští každých 15 minut, přidejte do metody Main následující kód:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Chcete-li monitorovat spuštění triggeru, přidejte následující kód před `Console.WriteLine` poslední příkaz v ukázce:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Pokud chcete monitorovat spuštění triggerů a spuštění kanálu v Azure Portal, přečtěte si téma [monitorování spuštění kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
V této části se dozvíte, jak pomocí sady Python SDK vytvořit, spustit a monitorovat Trigger. Pokud si chcete prohlédnout tuto ukázkovou práci, Projděte si [nejdřív rychlé zprovoznění: Vytvořte datovou továrnu pomocí sady Python SDK](quickstart-create-data-factory-python.md). Pak přidejte následující blok kódu za "monitorovat blok kódu spuštění kanálu" ve skriptu Pythonu. Tento kód vytvoří aktivační událost plánovače, která se spouští každých 15 minut mezi zadaným počátečním a koncovým časem. Aktualizujte proměnnou **start_time** na aktuální čas UTC a proměnnou **end_time** na jednu hodinu po aktuálním čase UTC.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Pokud chcete monitorovat spuštění triggerů a spuštění kanálu v Azure Portal, přečtěte si téma [monitorování spuštění kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
K vytvoření triggeru můžete použít šablonu Azure Resource Manager. Podrobné pokyny najdete v tématu [Vytvoření datové továrny Azure pomocí šablony Správce prostředků](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Předání času spuštění triggeru do kanálu
Azure Data Factory verze 1 podporuje čtení nebo zápis dělených dat pomocí systémových proměnných: **Vlastnosti slicestart**, **SliceEnd**, **WindowStart**a **WindowEnd**. V aktuální verzi Azure Data Factory můžete dosáhnout tohoto chování pomocí parametru kanálu. Čas spuštění a naplánovaný čas pro aktivační událost se nastaví jako hodnota parametru kanálu. V následujícím příkladu je naplánovaný čas triggeru předán jako hodnota parametru **scheduledRunTime** kanálu:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>Schéma JSON
Následující definice JSON ukazuje, jak vytvořit aktivační událost plánovače s plánováním a opakováním:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Vlastnost **parameters** elementu **pipelines** je povinná. Pokud váš kanál nepřijímá žádné parametry, musíte pro vlastnost **parameters** zahrnout prázdnou definici JSON.


### <a name="schema-overview"></a>Přehled schématu
Následující tabulka obsahuje přehled hlavních elementů schématu souvisejících s opakováním a plánováním aktivační události:

| Vlastnost JSON | Popis |
|:--- |:--- |
| **startTime** | Hodnota data a času. V případě jednoduchých plánů se hodnota vlastnosti **startTime** vztahuje pouze na první výskyt. U složitějších plánů aktivační událost nezačíná dřív než v čas určený hodnotou **startTime**. |
| **endTime** | Koncové datum a čas pro aktivační událost. Aktivační událost se nebude spouštět po zadaném koncovém datu a času. Hodnota této vlastnosti nemůže být v minulosti. Tato vlastnost je nepovinná. |
| **timeZone** | Časové pásmo. V současné době se podporuje pouze časové pásmo UTC. |
| **recurrence** | Objekt recurrence, který určuje pravidla opakování aktivační události. Objekt recurrence podporuje elementy **frequency** (frekvence), **interval** (interval), **endTime** (koncový čas), **count** (počet) a **schedule** (plán). Když je definovaný objekt recurrence, vyžaduje se element **frequency** (frekvence). Další elementy objektu recurrence jsou volitelné. |
| **frequency** | Jednotka frekvence, s jakou se aktivační událost opakuje. Mezi podporované hodnoty patří „minute“ (minuta), „hour“ (hodina), „day“ (den), „week“ (týden) a „month“ (měsíc). |
| **interval** | Kladné celé číslo označující interval pro hodnotu **frequency**, která určuje, jak často se má aktivační událost spouštět. Pokud má například **interval** hodnotu 3 a **frequency** hodnotu „week“ (týden), aktivační událost se opakuje každé 3 týdny. |
| **schedule** | Plán opakování pro aktivační událost. Aktivační události se zadanou hodnotou **frequency** mění své opakování na základě plánu opakování. Vlastnost **schedule** obsahuje úpravy opakování na základě minut, hodin, dní v týdnu, dní v měsíci a čísla týdne.


### <a name="schema-defaults-limits-and-examples"></a>Výchozí hodnoty, omezení a příklady schématu

| Vlastnost JSON | type | Požadováno | Výchozí hodnota | Platné hodnoty | Příklad |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Řetězec | Ano | Žádné | Data a časy podle normy ISO 8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Object | Ano | Žádné | Objekt opakování | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | Ne | 1 | 1 až 1 000 | `"interval":10` |
| **endTime** | Řetězec | Ano | Žádné | Hodnota data a času představující čas v budoucnosti. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Object | Ne | Žádné | Objekt plánu | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Vlastnost startTime
Následující tabulka ukazuje, jakým způsobem vlastnost **startTime** ovládá spouštění aktivační události:

| Hodnota startTime | Opakování bez plánu | Opakování s plánem |
|:--- |:--- |:--- |
| Čas začátku v minulosti | Vypočítá první čas spuštění v budoucnosti následující po čase začátku a v tomto čase se spustí.<br/><br/>Zahájí další spuštění na základě výpočtu z posledního času spuštění.<br/><br/>Prohlédněte si příklad pod touto tabulkou. | Aktivační událost se spustí _až po_ zadaném čase začátku. První výskyt vychází z plánu vypočítaného z času začátku.<br/><br/>Zahájí další spuštění na základě plánu opakování. |
| Čas začátku v budoucnosti nebo přítomnosti | Spustí se jednou v zadaný čas začátku.<br/><br/>Zahájí další spuštění na základě výpočtu z posledního času spuštění. | Aktivační událost se spustí _až po_ zadaném čase začátku. První výskyt vychází z plánu vypočítaného z času začátku.<br/><br/>Zahájí další spuštění na základě plánu opakování. |

Podívejme se na příklad toho, co se stane, když je čas začátku v minulosti a je nastaveno opakování, ale žádný plán. Předpokládejme, že aktuální čas je `2017-04-08 13:00`, čas začátku je `2017-04-07 14:00` a opakování je nastaveno na každé dva dny. (Hodnota **recurrence** je definovaná nastavením vlastnosti **frequency** na hodnotu „day“ (den) vlastnosti **interval** na hodnotu 2.) Všimněte si, že hodnota **startTime** je v minulosti a předchází aktuálnímu času.

Za těchto podmínek dojde k prvnímu spuštění v `2017-04-09 at 14:00`. Modul plánovače vypočítá výskyty spuštění na základě času začátku. Všechny instance v minulosti se zahodí. Modul použije další instanci, která nastane v budoucnosti. V tomto scénáři je čas začátku `2017-04-07 at 2:00pm`, takže další instance nastane o dva dny později, tedy v čase `2017-04-09 at 2:00pm`.

První čas spuštění je stejný, i když má vlastnost **startTime** hodnotu `2017-04-05 14:00` nebo `2017-04-01 14:00`. Po prvním spuštění se další spuštění vypočítají na základě plánu. Proto další spuštění proběhnou v čase `2017-04-11 at 2:00pm`, pak v `2017-04-13 at 2:00pm`, pak v `2017-04-15 at 2:00pm` atd.

A nakonec, pokud v plánu aktivační události nejsou nastavené hodiny nebo minuty, jako výchozí hodnoty se použijí hodiny nebo minuty prvního spuštění.

### <a name="schedule-property"></a>Vlastnost schedule
Na jedné straně může použití plánu omezit počet spuštění aktivační události. Pokud má například aktivační událost s měsíční frekvencí naplánované spouštění jenom v 31. den, tato aktivační událost se spustí jenom v měsících, které mají 31 dní.

Na druhou stranu může plán způsobit také zvýšení počtu spuštění aktivační události. Například aktivační událost s měsíční frekvencí, která má naplánované spouštění na 1. a 2. den v měsíci se nespustí jednou za měsíc, ale v 1. i 2. den v měsíci.

Pokud je zadaných více elementů **schedule**, pořadí jejich vyhodnocování postupuje od největšího k nejmenšímu nastavení plánu. Vyhodnocování začne číslem týdne a pak pokračuje dnem v měsíci, dnem v týdnu, hodinou a nakonec minutou.

Následující tabulka obsahuje podrobný popis elementů **schedule**:


| Element JSON | Popis | Platné hodnoty |
|:--- |:--- |:--- |
| **minutes** | Minuty v hodině, ve kterých se aktivační událost spouští. | <ul><li>Integer</li><li>Pole celých čísel</li></ul>
| **hours** | Hodiny dne, ve kterých se aktivační událost spouští. | <ul><li>Integer</li><li>Pole celých čísel</li></ul> |
| **weekDays** | Dny v týdnu, ve kterých se aktivační událost spouští. Tuto hodnotu je možné zadat jenom při týdenní frekvenci. | <ul><li>Monday (pondělí), Tuesday (úterý), Wednesday (středa), Thursday (čtvrtek), Friday (pátek), Saturday (sobota), Sunday (neděle)</li><li>Pole hodnot dní (maximální velikost pole je 7)</li><li>V hodnotách dní se nerozlišují malá a velká písmena</li></ul> |
| **monthlyOccurrences** | Dny v měsíci, ve kterých se aktivační událost spouští. Tuto hodnotu je možné zadat jenom při měsíční frekvenci. | <ul><li>Pole objektů **monthlyOccurrence** : `{ "day": day,  "occurrence": occurrence }`.</li><li>Atribut **day** představuje den v týdnu, ve kterém se aktivační událost spouští. Například vlastnost **monthlyOccurrences** s atributem **day** s hodnotou `{Sunday}` znamená každou neděli v měsíci. Atribut **day** je povinný.</li><li>Atribut **occurrence** představuje výskyt zadaného dne (**day**) v měsíci. Například vlastnost **monthlyOccurrences** s atributy **day** a **occurrence** s hodnotami `{Sunday, -1}` znamená poslední neděli v měsíci. Atribut **occurrence** je volitelný.</li></ul> |
| **monthDays** | Dan v měsíci, ve kterém se aktivační událost spouští. Tuto hodnotu je možné zadat jenom při měsíční frekvenci. | <ul><li>Libovolná hodnota < = -1 a > =-31</li><li>Libovolná hodnota >= 1 a <= 31</li><li>Pole hodnot</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Příklady plánů opakování aktivačních událostí
Tato část obsahuje příklady plánů opakování se zaměřením na objekt **schedule** a jeho elementy.

V příkladech se předpokládá, že vlastnost **interval** má hodnotu 1 a vlastnost **frequency** má správnou hodnotu s ohledem na definici plánu. Například není možné, aby vlastnost **frequency** měla hodnotu „day“ a zároveň objekt **schedule** obsahoval úpravu atributu „monthDays“. Tato a podobná omezení jsou popsaná v tabulce v předchozí části.

| Příklad | Popis |
|:--- |:--- |
| `{"hours":[5]}` | Spuštění každý den v 5:00. |
| `{"minutes":[15], "hours":[5]}` | Spuštění každý den v 5:15. |
| `{"minutes":[15], "hours":[5,17]}` | Spuštění každý den v 5:15 a 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` | Spuštění každý den v 5:15, 5:45 a 17:15 a 17:45. |
| `{"minutes":[0,15,30,45]}` | Spuštění každých 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Spuštění každou hodinu. Tato aktivační událost se spouští každou hodinu. Minuty se řídí hodnotou **startTime**, pokud je zadaná. Pokud hodnotu nezadáte, minuty se řídí časem vytvoření. Například pokud je čas začátku nebo čas vytvoření (podle situace) 0:25, aktivační událost se spustí v 0:25, 1:25, 2:25 atd. až do 23:25.<br/><br/>Tento plán je stejný jako aktivační událost s vlastností **frequency** s hodnotou „hour“ (hodina), vlastností **interval** s hodnotou 1 a žádným objektem **schedule**.  Tento plán se dá s jinými hodnotami **frequency** a **interval** použít k vytvoření jiných aktivačních událostí. Když má například **frequency** hodnotu „month“ (měsíc), plán se spustí pouze jednou za měsíc, a ne každý den, jako když má **frequency** hodnotu „day“ (den). |
| `{"minutes":[0]}` | Spuštění v každou celou hodinu. Tato aktivační událost se spouští každou celou hodinu počínaje 0:00, 1:00, 2:00 atd.<br/><br/>Tento plán je stejný jako aktivační událost s vlastností **frequency** s hodnotou „hour“ (hodina) a vlastností **startTime** s hodnotou nula minut nebo žádným objektem **schedule**, ale s vlastností **frequency** s hodnotou „day“ (den). Pokud má **frequency** hodnotu „week“ (týden) nebo „month“ (měsíc), plán se spustí pouze v jeden den v týdnu nebo jeden den v měsíci. |
| `{"minutes":[15]}` | Spuštění 15 minut po každé celé hodině. Tato aktivační událost se spouští 15 minut po každé celé hodině počínaje 0:15, 1:15, 2:15 atd. až do 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Spuštění každý týden v sobotu v 17:00. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Spuštění každý týden v pondělí, středu a pátek v 17:00. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Spuštění každý týden v pondělí, středu a pátek v 17:15 a 17:45. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Spuštění ve všední dny každých 15 minut. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Spuštění ve všední dny každých 15 minut mezi 9:00 a 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Spuštění každé úterý a čtvrtek v zadaný čas začátku. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Spuštění 28. dne každého měsíce v 6:00 (za předpokladu, že **frequency** má hodnotu „month“ (měsíc)). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Spuštění poslední den v měsíci v 6:00. Pokud chcete spustit aktivační událost poslední den v měsíci, použijte místo hodnot 28, 29, 30 nebo 31 hodnotu -1. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Spuštění v každý první a poslední den v měsíci v 6:00. |
| `{monthDays":[1,14]}` | Spuštění v každý první a 14. den v měsíci v zadaný čas začátku. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Spuštění v každý první pátek v měsíci v 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Spuštění v každý první pátek v měsíci v zadaný čas začátku. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Spuštění v každý třetí pátek od konce měsíce v zadaný čas začátku. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Spuštění v každý první a poslední pátek v měsíci v 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Spuštění v každý první a poslední pátek v měsíci v zadaný čas začátku. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Spuštění v každý pátý pátek v měsíci v zadaný čas začátku. Pokud určitý měsíc nemá pátý pátek, kanál se nespustí, protože je jeho spouštění naplánováno jenom na páté pátky. Pokud chcete, aby se aktivační událost spouštěla v poslední pátek v měsíci, můžete jako **occurrence** místo hodnoty 5 zadat hodnotu -1. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Spuštění v poslední pátek v měsíci každých 15 minut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Spuštění každou třetí středu v měsíci v 5:15, 5:45, 17:15 a 17:45. |


## <a name="next-steps"></a>Další kroky
Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#triggers).
