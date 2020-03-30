---
title: Monitorování a správa kanálů pomocí portálu Azure a PowerShellu
description: Zjistěte, jak pomocí portálu Azure a Azure PowerShellu monitorovat a spravovat datové továrny a kanály Azure, které jste vytvořili.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666966"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorování a správa kanálů Azure Data Factory pomocí portálu Azure a PowerShellu
> [!div class="op_single_selector"]
> * [Používání Azure portal/Azure PowerShellu](data-factory-monitor-manage-pipelines.md)
> * [Používání aplikace Monitorování a správa](data-factory-monitor-manage-app.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma monitorování a správa kanálů Data Factory v .](../monitor-visually.md)

Tento článek popisuje, jak sledovat, spravovat a ladit vaše kanály pomocí portálu Azure a PowerShellu.

> [!IMPORTANT]
> Aplikace pro správu & monitorování poskytuje lepší podporu pro monitorování a správu datových kanálů a řešení problémů. Podrobnosti o používání aplikace najdete v [tématu monitorování a správa kanálů Data Factory pomocí aplikace Monitorování a správa](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory verze 1 teď používá novou [infrastrukturu upozornění Azure Monitoru](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Stará infrastruktura upozornění je zastaralá. V důsledku toho již nefungují existující výstrahy nakonfigurované pro továrny na data verze 1. Vaše stávající výstrahy pro továrny dat v1 se nemigrují automaticky. Je třeba znovu vytvořit tyto výstrahy na nové infrastruktury výstrah. Přihlaste se k portálu Azure a vyberte **Monitor,** chcete-li vytvořit nové výstrahy na metriky (například neúspěšné spuštění nebo úspěšné spuštění) pro vaše továrny na data verze 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Principy kanálů a stavů aktivit
Pomocí portálu Azure můžete:

* Zobrazení datové továrny jako diagramu.
* Zobrazení aktivit v kanálu.
* Zobrazení vstupních a výstupních datových sad.

Tato část také popisuje, jak řez datové sady přechází z jednoho stavu do jiného stavu.   

### <a name="navigate-to-your-data-factory"></a>Přejděte do datové továrny
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce vlevo klikněte na **Datové továrny.** Pokud ho nevidíte, klikněte na **Další služby >** a potom klikněte na **Datové továrny** v kategorii **INTELLIGENCE + ANALYTICS.**

   ![Procházet všechny > datové továrny](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. V okně **Datové továrny** vyberte datovou továrnu, která vás zajímá.

    ![Výběr datové továrny](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Měli byste vidět domovskou stránku pro datové továrny.

   ![Okno Datová továrna](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Zobrazení diagramu vaší datové továrny
Zobrazení **diagramu** datové továrny poskytuje jediné podokno skla pro sledování a správu datové továrny a jejích prostředků. Pokud chcete zobrazit zobrazení **diagramu** vaší továrny dat, klikněte na titulní stránce datové továrny na **Diagram.**

![Zobrazení diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Můžete přiblížit, oddálit, zvětšit, přizpůsobit, přiblížit na 100 %, zamknout rozložení diagramu a automaticky umístit kanály a datové sady. Můžete také zobrazit informace o datové linii (to znamená zobrazit položky vybraných položek).

### <a name="activities-inside-a-pipeline"></a>Aktivity uvnitř kanálu
1. Klikněte pravým tlačítkem myši na kanál a potom klikněte na **Otevřít kanál** zobrazíte všechny aktivity v kanálu, spolu se vstupními a výstupními datovými sadami pro aktivity. Tato funkce je užitečná, když váš kanál obsahuje více než jednu aktivitu a chcete pochopit provozní linie jednoho kanálu.

    ![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. V následujícím příkladu se zobrazí aktivita kopírování v kanálu se vstupem a výstupem. 

    ![Aktivity uvnitř kanálu](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Na domovskou stránku datové továrny můžete přejít kliknutím na odkaz **Data factory** v popisce cesty v levém horním rohu.

    ![Přechod zpět do datové továrny](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Zobrazení stavu jednotlivých aktivit v kanálu
Aktuální stav aktivity můžete zobrazit zobrazením stavu libovolné datové sady, které jsou vytvořeny aktivitou.

Poklepáním na **OutputBlobTable** v **diagramu**, můžete zobrazit všechny řezy, které jsou vytvářeny různé aktivity běží uvnitř kanálu. Můžete vidět, že aktivita kopírování byla úspěšně spuštěna za posledních osm hodin a vytvořila řezy ve stavu **Připraveno.**  

![Stav potrubí](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Řezy datové sady v datové továrně mohou mít jeden z následujících stavů:

<table>
<tr>
    <th align="left">Stav</th><th align="left">Podstát</th><th align="left">Popis</th>
</tr>
<tr>
    <td rowspan="8">Čekání</td><td>ScheduleTime</td><td>Nenadešel čas, aby se ten kousek rozběhl.</td>
</tr>
<tr>
<td>Závislostí datové sady</td><td>Upstream závislosti nejsou připraveny.</td>
</tr>
<tr>
<td>Výpočetní prostředky</td><td>Výpočetní prostředky nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivity jsou zaneprázdněny spuštěním jiných řezů.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavena a nelze spustit řezy, dokud aktivita je obnovena.</td>
</tr>
<tr>
<td>Retry</td><td>Provádění aktivity je opakováno.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření ještě nezačalo.</td>
</tr>
<tr>
<td>Ověření Opakování</td><td>Ověření čeká na opakování.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá</td><td>Ověřování</td><td>Probíhá ověření.</td>
</tr>
<td>-</td>
<td>Řez se zpracovává.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>Timedout</td><td>Provádění aktivity trvalo déle, než je povoleno aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Řez byl zrušen akcí uživatele.</td>
</tr>
<tr>
<td>Ověřování</td><td>Ověření se nezdařilo.</td>
</tr>
<tr>
<td>-</td><td>Řez se nepodařilo vygenerovat a/nebo ověřit.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Řez je připraven ke spotřebě.</td>
</tr>
<tr>
<td>Přeskočen</td><td>Žádný</td><td>Řez se nezpracovává.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Řez používaný k existenci s jiným stavem, ale byl resetován.</td>
</tr>
</table>



Podrobnosti o řezu můžete zobrazit klepnutím na položku řezu v okně **Naposledy aktualizované řezy.**

![Podrobnosti řezu](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Pokud byl řez proveden vícekrát, zobrazí se v seznamu **spuštění aktivity** více řádků. Podrobnosti o aktivitě spuštěné můžete zobrazit kliknutím na položku spuštění v seznamu **Spuštění spustí.** Seznam zobrazuje všechny soubory protokolu spolu s chybovou zprávou, pokud existuje. Tato funkce je užitečná pro zobrazení a ladění protokolů bez nutnosti opustit továrnu na data.

![Podrobnosti o spuštění aktivit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Pokud řez není ve stavu **Připraveno,** uvidíte výřezy proti proudu, které nejsou připravené a blokují spuštění aktuálního řezu v seznamu **Výřezů upstream, které nejsou připravené.** Tato funkce je užitečná, když je řez ve stavu **Čekání** a chcete pochopit závislosti proti proudu, na které řez čeká.

![Protiproudé řezy, které nejsou připraveny](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stavu datové sady
Po nasazení datové továrny a kanály mají platné aktivní období, datová sada řezy přechod z jednoho stavu do druhého. V současné době se stav řezu řídí následujícím diagramem stavu:

![Diagram stavu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Tok přechodu stavu datové sady v továrně dat je následující: Čekání -> In-Progress/In-In (Ověřování) -> Ready/Failed.

Řez začíná ve stavu **Čekání** a čeká na splnění podmínek před jeho spuštěním. Potom aktivita spustí provádění a řez přejde do stavu **Probíhá.** Spuštění aktivity může být úspěšné nebo neúspěšné. Řez je označen jako **Připravený** nebo **Neúspěšný**na základě výsledku spuštění.

Řez můžete obnovit a vrátit se ze stavu **Připraveno** nebo **Se nezdařilo** do stavu **Čekání.** Můžete také označit stav řezu **přeskočit**, který zabrání provádění aktivity a nezpracování řezu.

## <a name="pause-and-resume-pipelines"></a>Pozastavení a obnovení kanálů
Své kanály můžete spravovat pomocí Azure PowerShellu. Například můžete pozastavit a obnovit kanály spuštěním rutin Azure PowerShell. 

> [!NOTE] 
> Zobrazení diagramu nepodporuje pozastavení a obnovení potrubí. Pokud chcete používat uživatelské rozhraní, použijte monitorování a správu aplikace. Podrobnosti o používání aplikace najdete v [tématu monitorování a správa kanálů Data Factory pomocí](data-factory-monitor-manage-app.md) článku aplikace Monitorování a správa. 

Kanály můžete pozastavit nebo pozastavit pomocí rutiny **prostředí PowerShell pozastavit a pozastavit.** Tato rutina je užitečná, pokud nechcete spouštět kanály, dokud nebude problém vyřešen. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Například:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po opevnění problému s kanálem můžete pokračovat v pozastaveném kanálu spuštěním následujícího příkazu prostředí PowerShell:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Například:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Ladění kanálů
Azure Data Factory poskytuje bohaté funkce pro ladění a řešení potíží s kanály pomocí portálu Azure a Azure PowerShellu.

> [!NOTE] 
> Je mnohem jednodušší troubleshot chyby pomocí sledování & Management App. Podrobnosti o používání aplikace najdete v [tématu monitorování a správa kanálů Data Factory pomocí](data-factory-monitor-manage-app.md) článku aplikace Monitorování a správa. 

### <a name="find-errors-in-a-pipeline"></a>Hledání chyb v kanálu
Pokud se spuštění aktivity nezdaří v kanálu, datová sada vytvořená kanálem je v chybovém stavu z důvodu selhání. Můžete ladit a řešit chyby v Azure Data Factory pomocí následujících metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Ladění chyby pomocí portálu Azure
1. V okně **Tabulka** klepněte na problémový řez, ve které je **stav** nastaven na **Nepodařilo se**.

   ![Čepel stolu s problémovým řezem](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. V okně **Řez dat** klikněte na spuštění aktivity, které se nezdařilo.

   ![Řez dat s chybou](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. V okně **Podrobnosti spuštění aktivity** můžete stáhnout soubory, které jsou přidruženy ke zpracování HDInsight. Chcete-li stáhnout soubor protokolu chyb, který obsahuje podrobnosti o chybě, klepněte na tlačítko **Stáhnout** pro stav nebo stderr.

   ![Okno podrobností spuštění aktivity s chybou](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Ladění chyby pomocí prostředí PowerShell
1. Spusťte **prostředí PowerShell**.
2. Spusťte příkaz **Get-AzDataFactorySlice,** abyste viděli řezy a jejich stavy. Měli byste vidět řez se stavem **Failed**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Například:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Nahraďte **startdatetime** časem zahájení kanálu. 
3. Nyní spusťte rutinu **Get-AzDataFactoryRun,** abyste získali podrobnosti o aktivitě spuštěné pro řez.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Například:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Hodnota StartDateTime je čas zahájení pro chybu nebo problém řezu, který jste zaznamenali z předchozího kroku. Datum a čas by měl být uzavřen v uvozovkách.
4. Měli byste vidět výstup s podrobnostmi o chybě, která je podobná následující:

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
5. Můžete spustit **Rutina Save-AzDataFactoryLog** s hodnotou Id, která se zobrazí z výstupu, a stáhnout soubory protokolu pomocí **možnosti -DownloadLogs** pro rutinu.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Opětovné spuštění selhání v kanálu

> [!IMPORTANT]
> Je jednodušší řešit chyby a znovu spustit neúspěšné řezy pomocí aplikace Monitoring & Management. Podrobnosti o používání aplikace najdete v [tématu monitorování a správa kanálů Data Factory pomocí aplikace Monitorování a správa](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Po řešení potíží a ladění selhání v kanálu, můžete znovu spustit selhání přechodem na část chyby a klepnutím na tlačítko **Spustit** na panelu příkazů.

![Opětovné spuštění neúspěšného řezu](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

V případě, že se ověření řezu nezdařilo z důvodu selhání zásad (například pokud data nejsou k dispozici), můžete chybu opravit a znovu ověřit klepnutím na tlačítko **Ověřit** na panelu příkazů.

![Oprava chyb a ověření](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Použití Azure Powershell
Chyby lze znovu spustit pomocí rutiny **Set-AzDataFactorySliceStatus.** Syntaxi a další podrobnosti o rutině naleznete v tématu [Set-AzDataFactorySliceStatus.](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus)

**Příklad:**

Následující příklad nastaví stav všech řezů pro tabulku DAWikiAggregatedData na "Čekání" v továrně dat Azure "WikiADF".

'UpdateType' je nastavena na 'UpstreamInPipeline', což znamená, že stavy každého řezu pro tabulku a všechny závislé (proti proudu) tabulky jsou nastaveny na "Čekání". Další možnou hodnotou pro tento parametr je "Jednotlivec".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Vytváření výstrah na webu Azure Portal

1.  Přihlaste se k portálu Azure a vyberte **Monitor -> výstrahy** otevřete stránku Výstrahy.

    ![Otevřete stránku Výstrahy.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Chcete-li vytvořit novou výstrahu, vyberte **možnost + nové pravidlo výstrahy.**

    ![Vytvoření nové výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definujte **podmínku výstrahy**. (Ujistěte se, že v poli **Filtrovat podle typu zdroje** vyberete **datové továrny.)** Můžete také zadat hodnoty pro **dimenze**.

    ![Definovat výstražnou podmínku – vybrat cíl](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definovat výstražnou podmínku – přidat kritéria výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definovat stav výstrahy – přidat logiku výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definujte **podrobnosti výstrahy**.

    ![Definování podrobností výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definujte **skupinu akcí**.

    ![Definovat skupinu akcí – vytvoření nové skupiny akcí](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definovat skupinu akcí - nastavit vlastnosti](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definovat skupinu akcí – byla vytvořena nová skupina akcí](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Přesunutí datové továrny do jiné skupiny prostředků nebo předplatného
Továrnu dat můžete přesunout do jiné skupiny prostředků nebo jiného předplatného pomocí tlačítka **Přesunout** panel příkazů na domovské stránce datové továrny.

![Přesunout datovou továrnu](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Můžete také přesunout všechny související prostředky (například výstrahy, které jsou přidruženy k datové továrny), spolu s factory dat.

![Dialogové okno Přesunout zdroje](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
