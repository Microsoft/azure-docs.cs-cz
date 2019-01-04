---
title: Řešení potíží s Azure Data Factory
description: Informace o řešení potíží při použití služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: c41f03494720c9283bb3ce91fda6e3981f305084
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023017"
---
# <a name="troubleshoot-data-factory-issues"></a>Poradce při potížích se službou Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek poskytuje tipy pro řešení potíží pro problémy při použití služby Azure Data Factory. Tento článek neobsahuje všechny možné problémy při použití služby, ale uvádí některé problémy a Obecné tipy pro řešení potíží.   

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Chyba: Předplatné není zaregistrované používání oboru názvů Microsoft.DataFactory.
Pokud se zobrazí tato chyba, poskytovatel prostředků Azure Data Factory není na vašem počítači zaregistrovaný. Udělejte toto:

1. Spusťte Azure PowerShell.
2. Přihlaste se ke svému účtu Azure pomocí následujícího příkazu.

    ```powershell
    Connect-AzureRmAccount
    ```
3. Spuštěním následujícího příkazu zaregistrujte poskytovatele Azure Data Factory.

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problém: Neautorizovaná chybová zpráva při spuštění rutiny služby Data Factory
Pravděpodobně pro Azure PowerShell nepoužíváte správné předplatné nebo účet Azure. Pomocí následujících rutin vyberte správné předplatné a účet Azure pro použití s Azure PowerShellem.

1. Connect-AzureRmAccount - použití správné ID uživatele a heslo
2. Get-AzureRmSubscription - zobrazit všechna předplatná pro tento účet.
3. Select-AzureRmSubscription &lt;název předplatného&gt; – výběr správného předplatného. Použijte stejný jako ten, který použijete k vytvoření služby data factory na webu Azure portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problém: Nepovedlo se spustit expresní instalaci serveru Data Management Gateway z webu Azure portal
Expresní instalace pro Bránu pro správu dat vyžaduje Internet Explorer nebo webový prohlížeč kompatibilní s technologií Microsoft ClickOnce. Pokud se expresní instalace nezdaří, použijte jeden z následujících postupů:

* Použijte Internet Explorer nebo Microsoft ClickOnce kompatibilní webový prohlížeč.

    Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.

    Totéž proveďte pro Firefox (instalace doplňku). Na panelu nástrojů klikněte na tlačítko Otevřít nabídku (tři vodorovné čáry v pravém horním rohu), klikněte na Doplňky, dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
* Použití **ruční instalaci** odkazu je zobrazen ve stejném okně na portálu. Tento postup použijte ke stažení instalačního souboru a jeho spuštění. Když je instalace úspěšná, zobrazí dialogové okno Konfigurace brány správy dat. Zkopírujte **klíč** z okna portálu a použijte ho ve správci konfigurace k ruční registraci brány v příslušné službě.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problém: Nepovedlo se připojit k místnímu SQL serveru
Spuštění **Data Management Gateway Configuration Manager** na počítači brány a použít **Poradce při potížích s** kartu a otestujte připojení k SQL serveru z počítače brány. Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení/bránou související problémy.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problém: Vstupní řezy jsou napořád ve stavu Čekání.
Řezy mohou být v **čekání** stavu z různých důvodů. Jeden z běžných příčin je, že **externí** vlastnost není nastavena na **true**. By měly být označené všechny datové sady vytvořené mimo rozsah Azure Data Factory **externí** vlastnost. Tato vlastnost určuje, že data jsou externí a není podpořena žádnými kanály v data factory. Jakmile jsou data v příslušných úložištích dostupná, datové řezy se označí jako **připravené**.

Použití vlastnosti **external** si můžete prohlédnout v následujícím příkladu. Volitelně můžete zadat **externalData*** Pokud nastavíte external na hodnotu true.

Další podrobnosti o této vlastnosti najdete v článku [Datové sady](data-factory-create-datasets.md).

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

### <a name="problem-hybrid-copy-operation-fails"></a>Problém: Hybridní kopírování nezdaří
Naleznete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kroky pro řešení potíží s kopírování do a z místních dat pomocí brány správy dat úložiště.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problém: HDInsight na vyžádání zřizování se nezdaří
Při použití propojené služby typu HDInsightOnDemand, budete muset zadat vlastnost linkedServiceName, která odkazuje na služby Azure Blob Storage. Služba Data Factory využívá toto úložiště k ukládání protokolů a podpůrných souborů pro cluster HDInsight na vyžádání.  Někdy se zřizování clusteru HDInsight na vyžádání nezdaří s následující chybou:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Tato zpráva obvykle indikuje, že umístění účtu úložiště zadané vlastností linkedServiceName se neshoduje s umístěním datového centra, ve kterém dochází ke zřizování HDInsightu. Příklad: Pokud je služba data factory v oblasti západní USA a Azure storage je v oblasti východní USA, zřízení na vyžádání nezdaří v oblasti západní USA.

Navíc existuje ještě druhá vlastnost additionalLinkedServiceNames JSON, která umožňuje zadat další účty úložiště v HDInsightu na vyžádání. Tyto další propojené účty úložiště by měl být ve stejném umístění jako HDInsight cluster, nebo se postup nezdaří ke stejné chybě.

### <a name="problem-custom-net-activity-fails"></a>Problém: Vlastní aktivita technologie .NET selhala
V tématu [ladit kanálu pomocí vlastní aktivity](data-factory-use-custom-activities.md#troubleshoot-failures) podrobné pokyny.

## <a name="use-azure-portal-to-troubleshoot"></a>Řešení potíží pomocí webu Azure portal
### <a name="using-portal-blades"></a>Pomocí oken webu portal
Zobrazit [monitorování kanálu](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) kroky.

### <a name="using-monitor-and-manage-app"></a>Pomocí aplikace Monitorování a správa
Zobrazit [monitorování a Správa kanálů data factory pomocí monitorování a Správa aplikace](data-factory-monitor-manage-app.md) podrobnosti.

## <a name="use-azure-powershell-to-troubleshoot"></a>Řešení potíží s pomocí Azure Powershellu
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Řešení potíží s chybu pomocí Azure Powershellu
Zobrazit [objektu pro vytváření dat monitorování kanálů pomocí Azure Powershellu](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) podrobnosti.

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
