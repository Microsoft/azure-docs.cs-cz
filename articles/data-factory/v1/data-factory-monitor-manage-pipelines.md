---
title: Monitorování a Správa kanálů pomocí webu Azure portal a Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí webu Azure portal a prostředí Azure PowerShell monitorovat a spravovat objekty pro vytváření dat Azure a kanály, které jste vytvořili.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 843b92c20b2ec930ce67659802a4287328a08650
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618865"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorování a Správa kanálů Azure Data Factory pomocí webu Azure portal a Powershellu
> [!div class="op_single_selector"]
> * [Pomocí Azure portal nebo Azure Powershellu](data-factory-monitor-manage-pipelines.md)
> * [Pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [monitorování a Správa kanálů Data Factory v](../monitor-visually.md).

Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí webu Azure portal a PowerShell.

> [!IMPORTANT]
> Monitorování a Správa aplikací poskytuje lepší podporu pro monitorování a Správa datových kanálů a řešit jakékoli problémy. Podrobnosti o použití této aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory verze 1 nyní používá nový [Azure Monitor výstrahy infrastruktury](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Staré výstrahy infrastruktury je zastaralý. V důsledku toho existující výstrahy nakonfigurované pro verzi 1 datové továrny už nebude fungovat. Existující upozornění pro v1 datové továrny se nemigrují automaticky. Budete muset znovu vytvořit tyto výstrahy na nové výstrahy infrastruktury. Přihlaste se k Azure portal a vyberte **monitorování** vytvořit nové výstrahy týkající se metrik (jako jsou neúspěšná spuštění nebo úspěšná spuštění) pro vaši verzi 1 datových továren.

## <a name="understand-pipelines-and-activity-states"></a>Vysvětlení stavů aktivit a kanálů
Pomocí webu Azure portal můžete:

* Zobrazte jako diagram svou datovou továrnu.
* Zobrazit aktivity v kanálu.
* Zobrazte vstupní a výstupní datové sady.

Tato část také popisuje, jak datovou sadu řez přechází z jednoho stavu do jiného stavu.   

### <a name="navigate-to-your-data-factory"></a>Přejděte do služby data factory
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **datových továren** v nabídce na levé straně. Pokud ho nevidíte, klikněte na tlačítko **další služby >** a potom klikněte na tlačítko **datových továren** pod **INTELIGENCE a analýza** kategorie.

   ![Procházet vše > datové továrny](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Na **datových továren** okně vyberte služby data factory, který vás zajímá.

    ![Výběr datové továrny](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Zobrazí domovská stránka datové továrny.

   ![Okno Datová továrna](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Zobrazení diagramu datové továrny
**Diagram** zobrazení služby data factory poskytuje podokně ze skla ke sledování a správě data factory a její prostředky. Pokud chcete zobrazit **Diagram** služby data factory, klikněte na **Diagram** na domovské stránce objektu pro vytváření dat.

![Zobrazení diagramu](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Můžete přiblížit, oddálení, zvětšení na přizpůsobit, zvětšení na 100 %, uzamknout rozvržení diagramu a umožňuje automatické umísťování kanálů a datových sad. Můžete také zobrazit informace o rodokmenu dat (to znamená, zobrazit nadřazené a podřízené položky vybraných položek).

### <a name="activities-inside-a-pipeline"></a>Aktivity v kanálu
1. Klikněte pravým tlačítkem na kanálu a potom klikněte na tlačítko **otevřít kanál** zobrazíte všechny aktivity v kanálu spolu se vstupními a výstupními datovými sadami pro aktivity. Tato funkce je užitečná, když svůj kanál obsahuje více než jednu aktivitu a chcete pochopit provozní rodokmenu jeden kanál.

    ![Nabídka Otevřít kanál](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. V následujícím příkladu uvidíte aktivitu kopírování v kanálu se vstup a výstup. 

    ![Aktivity v kanálu](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Můžete přejít zpět na domovskou stránku služby data factory, kliknutím **služby Data factory** odkaz z jeho levého horního rohu.

    ![Přejděte zpět do služby data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Zobrazit stav každé aktivity v kanálu
Můžete zobrazit aktuální stav aktivity zobrazením stavu u všech datových sad, které jsou produkované aktivitou.

Dvojitým kliknutím **OutputBlobTable** v **Diagram**, zobrazí se všechny řezy, které jsou vytvářeny v jiné aktivitě spuštění v kanálu. Můžete uvidíte, že aktivita kopírování běžel úspěšně posledních osm hodin a vytvořen výseče **připravené** stavu.  

![Stav kanálu](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Kolekce obsahuje nějaké řezy datové sady ve službě data factory může mít jednu z následujících stavů:

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
<td>ComputeResources</td><td>Výpočetní prostředky, které nejsou k dispozici.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Všechny instance aktivit právě zpracovávají jiné řezy.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivita je pozastavená a řezy nelze spustit, dokud nebude obnoven aktivity.</td>
</tr>
<tr>
<td>Retry</td><td>Je o provádění aktivity.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření se ještě nespustilo.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ověření čeká se na opakování pokusu.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Probíhá zpracování.</td><td>Ověřování platnosti</td><td>Probíhá ověřování.</td>
</tr>
<td>-</td>
<td>Řez se zpracovává.</td>
</tr>
<tr>
<td rowspan="4">Selhalo</td><td>Vypršel časový limit</td><td>Provádění aktivity trvalo déle, než povoluje aktivitou.</td>
</tr>
<tr>
<td>Zrušeno</td><td>Řez bylo zrušeno uživatelem.</td>
</tr>
<tr>
<td>Ověření</td><td>Ověření selhalo.</td>
</tr>
<tr>
<td>-</td><td>Vygenerovat nebo ověřit řez se nezdařilo.</td>
</tr>
<td>Připraveno</td><td>-</td><td>Řez je připraven ke spotřebování.</td>
</tr>
<tr>
<td>Přeskočené</td><td>Žádný</td><td>Řez se právě zpracovává.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Řez používá se jiný stav, ale bylo resetováno.</td>
</tr>
</table>



Můžete zobrazit podrobnosti o řezu po kliknutí na vstupní řez **nedávno aktualizován řezy** okno.

![Podrobnosti o řezu](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Pokud spuštění více než jednou řez zobrazí více řádků **spuštění aktivit** seznamu. Můžete zobrazit podrobnosti o spuštění kliknutím na tlačítko Spustit záznam v aktivity **spuštění aktivit** seznamu. V seznamu jsou uvedeny všechny soubory protokolů, spolu s chybovou zprávu, pokud existuje. Tato funkce je užitečná k zobrazení a ladění protokoly, aniž byste museli opustit svou datovou továrnu.

![Podrobnosti o spuštění aktivit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Není-li řez v **připravené** stavu, zobrazí se upstreamové datové řezy, které ještě nejsou připravené a které blokují ve spuštění aktuálního řezu **Upstreamové datové řezy, které nejsou připraveny** seznamu. Tato funkce je užitečná, když je vaše řez ve **čekání** stavu a chcete pochopit upstreamové závislosti řezu čeká na.

![Upstreamové datové řezy, které nejsou připraveny](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram stavu datovou sadu
Po nasazení služby data factory a kanály mají platný aktivního období, datové sady řezy přechod z jednoho stavu do druhého. Stav řezu v současné době probíhá následující diagram stavu:

![Diagram stavu](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Tok přechod stavu datovou sadu ve službě data factory je následující: čekání -> průběh/v – probíhá (ověřování) -> Připraveno nebo Failed.

Řez se spustí v **čekání** stavu čekání na předběžné podmínky splněny, než se provede. Potom spuštěním aktivity spuštění a řez přejde do **probíhá** stavu. Provádění aktivity může úspěch nebo neúspěch. Řez je označen jako **připravené** nebo **neúspěšné**podle výsledků spuštění.

Můžete resetovat řez se vrátíte z **připravené** nebo **neúspěšné** do stavu **čekání** stavu. Můžete také označit stav řezu, který má **přeskočit**, což zabraňuje aktivita z provádění a ne zpracování řezu.

## <a name="pause-and-resume-pipelines"></a>Pozastavení a obnovení kanály
Vaše kanály můžete spravovat pomocí Azure Powershellu. Například můžete pozastavit a obnovit kanály spuštěním rutin prostředí Azure PowerShell. 

> [!NOTE] 
> Pozastavování a obnovování kanály, zobrazení diagramu se nepodporuje. Pokud chcete použít uživatelské rozhraní, pomocí monitorování a Správa aplikace. Podrobnosti o použití této aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md) článku. 

Je možné pozastavit/pozastavit kanály pomocí **Suspend-AzureRmDataFactoryPipeline** rutiny Powershellu. Tato rutina je užitečná, když nechcete, aby ke spuštění kanálů, dokud nebude problém vyřešen. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Po vyřešení problému s kanálem, lze obnovit pozastavenou kanálu spuštěním následujícího příkazu Powershellu:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Příklad:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Ladění kanálů
Azure Data Factory nabízí bohaté možnosti, ladění a řešení potíží s kanály s využitím webu Azure portal a Azure Powershellu.

> [!NOTE] 
> Je mnohem jednodušší potíže chyby pomocí monitorování a správu aplikací. Podrobnosti o použití této aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md) článku. 

### <a name="find-errors-in-a-pipeline"></a>Vyhledejte chyby v kanálu
Pokud se nezdaří spuštění aktivity v kanálu, datové sady, který je vytvořen kanál je v chybovém stavu kvůli selhání. Můžete ladit a řešení potíží s chybami v Azure Data Factory pomocí následujících metod.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Ladění chybu pomocí webu Azure portal
1. Na **tabulky** okno, kliknutím na řez otevřete problém, který má **stav** nastavena na **neúspěšné**.

   ![Okno tabulky s problém řezu](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Na **datový řez** okna, klikněte na tlačítko spuštění aktivity, která selhala.

   ![Datový řez s chybou](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Na **podrobnosti o spuštění aktivit** okně si můžete stáhnout soubory, které jsou spojené s HDInsight zpracování. Klikněte na tlačítko **Stáhnout** pro stav/stderr stáhněte si soubor protokolu chyb, který obsahuje podrobnosti o chybě.

   ![Spuštění s chybou podrobnosti o okně aktivity](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Použití Powershellu k ladění k chybě
1. Spusťte **PowerShell**.
2. Spustit **Get-AzureRmDataFactorySlice** příkazu zobrazíte řezy a jejich stavy. Zobrazí se řez ve stavu **neúspěšné**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Příklad:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Nahraďte **StartDateTime** s časem zahájení vašeho kanálu. 
3. Nyní, spusťte **Get-AzureRmDataFactoryRun** rutiny zobrazíte podrobnosti o aktivitě spuštění řez.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Příklad:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Hodnota StartDateTime je počáteční čas řezu chybě nebo problému, který jste si poznamenali v předchozím kroku. Datum a čas by měl být uzavřen do dvojitých uvozovek.
4. Byste měli vidět výstup s podrobnostmi o chyby, ke které je podobný následujícímu:

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
5. Můžete spustit **uložit AzureRmDataFactoryLog** rutina s hodnotou Id najdete v článku z výstupu a stáhněte si soubory protokolů pomocí **- DownloadLogsoption** pro rutinu.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Opětovné spuštění při selhání v rámci kanálu

> [!IMPORTANT]
> Je snazší řešení potíží s chybami a znovu spusťte řezy selhání pomocí monitorování a správu aplikací. Podrobnosti o použití této aplikace najdete v tématu [monitorování a Správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Po řešení a ladit chyby v kanálu, můžete znovu spustit selhání přechodem k chybě řezu a kliknutím **spustit** tlačítko na panelu příkazů.

![Opětovné spuštění neúspěšné řezu](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

V případě řez se nezdařilo ověření z důvodu selhání zásad (například pokud data nejsou k dispozici), můžete opravit chyby a znovu ověřit kliknutím **ověřit** tlačítko na panelu příkazů.

![Opravte chyby a ověřit](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Použití Azure Powershell
Můžete znovu spustit selhání pomocí **Set-AzureRmDataFactorySliceStatus** rutiny. Zobrazit [Set-AzureRmDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/azurerm.datafactories/set-azurermdatafactoryslicestatus) tématu pro syntaxi a další podrobnosti o rutině.

**Příklad:**

Následující příklad nastaví stav všech řezů pro tabulku "DAWikiAggregatedData" čekání ve službě Azure data factory "WikiADF".

Typ "aktualizace" je nastavena na "UpstreamInPipeline", což znamená, že stavy každý řez pro tabulku a všechny závislé (nadřazeného) tabulky nastavené na "Čeká". S možnou hodnotou pro tento parametr je "Osoba".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Vytvářet upozornění na webu Azure Portal

1.  Přihlaste se k Azure portal a vyberte **monitorování -> výstrahy** k otevření stránky s upozorněními.

    ![Otevření stránky s upozorněními.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Vyberte **+ nové pravidlo upozornění** vytvořit nové oznámení.

    ![Vytvořit nové upozornění.](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definovat **upozornění podmínku**. (Nezapomeňte vybrat **datových továren** v **filtrovat podle typu prostředku** pole.) Můžete také zadat hodnoty pro **dimenze**.

    ![Definujte podmínku upozornění – Výběr cíle](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definujte podmínku upozornění – přidání kritérií výstrah](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definujte podmínku upozornění – přidání logika upozornění](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definovat **podrobnosti výstrahy**.

    ![Definujte podrobnosti o upozornění](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definovat **skupiny akcí**.

    ![Definujte skupinu akcí – vytvoření nové skupiny akcí](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definujte skupinu akcí – nastavení vlastností](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definujte skupinu akcí - vytvoří nová skupina akcí](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Přesunout objekt pro vytváření dat do jiné skupiny prostředků nebo předplatného
Datové továrny můžete přesunout do jiné skupiny prostředků nebo jiném předplatném s použitím **přesunout** příkazový řádek tlačítko na domovské stránce vaší datové továrně.

![Přesunutí objektu pro vytváření dat](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Můžete také přesunout všechny související prostředky (jako například výstrahy, které jsou spojeny s datovou továrnou), spolu s datovou továrnou.

![Přesunutí prostředků – dialogové okno](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
