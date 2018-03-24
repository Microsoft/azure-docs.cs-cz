---
title: Monitorování a Správa kanálů pomocí portálu Azure a prostředí PowerShell | Microsoft Docs
description: Další informace o použití portálu Azure a prostředí Azure PowerShell monitorovat a spravovat Azure data Factory a kanály, které jste vytvořili.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f3fb7c0be6f69f15b5b761f0c36d983f008282e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorování a Správa kanálů služby Azure Data Factory pomocí portálu Azure a prostředí PowerShell
> [!div class="op_single_selector"]
> * [Použití Azure portal nebo Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [sledování a Správa kanálů služby Data Factory v verze 2](../monitor-visually.md).

> [!IMPORTANT]
> Aplikace monitorování a správu poskytuje lepší podporu pro monitorování a správy datových kanálů a řešení potíží s problémy. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md). 


Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí portálu Azure a prostředí PowerShell. Tento článek také poskytuje informace o tom, jak vytvářet výstrahy a upozorňování o selhání.

## <a name="understand-pipelines-and-activity-states"></a>Pochopit kanály a aktivity stavy
Pomocí portálu Azure, můžete:

* Objekt pro vytváření dat zobrazte jako diagram.
* Zobrazit aktivity v kanálu.
* Zobrazte vstupní a výstupní datové sady.

Tato část také popisuje, jak se řez datovou sadu přechází z jednoho stavu do jiného stavu.   

### <a name="navigate-to-your-data-factory"></a>Přejděte do data factory
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **datové továrny** v nabídce na levé straně. Pokud ho nevidíte, klikněte na tlačítko **další služby >**a potom klikněte na **datové továrny** pod **INTELLIGENCE + analýzy** kategorie.

   ![Procházet všechny > datové továrny](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na **datové továrny** okně vyberte služby data factory, která vás zajímá.

    ![Výběr datové továrny](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Měli byste vidět domovské stránce služby data Factory.

   ![Okno Datová továrna](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Zobrazení diagramu svojí datové továrny
**Diagram** zobrazení objektu pro vytváření dat poskytuje skla ke sledování a správě objektu pro vytváření dat a její prostředky. Chcete-li zobrazit **Diagram** zobrazení objektu pro vytváření dat, klikněte na tlačítko **Diagram** na domovské stránce objektu pro vytváření dat.

![Zobrazení diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Můžete přiblížení, oddálení, zvětšení přizpůsobit, zvětšení na 100 %, uzamčení rozložení diagramu a automatické umísťování kanálů a datové sady. Můžete také zjistit informace o rodokmenu dat (tedy zobrazit nadřazené a podřízené položky vybraných položek).

### <a name="activities-inside-a-pipeline"></a>Aktivity v kanálu
1. Klikněte pravým tlačítkem na kanál a pak klikněte na **otevřít kanál** zobrazíte všechny aktivity v kanálu spolu s vstupní a výstupní datové sady pro aktivity. Tato funkce je užitečná, když vaše kanál obsahuje více než jednu aktivitu a chcete se dozvědět provozní rodokmenu jednoho kanálu.

    ![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. V následujícím příkladu najdete v části aktivity kopírování v kanálu s vstup a výstup. 

    ![Aktivity v kanálu](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Můžete přejít zpět na domovské stránce objektu pro vytváření dat kliknutím **objekt pro vytváření dat** odkaz v zobrazení cesty v levém horním rohu.

    ![Přejděte zpět na objekt pro vytváření dat](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Zobrazit stav každé aktivity v kanálu
Zobrazení stavu libovolných datové sady, které jsou produkované aktivitou, můžete zobrazit aktuální stav aktivity.

Dvojitým kliknutím **OutputBlobTable** v **Diagram**, zobrazí se všechny datové řezy, které vznikají pomocí funkcí spustí jinou aktivitu v kanálu. Uvidíte, aktivitě kopírování byla spuštěna úspěšně za posledních 8 hodin a vytváří výseče **připraven** stavu.  

![Stav kanálu](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Řezy datovou sadu v objektu pro vytváření dat může mít jednu z následujících stavů:

<table>
<tr>
    <th align="left">Stav</th><th align="left">Dílčí stav</th><th align="left">Popis</th>
</tr>
<tr>
    <td rowspan="8">Čekání</td><td>ScheduleTime</td><td>Pro spuštění řezu ještě nenastal čas.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Upstreamové závislosti nejsou připravené.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivit právě zpracovávají jiné řezy.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavená a zpracování řezů nejde spustit, dokud je obnoveno aktivity.</td>
</tr>
<tr>
<td>Retry</td><td>Probíhá pokus o spuštění aktivity je zopakován.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření se ještě nespustilo.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověření čeká na opakovat.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá zpracování.</td><td>Ověřování platnosti</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Řez se zpracovává.</td>
</tr>
<tr>
<td rowspan="4">Selhalo</td><td>TimedOut</td><td>Provedení aktivity trvalo déle, než je povolené aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Řez zrušil akce uživatele.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření selhalo.</td>
</tr>
<tr>
<td>-</td><td>Řez se nepodařilo vygenerovat nebo ověřit.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Řez je připraven ke spotřebování.</td>
</tr>
<tr>
<td>Vynecháno</td><td>Žádné</td><td>Řez se zpracovává.</td>
</tr>
<tr>
<td>Žádné</td><td>-</td><td>Řez měl dříve jiný stav, ale byla obnovena.</td>
</tr>
</table>



Podrobnosti o řez můžete zobrazit kliknutím na položku řez **nedávno aktualizován řezy** okno.

![Řez podrobnosti](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Pokud je řez provedl vícekrát, zobrazí více řádků v **aktivita spuštěna** seznamu. Můžete zobrazit podrobnosti o aktivitě spustíte kliknutím na položku Spustit v **aktivita spuštěna** seznamu. V seznamu jsou uvedeny všechny soubory protokolu, spolu s chybovou zprávu, pokud existuje. Tato funkce je užitečná k zobrazení a ladění protokoly, aniž by museli opustit váš služby data factory.

![Podrobnosti o spuštění aktivit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Není-li řez v **připraven** stavu, můžete zobrazit upstreamové datové řezy, které nejsou připravené a které blokují spuštění aktuálního řezu ve spuštění **Upstreamové datové řezy, které nejsou připraveny** seznamu. Tato funkce je užitečná, když vaše řez v **čekání** stavu a chcete pochopili nadřazeného závislosti, které řez čeká na.

![Upstreamové datové řezy, které nejsou připraveny](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stavu datové sady
Po nasazení služby data factory a kanálů mají platný aktivní období, datová sada řezy přechod z jednoho stavu do jiného. V současné době stav řezu zahrnuje následující diagram stavu:

![Diagram stavu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Tok přechod stavu datovou sadu v objektu pro vytváření dat je následující: Čekání na -> průběh/v – probíhající (ověřování) -> Připraveno nebo se nezdařilo.

Řez se spustí v **čekání** stavu čekání předběžných podmínek, které musí být splněné, než se provede. Pak spustí aktivita provádění a řez přejde do **probíhající** stavu. Provedení aktivity může úspěch nebo neúspěch. Řez je označena jako **připraven** nebo **se nezdařilo**, podle výsledek provedení.

Můžete resetovat řez se vrátíte z **připraven** nebo **se nezdařilo** stavu na **čekání** stavu. Můžete také označit stav řez, který má **přeskočit**, která brání aktivity z provádění a není zpracování řezu.

## <a name="pause-and-resume-pipelines"></a>Pozastavení a obnovení kanálů
Kanály můžete spravovat pomocí prostředí Azure PowerShell. Například můžete pozastavit a obnovit kanály spuštěním rutin prostředí Azure PowerShell. 

> [!NOTE] 
> Zobrazení diagramu nepodporuje pozastavení a obnovení kanály. Pokud chcete použít uživatelské rozhraní, použijte monitorování a správu aplikací. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md) článku. 

Je možné pozastavit nebo pozastavit kanály pomocí **Suspend-AzureRmDataFactoryPipeline** rutiny prostředí PowerShell. Tato rutina je užitečná, když nechcete, aby běžela kanály, dokud nebude problém vyřešen. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po napravení problému s kanálu, můžete obnovit pozastavenou kanálu spuštěním následujícího příkazu Powershellu:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Ladit kanály
Azure Data Factory poskytuje bohaté možnosti pro ladění a řešení potíží kanály pomocí portálu Azure a prostředí Azure PowerShell.

> [! Poznámka:}, že je mnohem snazší potíže chyby pomocí aplikace pro správu a monitorování. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md) článku. 

### <a name="find-errors-in-a-pipeline"></a>Vyhledejte chyby v kanálu
Pokud se nezdaří spustit aktivitu v kanálu, datovou sadu, která je vytvořena v kanálu je v chybovém stavu z důvodu selhání. Můžete ladění a odstraňování chyb v Azure Data Factory pomocí následujících metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Použití portálu Azure k ladění k chybě
1. Na **tabulky** okně klikněte na tlačítko řez problém, který má **stav** nastavena na **se nezdařilo**.

   ![Okno tabulky s řez problém](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na **datový řez** okno, klikněte na tlačítko spuštění aktivity, která se nezdařila.

   ![Datový řez s chybou](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na **podrobnosti o spuštění aktivit** okně si můžete stáhnout soubory, které jsou spojeny s HDInsight zpracování. Klikněte na tlačítko **Stáhnout** pro stav nebo stderr ke stažení souboru protokolu chyb, který obsahuje podrobnosti o této chybě.

   ![Aktivity při spuštění podrobnosti okno s chybou](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Použití prostředí PowerShell k ladění k chybě
1. Spusťte **PowerShell**.
2. Spustit **Get-AzureRmDataFactorySlice** příkazu zobrazte řezy a jejich stav. Měli byste vidět řez se stavem **se nezdařilo**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Příklad:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Nahraďte **StartDateTime** se časem spuštění vaší kanálu. 
3. Teď, spusťte **Get-AzureRmDataFactoryRun** rutinu získat tak podrobné údaje o aktivitě spustit řez.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Příklad:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Hodnota StartDateTime je čas zahájení pro řez chyby nebo problému, který jste si poznamenali v předchozím kroku. Datum a čas by měl být uzavřena do uvozovek.
4. Měli byste vidět výstup s podrobnostmi o chybě, který je podobný následujícímu:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Můžete spustit **uložit AzureRmDataFactoryLog** rutiny se hodnota Id najdete v části z výstupu a stažení souborů protokolu pomocí **- DownloadLogsoption** pro rutinu.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Znovu spustit chyby v kanálu

> [!IMPORTANT]
> Je snazší řešení chyb a znovu spusťte selhání řezy pomocí monitorování a správu aplikací. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Po řešení potíží a ladění chyby v kanálu se může znovu selhání přejdete na řez chyba a kliknutím na **spustit** tlačítka na panelu příkazů.

![Opětovné spuštění neúspěšné řez](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

V případě řezu selhalo ověření z důvodu selhání zásad (například pokud data nejsou k dispozici), můžete opravit chyby a znovu ověřit kliknutím **ověřením** tlačítka na panelu příkazů.

![Opravte chyby a ověření](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Použití Azure Powershell
Selhání může znovu s použitím **Set-AzureRmDataFactorySliceStatus** rutiny. Najdete v článku [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) téma pro syntaxi a další podrobnosti o rutině.

**Příklad:**

Následující příklad nastaví stav všech řezech tabulky 'DAWikiAggregatedData' čekání v Azure data factory 'WikiADF'.

Typ 'aktualizace, je nastaven na 'UpstreamInPipeline", což znamená, že stavy každý řez tabulky a všechny závislé (nadřazený) tabulky jsou nastavená na 'Čekání na'. S možnou hodnotou tohoto parametru je "Individuální".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Vytváření upozornění
Azure protokoly událostí uživatele při prostředků Azure (například objekt pro vytváření dat) je vytvořen, aktualizovat ani odstranit. Výstrahy můžete vytvořit na těchto událostech. Objekt pro vytváření dat můžete použít k zachycení různé metriky a vytvořit oznámení o metrikách. Doporučujeme použít události pro monitorování v reálném čase a použít metriky pro účely záznamu historie událostí.

### <a name="alerts-on-events"></a>Výstrahy na události
Azure události poskytují užitečné přehledy co se děje v vašich prostředků Azure. Pokud používáte Azure Data Factory, události se generují při:

* Objekt pro vytváření dat je vytvořen, aktualizovat ani odstranit.
* Zpracování dat (jako "spuštěno") je spuštěna, nebo byla dokončena.
* Vytvoření clusteru HDInsight na vyžádání nebo odebrat.

Můžete vytvářet výstrahy na těchto událostech uživatele a nakonfigurovat je pro odeslání e-mailová oznámení pro správce a coadministrators předplatného. Kromě toho můžete zadat další e-mailové adresy uživatelů, kteří potřebují pro příjem e-mailová oznámení, pokud jsou splněny podmínky. Tato funkce je užitečná, když chcete dostat upozornění na selhání a nechcete neustále monitorovat váš služby data factory.

> [!NOTE]
> V současné době nepodporuje na portálu zobrazit výstrahy na události. Použití [monitorování a správu aplikace](data-factory-monitor-manage-app.md) zobrazíte všechny výstrahy.


#### <a name="specify-an-alert-definition"></a>Zadejte definici výstrah
Pokud chcete zadat výstrahy definice, vytvořte soubor JSON, který popisuje operace, které chcete být upozorněni na. V následujícím příkladu odešle výstrahy e-mailové oznámení pro operaci RunFinished. Být konkrétní, je odeslána e-mailové oznámení, pokud bylo dokončeno spustit v datové továrně a spustit se nezdařila (stav = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Můžete odebrat **subStatus** z definice JSON, pokud chcete být upozorněni na konkrétní chyby.

Tento příklad nastaví upozornění pro všechny datové továrny v rámci vašeho předplatného. Pokud chcete výstrahu, kterou chcete být nastavenou službu objekt pro vytváření konkrétní data, můžete zadat objekt pro vytváření dat **resourceUri** v **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

Následující tabulka obsahuje seznam dostupné operace a stavy (a dílčí stavy).

| Název operace | Status | Podřízený stav |
| --- | --- | --- |
| RunStarted |Spuštěno |Spouštění |
| RunFinished |Nemohl / bylo úspěšné |FailedResourceAllocation<br/><br/>Úspěch<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/>< zrušena<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |Spuštěno | |
| OnDemandClusterCreateSuccessful |Úspěch | |
| OnDemandClusterDeleted |Úspěch | |

V tématu [vytvořit pravidlo výstrahy](https://msdn.microsoft.com/library/azure/dn510366.aspx) podrobnosti o elementy JSON, které se používají v příkladu.

#### <a name="deploy-the-alert"></a>Nasazení výstrahy
Nasadit výstrahy, použijte rutinu prostředí Azure PowerShell **New-AzureRmResourceGroupDeployment**, jak je znázorněno v následujícím příkladu:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Po nasazení skupiny prostředků se úspěšně dokončil, můžete zobrazit následující zprávy:

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Můžete použít [vytvořit pravidlo výstrahy](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST API pro vytvoření pravidla výstrahy. Datová část JSON je podobné jako v příkladu JSON.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Načtení seznamu nasazení skupiny prostředků Azure.
Pro načtení seznamu nasazení skupiny prostředků nasazené Azure, použijte rutinu **Get-AzureRmResourceGroupDeployment**, jak je znázorněno v následujícím příkladu:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Řešení potíží s událostmi uživatele
1. Zobrazí všechny události, které jsou generovány po kliknutí na tlačítko **metriky a operace** dlaždici.

    ![Dlaždice metriky a operace](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Klikněte **události** dlaždice sledovat události.

    ![Dlaždice události](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Na **události** okno, můžete zobrazit podrobnosti o události, filtrované události a tak dále.

    ![Okno události](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Klikněte na tlačítko **operace** v seznamu operací, která způsobuje chybu.

    ![Vyberte operaci](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Klikněte na tlačítko **chyba** událost zobrazíte podrobnosti o této chybě.

    ![Chyba události](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

V tématu [rutiny Azure přehledy](https://msdn.microsoft.com/library/mt282452.aspx) pro rutiny prostředí PowerShell, které můžete přidat, získat, nebo odeberte výstrahy. Tady je několik příkladů použití **Get-AlertRule** rutiny:

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Spusťte následující příkazy get-help zobrazíte podrobnosti a příklady pro rutinu Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Pokud uvidíte události generování výstrah v okně portálu, ale nebudete dostávat e-mailová oznámení, zkontrolujte, zda je pro příjem e-mailů z externí odesílatelé nastaven e-mailovou adresu, která je zadána. Výstrahy e-mailů může mít zablokovaný nastavení e-mailu.

### <a name="alerts-on-metrics"></a>Výstrahy na metriky
V objektu pro vytváření dat můžete zachytit různé metriky a vytvořit oznámení o metrikách. Můžete sledovat a vytvářet upozornění na následující metriky pro řezy v datové továrně:

* **Spustí se nezdařilo**
* **Úspěšné spuštění**

Tyto metriky jsou užitečné a umožňují získat přehled o celkové úspěšné a neúspěšné spuštění v datové továrně. Metriky jsou vydávány pokaždé, když je spustit řez. Na začátku hodinu, jsou tyto metriky agregovat a instaluje do účtu úložiště. Chcete-li povolit metriky, nastavte účet úložiště.

#### <a name="enable-metrics"></a>Povolit metriky
Pokud chcete povolit metriky, klikněte na následující z **Data Factory** okno:

**Monitorování** > **metrika** > **nastavení pro diagnostiku** > **diagnostiky**

![Odkaz diagnostiky](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Na **diagnostiky** okně klikněte na tlačítko **na**, vyberte účet úložiště a klikněte na **Uložit**.

![Okno diagnostiky](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Může trvat až jednu hodinu metrik, které mají být zobrazeny na **monitorování** okno protože metriky agregace se stane každou hodinu.

### <a name="set-up-an-alert-on-metrics"></a>Nastavit výstrahy na metriky
Klikněte **metriky služby Data Factory** dlaždice:

![Dlaždice metriky objekt pro vytváření dat](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Na **metrika** okně klikněte na tlačítko **+ přidat upozornění** na panelu nástrojů.
![Okno metriky objekt pro vytváření dat > Přidat upozornění](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Na **přidání pravidla výstrahy** proveďte následující kroky a klikněte na tlačítko **OK**.

* Zadejte název pro výstrahu (Příklad: "se nezdařilo výstraha").
* Zadejte popis pro výstrahu (Příklad: "Odeslat e-mail, pokud dojde k selhání").
* Vyberte metriku (vs "Spuštění se nezdařilo". "Úspěšně spuštěno").
* Zadejte podmínku a prahová hodnota.   
* Zadejte časové období.
* Určete, zda se mají odesílat e-mailu vlastníci, přispěvatelé a čtenáři.

![Okno metriky objekt pro vytváření dat > Přidat pravidlo výstrahy](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Po pravidlo výstrahy bylo úspěšně přidáno, zavře okno a uvidíte nové výstrahy na **metrika** okno.

![Okno metriky objekt pro vytváření dat > Přidat nové výstrahy](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Měli byste taky vidět počet výstrah v **výstrah pravidla** dlaždici. Klikněte **výstrah pravidla** dlaždici.

![Data factory metriky okno - pravidla výstrah](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

Na **výstrahy pravidla** okno, zobrazit všechny existující výstrahy. Chcete-li přidat výstrahu, klikněte na tlačítko **přidat upozornění** na panelu nástrojů.

![Okno pravidla výstrah](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Oznámení o výstrahách
Po pravidlo výstrahy odpovídá podmínku, měli byste obdržet e-mail s upozorněním, že se aktivuje výstrahu. Jakmile je problém vyřešen a podmínka upozornění se neshoduje se už, získáte e-mail s upozorněním, že výstraha je vyřešený.

Toto chování se liší od událostí, kde jsou oznámení odesílána v každé selhání, který identifikuje pravidlo výstrahy pro.

### <a name="deploy-alerts-by-using-powershell"></a>Výstrahy nasazení pomocí prostředí PowerShell
Výstrahy metrik, které můžete nasadit stejně, jako je tomu u události.

**Definice upozornění**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Nahraďte *subscriptionId*, *resourceGroupName*, a *dataFactoryName* v ukázce s příslušnými hodnotami.

*metricName* aktuálně podporuje dvě hodnoty:

* FailedRuns
* SuccessfulRuns

**Nasazení výstrahy**

Nasadit výstrahy, použijte rutinu prostředí Azure PowerShell **New-AzureRmResourceGroupDeployment**, jak je znázorněno v následujícím příkladu:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Měli byste vidět následující zprávou po úspěšné nasazení:

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

Můžete také **přidat AlertRule** nasadíte pravidlo výstrahy. Najdete v článku [přidat AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) téma podrobné informace a příklady.  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Přesunout objekt pro vytváření dat do jiné skupině prostředků nebo předplatného
Objekt pro vytváření dat můžete přesunout na jinou skupinu prostředků nebo jiného předplatného pomocí **přesunout** příkazový řádek na domovské stránce objektu pro vytváření dat tlačítko.

![Přesunout objekt pro vytváření dat](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Také můžete přesunout všechny související prostředky (například výstrahy, které jsou přidruženy objektu pro vytváření dat), spolu s služby data factory.

![Dialogové okno prostředků](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
