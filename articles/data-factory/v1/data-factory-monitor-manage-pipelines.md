---
title: Monitorování a Správa kanálů pomocí Azure Portal a PowerShellu
description: Naučte se, jak pomocí Azure Portal a Azure PowerShell monitorovat a spravovat datové továrny Azure a kanály, které jste vytvořili.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 486f12c29c473d46e3aff73abe747f8aa5a2ef8d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380402"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorování a Správa kanálů Azure Data Factory pomocí Azure Portal a PowerShellu
> [!div class="op_single_selector"]
> * [Použití Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Používání aplikace pro monitorování a správu](data-factory-monitor-manage-app.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [monitorování a Správa kanálů Data Factory v nástroji](../monitor-visually.md).

Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí Azure Portal a PowerShellu.

> [!IMPORTANT]
> Aplikace monitoring & Management poskytuje lepší podporu pro monitorování a správu datových kanálů a řešení problémů. Podrobnosti o používání aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory verze 1 nyní používá novou [infrastrukturu upozorňování Azure monitor](../../azure-monitor/platform/alerts-metric.md). Stará infrastruktura upozorňování je zastaralá. V důsledku toho už nebudou existující výstrahy nakonfigurované pro datové továrny verze 1 fungovat. Vaše stávající výstrahy pro datové továrny V1 se nemigrují automaticky. Tyto výstrahy je nutné znovu vytvořit na nové infrastruktuře upozorňování. Přihlaste se k Azure Portal a vyberte **monitorování** a vytvořte nové výstrahy týkající se metriky (například neúspěšné spuštění nebo úspěšné spuštění) pro datové továrny verze 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Porozumění kanálům a stavům aktivit
Pomocí Azure Portal můžete:

* Zobrazte datovou továrnu jako diagram.
* Zobrazení aktivit v kanálu.
* Zobrazit vstupní a výstupní datové sady.

Tato část také popisuje, jak se řez datové sady přechází z jednoho stavu do jiného.   

### <a name="navigate-to-your-data-factory"></a>Přejděte do objektu pro vytváření dat.
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce na levé straně klikněte na **Datové továrny** . Pokud ho nevidíte, klikněte na **Další služby >** a potom klikněte na **Datové továrny** v kategorii **Intelligence + Analytics** .

   ![Procházet všechny > datové továrny](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. V okně **Datové továrny** vyberte datovou továrnu, na kterou vás zajímáte.

    ![Výběr datové továrny](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Měla by se zobrazit Domovská stránka objektu pro vytváření dat.

   ![Okno Datová továrna](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Zobrazení diagramu datové továrny
Zobrazení **diagramu** datové továrny poskytuje jediné podokno skla pro monitorování a správu objektu pro vytváření dat a jeho assetů. Zobrazení **diagramu** datové továrny zobrazíte tak, že kliknete na **diagram** na domovské stránce pro datovou továrnu.

![Zobrazení diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Můžete ho přiblížit, oddálit, přiblížit, zvětšit a zmenšit na 100%, zamknout rozložení diagramu a automaticky umístit kanály a datové sady. Můžete si také prohlédnout informace o datovém proudu (tj. zobrazit nadřazené a podřízené položky vybraných položek).

### <a name="activities-inside-a-pipeline"></a>Aktivity uvnitř kanálu
1. Klikněte pravým tlačítkem na kanál a potom klikněte na **Otevřít kanál** . zobrazí se všechny aktivity v kanálu spolu se vstupními a výstupními datovými sadami pro aktivity. Tato funkce je užitečná v případě, že váš kanál obsahuje více než jednu aktivitu a chcete pochopit provozní linii jednoho kanálu.

    ![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. V následujícím příkladu vidíte aktivitu kopírování v kanálu se vstupem a výstupem. 

    ![Aktivity uvnitř kanálu](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Kliknutím na odkaz **Data Factory** v navigační oblasti v levém horním rohu můžete přejít zpátky na domovskou stránku objektu pro vytváření dat.

    ![Přejít zpátky do objektu pro vytváření dat](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Zobrazení stavu každé aktivity v kanálu
Aktuální stav aktivity můžete zobrazit zobrazením stavu kterékoli z datových sad, které jsou vytvořeny aktivitou.

Dvojitým kliknutím na **OutputBlobTable** v **diagramu** můžete zobrazit všechny řezy, které jsou vytvářeny různými aktivitami v kanálu. Můžete vidět, že aktivita kopírování proběhla úspěšně za posledních osm hodin a vytvořila řezy ve stavu **připraveno** .  

![Stav kanálu](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Řezy datové sady v datové továrně mohou mít jeden z následujících stavů:

<table>
<tr>
    <th align="left">State</th><th align="left">Podstav</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">Čekající</td><td>ScheduleTime</td><td>Čas nepřijde, aby se řez spouštěl.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Nadřazené závislosti nejsou připravené.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivity jsou zaneprázdněny spouštěním jiných řezů.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavena a nemůže tyto řezy spustit, dokud nebude aktivita obnovena.</td>
</tr>
<tr>
<td>Zkusit znovu</td><td>Probíhá opakování provádění aktivity.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověřování ještě nebylo zahájeno.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověřování čeká na opakování.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Opětovné</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Řez se zpracovává.</td>
</tr>
<tr>
<td rowspan="4">Neúspěšný</td><td>Vypršel časový limit</td><td>Provádění aktivit trvalo déle, než je povoleno aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Řez byl zrušen akcí uživatele.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření se nezdařilo.</td>
</tr>
<tr>
<td>-</td><td>Řez se nepovedlo vygenerovat nebo ověřit.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Řez je připravený na spotřebu.</td>
</tr>
<tr>
<td>Přeskočeno</td><td>Žádné</td><td>Řez se nezpracovává.</td>
</tr>
<tr>
<td>Žádné</td><td>-</td><td>Řez použitý k existenci s jiným stavem, ale byl obnoven.</td>
</tr>
</table>



Kliknutím na položku řezu v okně **nedávno aktualizované řezy** si můžete zobrazit podrobnosti o řezu.

![Podrobnosti řezu](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Pokud byl řez proveden několikrát, zobrazí se v seznamu **spuštění aktivit** více řádků. Kliknutím na položku Spustit v seznamu **spuštění aktivit** si můžete zobrazit podrobnosti o spuštění aktivit. V seznamu se zobrazí všechny soubory protokolu spolu s chybovou zprávou, pokud je nějaký. Tato funkce je užitečná pro zobrazení a ladění protokolů, aniž byste museli opustit datovou továrnu.

![Podrobnosti o spuštění aktivit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Pokud řez není ve stavu **připraveno** , můžete vidět, které z nadřazených řezů nejsou připravené a které blokují aktuální řez ze spouštění v seznamu **nadřazených řezů, které nejsou připravené** . Tato funkce je užitečná v případě, že je váš řez ve stavu **čekání** a vy chcete pochopit nadřazené závislosti, na kterých řez čeká.

![Nadřazené řezy, které nejsou připravené](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stavu datové sady
Když nasadíte datovou továrnu a kanály mají platné aktivní období, datový objekt se převede z jednoho stavu do druhého. V současné době stav řezu následuje po následujícím diagramu stavu:

![Stavový diagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Tok přechodu stavu datové sady ve službě Data Factory je následující: čekání-> probíhající/probíhající (ověřování) – > připraveno nebo selhalo.

Řez začíná ve stavu **čekání** a čeká na splnění předběžných podmínek, než se spustí. Pak se aktivita začne spouštět a řez přejde **do stavu probíhá** . Spuštění aktivity může být úspěšné nebo neúspěšné. Řez je na základě výsledku provedení označený jako **připravený** nebo **neúspěšný**.

Řez můžete obnovit tak, aby se zpátky ze stavu **připraveno** nebo **nezdařilo** do **čekání** . Můžete také označit stav řezu k **přeskočení**, což zabrání aktivitě v provádění a nezpracovávání řezu.

## <a name="pause-and-resume-pipelines"></a>Pozastavení a obnovení kanálů
Své kanály můžete spravovat pomocí Azure PowerShell. Můžete například pozastavit a obnovit kanály spuštěním rutin Azure PowerShell. 

> [!NOTE] 
> Zobrazení diagramu nepodporuje pozastavení a obnovování kanálů. Pokud chcete použít uživatelské rozhraní, použijte aplikaci pro monitorování a správu. Podrobnosti o používání aplikace najdete v článku [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md) . 

Kanály můžete pozastavit nebo pozastavit pomocí rutiny **Suspend-AzDataFactoryPipeline** prostředí PowerShell. Tato rutina je užitečná, když nechcete, aby se kanály spouštěly, dokud problém nebude vyřešen. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po vyřešení problému s kanálem můžete pozastavený kanál obnovit spuštěním následujícího příkazu PowerShellu:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Ladění kanálů
Azure Data Factory poskytuje bohatě funkční možnosti pro ladění a odstraňování kanálů pomocí Azure Portal a Azure PowerShell.

> [!NOTE] 
> Troubleshotí chyb pomocí aplikace monitoring & Management je mnohem snazší. Podrobnosti o používání aplikace najdete v článku [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md) . 

### <a name="find-errors-in-a-pipeline"></a>Hledání chyb v kanálu
Pokud se spuštění aktivity v kanálu nezdaří, datová sada vytvořená kanálem je v chybovém stavu kvůli selhání. Chyby v Azure Data Factory můžete ladit a řešit pomocí následujících metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Použití Azure Portal k ladění chyby
1. V okně **tabulka** klikněte na řez problému, u kterého je **stav** nastavený na **neúspěšné**.

   ![Okno tabulky s průřezem problému](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. V okně **datový řez** klikněte na spuštěnou aktivitu, která se nezdařila.

   ![Datový řez s chybou](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. V okně **Podrobnosti o spuštění aktivit** můžete stáhnout soubory, které jsou přidruženy ke zpracování HDInsight. Kliknutím na **Stáhnout** pro stav/stderr Stáhněte soubor protokolu chyb, který obsahuje podrobnosti o chybě.

   ![Okno podrobností o spuštění aktivit s chybou](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Použití PowerShellu k ladění chyby
1. Spusťte **PowerShell**.
2. Chcete-li zobrazit řezy a jejich stavy, spusťte příkaz **Get-AzDataFactorySlice** . Měl by se zobrazit řez se stavem **selhalo**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Příklad:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Nahraďte **StartDateTime** počátečním časem vašeho kanálu. 
3. Teď spuštěním rutiny **Get-AzDataFactoryRun** získáte podrobnosti o spuštění aktivit pro daný řez.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Příklad:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Hodnota StartDateTime je čas spuštění řezu chyby nebo problému, který jste si poznamenali v předchozím kroku. Datum a čas by měl být uzavřený v dvojitých uvozovkách.
4. Měl by se zobrazit výstup s podrobnostmi o chybě, která je podobná následující:

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
5. Můžete spustit rutinu **Save-AzDataFactoryLog** s hodnotou ID, kterou vidíte ve výstupu, a stáhnout soubory protokolu pomocí rutiny **-DownloadLogsoption** pro rutinu.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Opětovné spuštění v kanálu selhání

> [!IMPORTANT]
> Řešení chyb a opětovné spuštění neúspěšných řezů pomocí aplikace monitoring & Management je snazší. Podrobnosti o používání aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Po vyřešení potíží a ladění chyb v kanálu můžete chyby znovu spustit tak, že přejdete na chybový řez a kliknete na tlačítko **Spustit** na panelu příkazů.

![Opětovné spuštění neúspěšného řezu](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

V případě neúspěšného ověření řezu v důsledku selhání zásady (například pokud nejsou k dispozici data) můžete chybu opravit a ověřit znovu kliknutím na tlačítko **ověřit** na panelu příkazů.

![Opravte chyby a ověřte](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Použití Azure Powershell
Selhání můžete znovu spustit pomocí rutiny **set-AzDataFactorySliceStatus** . Syntaxe a další podrobnosti o rutině najdete v tématu [set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) .

**Příklad:**

Následující příklad nastaví stav všech řezů pro tabulku ' DAWikiAggregatedData ' na ' wait ' v Azure Data Factory ' WikiADF '.

' Typ aktualizace ' je nastaven na ' UpstreamInPipeline ', což znamená, že stavy jednotlivých výsečí pro tabulku a všechny závislé (nadřazené) tabulky jsou nastaveny na ' čekání '. Další možnou hodnotou pro tento parametr je "jednotlivá".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Vytváření výstrah v Azure Portal

1.  Přihlaste se k Azure Portal a vyberte **výstrahy monitorování->** a otevřete stránku výstrahy.

    ![Otevřete stránku výstrahy.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Vyberte **+ nové pravidlo výstrahy** pro vytvoření nové výstrahy.

    ![Vytvoření nové výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definujte **podmínku upozornění**. (V poli **filtrovat podle typu prostředku** je třeba vybrat **Datové továrny** .) Můžete také zadat hodnoty pro **dimenze**.

    ![Definování podmínky upozornění – výběr cíle](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definování podmínky upozornění – přidat kritéria výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definování podmínky upozornění – Přidání logiky výstrah](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Zadejte **Podrobnosti výstrahy**.

    ![Zadejte podrobnosti výstrahy.](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definujte **skupinu akcí**.

    ![Definujte skupinu akcí – vytvořit novou skupinu akcí.](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definice skupiny akcí – sady vlastností](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definujte skupinu akcí – nová skupina akcí vytvořena.](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Přesunutí datové továrny do jiné skupiny prostředků nebo předplatného
Datovou továrnu můžete přesunout do jiné skupiny prostředků nebo jiného předplatného pomocí tlačítka **přesunout** panel příkazů na domovské stránce vaší datové továrny.

![Přesunout objekt pro vytváření dat](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Spolu s datovou továrnou můžete také přesunout všechny související prostředky (například výstrahy spojené s objektem pro vytváření dat).

![Dialogové okno přesunout prostředky](./media/data-factory-monitor-manage-pipelines/MoveResources.png)