---
title: Přeučování a nasazení webové služby
titleSuffix: Azure Machine Learning Studio
description: Zjistěte, jak aktualizovat webovou službu, která používá nově trénovaný model strojového učení v Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: b57dd40c8610953563a3d5b8861e144d775b4eb7
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330507"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Přeučování a nasadit model strojového učení

Přeškolení je jeden způsob, jak zajistit, že modely strojového učení zůstat přesné a založená na nejdůležitější data dostupná. Tento článek ukazuje, jak přetrénujete a nasadit model strojového učení jako nové webové služby v sadě Studio. Pokud hledáte přeučování klasickou webovou službou [zobrazit tento článek.](retrain-classic-web-service.md)

Tento článek předpokládá, že již máte prediktivní webové služby nasazené. Pokud ještě nemáte prediktivní webové služby, [zjistěte, jak nasadit webovou službu Studio tady.](publish-a-machine-learning-web-service.md)

Vytvoříte provedením následujících kroků přeučování a nasazení novou webovou službu machine learning:

1. Nasazení **přetrénování webové služby**
1. Natrénovat nový model pomocí vaší **přetrénování webové služby**
1. Aktualizovat stávající **prediktivní experiment** použít nový model

## <a name="deploy-the-retraining-web-service"></a>Nasazení retraining webové služby

Retraining webová služba vám umožní přeučování modelu s novou sadu parametrů, jako jsou nová data a uložit pro pozdější použití. Když připojíte **výstup webové služby** k **trénování modelu**, výukového experimentu výstupy nový model můžete použít.

Pomocí následujících kroků nasaďte retraining webové služby:

1. Připojení **webové služby vstupní** modulu na datový vstup. Obvykle budete chtít zajistit, že vstupní data budou zpracovány stejně jako váš původním trénovací data.
1. Připojení **webové služby výstup** k výstupu modulu vaše **Train Model**.
1. Pokud máte **Evaluate Model** modul, můžete se připojit **webové služby výstup** modulu k vypsání výsledků vyhodnocení
1. Spusťte experiment.

    Po spuštění experimentu, výsledné pracovní postup by měl být podobně jako na následujícím obrázku:

    ![Výsledný pracovního postupu](media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nyní nasadíte výukového experimentu jako retraining webovou službu, jejichž výstupem jsou trénovaného modelu a výsledky vyhodnocení modelu.

1. V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby**
1. Vyberte **nasazení webové služby [nové]**. Na portálu Azure Machine Learning Web Services se otevře **nasadit webovou službu** stránky.
1. Zadejte název pro webovou službu a zvolte plán plateb.
1. Vyberte **Nasadit**.

## <a name="retrain-the-model"></a>Přeučování modelu

V tomto příkladu používáme C# k vytvoření aplikace přeučení. K provedení této úlohy můžete také použít Python nebo R ukázkový kód.

Pro volání rozhraní API pro přeučení postupujte následovně:

1. Vytvoření C# konzolovou aplikaci v sadě Visual Studio: **Nové** > **projektu** > **Visual C#**   >  **klasická plocha Windows**  >   **Aplikace konzoly (.NET Framework)**.
1. Přihlaste se k portálu webových služeb Machine Learning.
1. Klikněte na webové služby, pro kterou pracujete.
1. Klikněte na tlačítko **využívat**.
1. V dolní části **využívání** stránku, **ukázkový kód** klikněte na tlačítko **Batch**.
1. Zkopírujte ukázkový kód jazyka C# pro spuštění dávky a vložte ho do souboru Program.cs. Ujistěte se, že obor názvů zůstane beze změny.

Přidejte balíček NuGet Microsoft.AspNet.WebApi.Client, jak je uvedeno v komentářích. Chcete-li přidat odkaz na Microsoft.WindowsAzure.Storage.dll, může být potřeba nainstalovat [klientskou knihovnu služby Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Následující snímek obrazovky ukazuje **využívání** stránky na portálu Azure Machine Learning Web Services.

![Využívání stránky](media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizovat deklarace apikey

Vyhledejte **apikey** deklarace:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V **informace o základní spotřeby** část **využívání** stránky, vyhledejte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage

Ukázkový kód BES nahraje soubor z místního disku (například "C:\temp\CensusInput.csv") do služby Azure Storage, procesy a zapisuje výsledky zpět do služby Azure Storage.

1. Přihlášení k webu Azure Portal
1. V levém navigačním sloupci klikněte na tlačítko **další služby**, vyhledejte **účty úložiště**a vyberte ji.
1. V seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
1. V levém navigačním sloupci klikněte na tlačítko **přístupové klíče**.
1. Zkopírujte a uložte **primární přístupový klíč**.
1. V levém navigačním sloupci klikněte na tlačítko **kontejnery**.
1. Vybrat existující kontejner, nebo vytvořte novou a uložit název.

Vyhledejte *StorageAccountName*, *StorageAccountKey*, a *StorageContainerName* deklarace a aktualizujte hodnoty, které jste si uložili z portálu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Také musíte zajistit, že vstupní soubor je k dispozici na umístění, které zadáte v kódu.

### <a name="specify-the-output-location"></a>Určení umístění výstupu

Pokud zadáte umístění výstupu v datové žádost o rozšíření souboru, který je zadán v *RelativeLocation* musí být zadán jako `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Tady je příklad retraining výstupu:

![Přeškolení výstupu](media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Vyhodnoťte retraining výsledky

Při spuštění aplikace výstup obsahuje adresu URL a token sdílených přístupových podpisů, které jsou nezbytné pro přístup k výsledky hodnocení.

Zobrazí se výsledky výkonu retrained modelu tím, že zkombinujete *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu pro *output2* a vložení úplnou adresu URL do adresního řádku prohlížeče.

Zkontrolujte výsledky a určit, pokud nově trénovaného modelu provádí lepší než ten současný.

Uložit *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu.

## <a name="update-the-predictive-experiment"></a>Aktualizovat prediktivní experiment

### <a name="sign-in-to-azure-resource-manager"></a>Přihlaste se k Azure Resource Manageru

Nejprve se přihlaste ke svému účtu Azure z prostředí PowerShell pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny.

### <a name="get-the-web-service-definition-object"></a>Získejte objekt definice webové služby

Potom získejte objekt definice webové služby pomocí volání [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) rutiny.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

K určení názvu skupiny prostředků existující webové služby, spusťte rutinu Get-AzureRmMlWebService bez parametrů pro zobrazení webových služeb v rámci vašeho předplatného. Vyhledejte webovou službu a podívejte se na jeho ID webové služby. Název skupiny prostředků je čtvrtý prvek v poli ID bezprostředně po *resourceGroups* elementu. V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Můžete také určit název skupiny prostředků existující webové služby, přihlaste se k portálu Azure Machine Learning Web Services. Vyberte webovou službu. Název skupiny prostředků je pátého prvku pole Adresa URL webové služby, bezprostředně po *resourceGroups* elementu. V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Export objektu definice webové služby jako JSON.

Pokud chcete upravit definici trénovaného modelu, použití nově trénovaného modelu, musíte nejprve použít [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) rutiny a jejich export do souboru ve formátu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizovat odkaz na objekt blob ilearner

V prostředky, vyhledejte [trénovaný model], aktualizujte *identifikátor uri* hodnotu *locationInfo* uzlu s identifikátorem URI objektu ilearner blob. Identifikátor URI je generován kombinování *BaseLocation* a *RelativeLocation* z výstupu BES přetrénování volání.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>Import kódu JSON do objektu definice webové služby

Použít [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) rutiny pro převod upravený soubor JSON zpět do objektu definice webové služby, který vám pomůže aktualizovat predicative experimentu.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Aktualizovat webovou službu

Nakonec použijte [aktualizace AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) rutina pro aktualizaci prediktivní experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Další postup

Další informace o tom, jak spravovat webové služby nebo udržovat přehled o různých spuštění experimentů, naleznete v následujících článcích:

* [Prozkoumejte portálu webových služeb](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)
