---
title: Řešení problémů s Azure Data Factory
description: Informace o řešení problémů s pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a9a4f7b19d6ef08562d17474e252ce72fd0977dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-data-factory-issues"></a>Poradce při potížích se službou Data Factory
> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je obecně dostupná (GA). 

Tento článek obsahuje tipy k řešení potíží pro problémy při použití Azure Data Factory. V tomto článku nejsou uvedeny všechny možné problémy při použití služby, týká se však některé problémy a Obecné tipy k řešení potíží.   

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Chyba: Pro předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory.
Pokud se zobrazí tato chyba, poskytovatel prostředků Azure Data Factory není na vašem počítači zaregistrovaný. Udělejte toto:

1. Spusťte Azure PowerShell.
2. Přihlaste se k účtu Azure, pomocí následujícího příkazu.

    ```powershell
    Connect-AzureRmAccount
    ```
3. Spusťte následující příkaz pro registraci zprostředkovatele služby Azure Data Factory.

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problém: Neoprávněným Chyba při spuštění rutiny služby Data Factory
Pravděpodobně pro Azure PowerShell nepoužíváte správné předplatné nebo účet Azure. Pomocí následujících rutin vyberte správné předplatné a účet Azure pro použití s Azure PowerShellem.

1. Připojit-AzureRmAccount - použití správné uživatelské ID a heslo
2. Get-AzureRmSubscription - zobrazte všechna předplatná pro účet.
3. Select-AzureRmSubscription &lt;název odběru&gt; -vyberte správné předplatné. Použijte stejný jako ten, který používáte pro vytváření dat na portálu Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problém: Nepodařilo Data Management Gateway Express instalaci spustit z portálu Azure
Expresní instalace pro Bránu pro správu dat vyžaduje Internet Explorer nebo webový prohlížeč kompatibilní s technologií Microsoft ClickOnce. Pokud se expresní instalace nezdaří, použijte jeden z následujících postupů:

* Použijte Internet Explorer nebo Microsoft ClickOnce kompatibilní webového prohlížeče.

    Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.

    Totéž proveďte pro Firefox (instalace doplňku). Na panelu nástrojů klikněte na tlačítko Otevřít nabídku (tři vodorovné čáry v pravém horním rohu), klikněte na Doplňky, dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
* Použití **ruční instalace** odkazu je zobrazen v okně stejné na portálu. Tuto metodu použijte k stáhněte instalační soubor a spustit ručně. Po úspěšné instalaci, zobrazí se dialogové okno Konfigurace brány pro správu dat. Zkopírujte **klíč** z okna portálu a použijte ho ve správci konfigurace k ruční registraci brány v příslušné službě.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problém: Nepodaří připojit k místnímu SQL serveru
Spusťte **Správce konfigurace brány pro správu dat** na počítači brány a použít **Poradce při potížích s** kartě, abyste otestovali připojení k systému SQL Server z počítače brány. V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problém: Vstupní řezy jsou ve stavu čekání pro někdy
Řezy může být v **čekání** stavu z různých důvodů. Jeden z běžných příčin je, že **externí** vlastnost není nastavena na **true**. Žádné datové sady, která je vytvořena mimo obor Azure Data Factory by měl být označené jako **externí** vlastnost. Tato vlastnost určuje, že data jsou externí a nejsou zálohovány pomocí žádné kanály v rámci objektu pro vytváření dat. Jakmile jsou data v příslušných úložištích dostupná, datové řezy se označí jako **připravené**.

Použití vlastnosti **external** si můžete prohlédnout v následujícím příkladu. Volitelně můžete zadat **externalData*** Pokud nastavíte externí na hodnotu true.

V tématu [datové sady](data-factory-create-datasets.md) článku Další podrobnosti o této vlastnosti.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Pokud chcete tuto chybu vyřešit, přidejte vlastnost **external** a volitelný oddíl **externalData** do definice JSON vstupní tabulky a potom tuto tabulku vytvořte znovu.

### <a name="problem-hybrid-copy-operation-fails"></a>Problém: Operace kopírování hybridní selže
V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kroky pro řešení problémů s kopírování do nebo z místních dat úložiště pomocí Brána pro správu dat.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problém: HDInsight na vyžádání zřizování nezdaří
Pokud používáte propojené služby typu HDInsightOnDemand, budete muset zadat linkedServiceName, která odkazuje na Azure Blob Storage. Služba Data Factory využívá toto úložiště k ukládání protokolů a podpůrných souborů pro cluster HDInsight na vyžádání.  Někdy se zřizování clusteru HDInsight na vyžádání nezdaří s následující chybou:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Tato zpráva obvykle indikuje, že umístění účtu úložiště zadané vlastností linkedServiceName se neshoduje s umístěním datového centra, ve kterém dochází ke zřizování HDInsightu. Příklad: Pokud je objekt pro vytváření dat v západní USA a Azure storage je v oblasti Východ USA, na vyžádání zřizování nezdaří západní USA.

Navíc existuje ještě druhá vlastnost additionalLinkedServiceNames JSON, která umožňuje zadat další účty úložiště v HDInsightu na vyžádání. Tyto další propojené účty úložiště by měl být ve stejném umístění jako HDInsight cluster, nebo se nezdaří s ke stejné chybě.

### <a name="problem-custom-net-activity-fails"></a>Problém: Vlastní selže aktivity rozhraní .NET
V tématu [ladění kanálu s aktivitou vlastní](data-factory-use-custom-activities.md#troubleshoot-failures) podrobné pokyny.

## <a name="use-azure-portal-to-troubleshoot"></a>Řešení potíží pomocí portálu Azure
### <a name="using-portal-blades"></a>Pomocí portálu oken
V tématu [monitorování kanálu](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) kroky.

### <a name="using-monitor-and-manage-app"></a>Pomocí aplikace Monitorování a správa
V tématu [monitorování a Správa kanálů služby data factory pomocí monitorování a Správa aplikací](data-factory-monitor-manage-app.md) podrobnosti.

## <a name="use-azure-powershell-to-troubleshoot"></a>Pomocí prostředí Azure PowerShell k řešení potíží
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Pomocí prostředí Azure PowerShell k řešení potíží s chybu
V tématu [kanálů monitorování pro vytváření dat pomocí Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) podrobnosti.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
