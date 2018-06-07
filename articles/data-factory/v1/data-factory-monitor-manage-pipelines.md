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
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b6cfe6ba510f1e7ed1b448d99fb8a71bb94053e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620679"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorování a Správa kanálů služby Azure Data Factory pomocí portálu Azure a prostředí PowerShell
> [!div class="op_single_selector"]
> * [Použití Azure portal nebo Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [sledování a Správa kanálů služby Data Factory v verze 2](../monitor-visually.md).

Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí portálu Azure a prostředí PowerShell.

> [!IMPORTANT]
> Aplikace monitorování a správu poskytuje lepší podporu pro monitorování a správy datových kanálů a řešení potíží s problémy. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory verze 1 nyní používá nový [Azure monitorování výstrahy infrastruktury](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Původní výstrahy infrastruktury je zastaralý. V důsledku toho existující výstrahy nakonfigurován pro verze 1 dat, které objekty Factory přestane fungovat. Vaše existující výstrahy pro v1 datové továrny se nemigrují automaticky. Budete muset znovu vytvořit tyto výstrahy na nové výstrahy infrastruktury. Přihlaste se k Azure portálu a vyberte možnost **monitorování** k vytvoření nové výstrahy o metrikách (například spuštění neúspěšné nebo úspěšné spustí) pro vaši verzi 1 datové továrny.

## <a name="understand-pipelines-and-activity-states"></a>Pochopit kanály a aktivity stavy
Pomocí portálu Azure, můžete:

* Objekt pro vytváření dat zobrazte jako diagram.
* Zobrazit aktivity v kanálu.
* Zobrazte vstupní a výstupní datové sady.

Tato část také popisuje, jak se řez datovou sadu přechází z jednoho stavu do jiného stavu.   

### <a name="navigate-to-your-data-factory"></a>Přejděte do data factory
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **datové továrny** v nabídce na levé straně. Pokud ho nevidíte, klikněte na tlačítko **další služby >** a potom klikněte na **datové továrny** pod **INTELLIGENCE + analýzy** kategorie.

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
<td>Přeskočeno</td><td>Žádný</td><td>Řez se zpracovává.</td>
</tr>
<tr>
<td>Žádný</td><td>-</td><td>Řez měl dříve jiný stav, ale byla obnovena.</td>
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

> [!NOTE] 
> Je mnohem snazší potíže chyby pomocí aplikace pro správu a monitorování. Podrobnosti o použití této aplikace najdete v tématu [sledování a Správa kanálů služby Data Factory pomocí monitorování a správu aplikace](data-factory-monitor-manage-app.md) článku. 

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
## <a name="create-alerts-in-the-azure-portal"></a>Vytvářet výstrahy na portálu Azure

1.  Přihlaste se k Azure portálu a vyberte možnost **monitorování -> výstrahy** chcete otevřít stránku výstrahy.

    ![Otevřete stránku výstrahy.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Vyberte **+ nové pravidlo výstrahy** vytvořit nové oznámení.

    ![Vytvořit nové oznámení](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definování **výstrahy podmínku**. (Nezapomeňte vybrat **datové továrny** v **filtrovat podle typu prostředku** pole.) Můžete také zadat hodnoty pro **dimenze**.

    ![Definovat se výstražný stav - vyberte cíl](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definovat podmínku upozornění – přidání kritérií výstrah](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definovat podmínku upozornění – přidání upozornění logiky](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definování **podrobnosti výstrahy**.

    ![Zadejte podrobnosti výstrahy](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definování **akce skupiny**.

    ![Definování skupiny akce – vytvoření nové skupiny akce](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definování skupiny akce – umožňuje nastavit vlastnosti](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definování skupiny akce - vytvořit nové akce skupiny](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Přesunout objekt pro vytváření dat do jiné skupině prostředků nebo předplatného
Objekt pro vytváření dat můžete přesunout na jinou skupinu prostředků nebo jiného předplatného pomocí **přesunout** příkazový řádek na domovské stránce objektu pro vytváření dat tlačítko.

![Přesunout objekt pro vytváření dat](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Také můžete přesunout všechny související prostředky (například výstrahy, které jsou přidruženy objektu pro vytváření dat), spolu s služby data factory.

![Dialogové okno prostředků](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
