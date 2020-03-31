---
title: Přeškolení webové služby
titleSuffix: ML Studio (classic) - Azure
description: Zjistěte, jak aktualizovat webovou službu tak, aby používala nově trénovaný model strojového učení v Azure Machine Learning Studio (klasika).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 218c1c98a2ed775ae86c1657156991879708cc7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217939"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Přeškolit a nasadit model strojového učení

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Rekvalifikace je jedním ze způsobů, jak zajistit, aby modely strojového učení zůstaly přesné a vycházely z nejrelevantnějších dostupných údajů. Tento článek ukazuje, jak přeškolit a nasadit model strojového učení jako novou webovou službu ve studiu (klasické). Pokud chcete přeškolit klasickou webovou službu, [podívejte se na tento článek s návody.](retrain-classic-web-service.md)

Tento článek předpokládá, že již máte prediktivní webové služby nasazeny. Pokud ještě nemáte prediktivní webovou službu, [přečtěte si, jak nasadit webovou službu Studio (klasické) zde.](deploy-a-machine-learning-web-service.md)

Podle těchto kroků můžete přeškolit a nasadit novou webovou službu strojového učení:

1. Nasazení **rekvalifikační webové služby**
1. Trénování nového modelu pomocí **rekvalifikační webové služby**
1. Aktualizace existujícího **prediktivního experimentu** za účelem použití nového modelu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Nasazení rekvalifikační webové služby

Retraining webová služba umožňuje přeškolit model s novou sadou parametrů, jako jsou nová data, a uložit ji na později. Když připojíte **výstup webové služby** k **modelu vlaku**, trénovací experiment vyžádá nový model, který můžete použít.

K nasazení rekvalifikační webové služby použijte následující kroky:

1. Připojte vstupní modul **webové služby** ke vstupu dat. Obvykle chcete zajistit, aby vaše vstupní data byla zpracována stejným způsobem jako původní trénovací data.
1. Připojte výstupní modul **webové služby** k výstupu **modelu vlaku**.
1. Pokud máte modul **Vyhodnotit model,** můžete připojit výstupní modul **webové služby** k výstupu výsledků vyhodnocení
1. Spusťte experiment.

    Po spuštění experimentu by měl být výsledný pracovní postup podobný následujícímu obrázku:

    ![Výsledný pracovní postup](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nyní nasadíte trénovací experiment jako rekvalifikační webovou službu, která vydělá trénovaný model a výsledky vyhodnocení modelu.

1. V dolní části plátna experimentu klikněte na **Nastavit webovou službu.**
1. Vyberte **možnost Nasadit webovou službu [New]**. Portál Azure Machine Learning Web Services se otevře na stránce **Nasadit webovou službu.**
1. Zadejte název webové služby a zvolte platební plán.
1. Vyberte **Nasadit**.

## <a name="retrain-the-model"></a>Přeškolit model

V tomto příkladu používáme C# k vytvoření retraining aplikace. K provedení tohoto úkolu můžete také použít ukázkový kód Pythonu nebo R.

Pomocí následujících kroků můžete volat rekvalifikační api:

1. Vytvořte aplikaci konzoly C# v sadě Visual Studio: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)**.
1. Přihlaste se k portálu webových služeb strojového učení.
1. Klikněte na webovou službu, se kterou pracujete.
1. Klepněte na **tlačítko Spotřebovat**.
1. V dolní části stránky **Spotřeba** klikněte v části **Ukázkový kód** na **dávky**.
1. Zkopírujte ukázkový kód Jazyka C# pro dávkové spuštění a vložte jej do souboru Program.cs. Ujistěte se, že obor názvů zůstane beze změny.

Přidejte balíček NuGet Microsoft.AspNet.WebApi.Client, jak je uvedeno v komentářích. Chcete-li přidat odkaz na soubor Microsoft.WindowsAzure.Storage.dll, bude pravděpodobně nutné nainstalovat [klientskou knihovnu pro služby Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Následující snímek obrazovky ukazuje **stránku Spotřebovávat** na portálu Azure Machine Learning Web Services.

![Stránka Spotřebovávají](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizovat apikey prohlášení

Vyhledejte **apikey** prohlášení:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V části **Základní informace o spotřebě** na stránce **Spotřebovávat** vyhledejte primární klíč a zkopírujte jej do **apikey deklarace.**

### <a name="update-the-azure-storage-information"></a>Aktualizace informací o úložišti Azure

Ukázkový kód BES nahraje soubor z místní jednotky (například "C:\temp\CensusInput.csv") do služby Azure Storage, zpracuje ho a výsledky zapíše zpět do služby Azure Storage.

1. Přihlášení k webu Azure Portal
1. V levém navigačním sloupci klikněte na **Další služby**, vyhledejte **účty úložiště**a vyberte je.
1. Ze seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
1. V levém navigačním sloupci klikněte na **přístupové klávesy**.
1. Zkopírujte a uložte **primární přístupový klíč**.
1. V levém navigačním sloupci klikněte na **Objekty blob .**
1. Vyberte existující kontejner nebo vytvořte nový a uložte název.

Vyhledejte deklarace *StorageAccountName*, *StorageAccountKey*a *StorageContainerName* a aktualizujte hodnoty, které jste uložili z portálu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Musíte také zajistit, že vstupní soubor je k dispozici v umístění, které zadáte v kódu.

### <a name="specify-the-output-location"></a>Určení výstupního umístění

Když zadáte výstupní umístění v požadavku datové části, musí být zadána přípona `ilearner`souboru, který je zadán v *relativelocation* jako .

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Zde je příklad retraining výstupu:

![Rekvalifikační výstup](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Vyhodnocení výsledků rekvalifikace

Při spuštění aplikace výstup obsahuje adresu URL a token sdílených přístupových podpisů, které jsou nezbytné pro přístup k výsledkům vyhodnocení.

Výsledky výkonu retrained modelu kombinací *BaseLocation*, *RelativeLocation*a *SasBlobToken* z výstupnívýsledky pro *výstup2* a vložení matné adresy URL do adresního řádku prohlížeče.

Zkontrolujte výsledky k určení, pokud nově trénovaný model funguje lépe než stávající.

Uložte *BaseLocation*, *RelativeLocation*a *SasBlobToken* z výsledků výstupu.

## <a name="update-the-predictive-experiment"></a>Aktualizace prediktivního experimentu

### <a name="sign-in-to-azure-resource-manager"></a>Přihlášení ke Správci prostředků Azure

Nejprve se přihlaste ke svému účtu Azure z prostředí PowerShell pomocí rutiny [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="get-the-web-service-definition-object"></a>Získání objektu Definice webové služby

Dále získejte objekt Definice webové služby voláním rutiny [Get-AzMlWebService.](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice)

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Chcete-li určit název skupiny prostředků existující webové služby, spusťte rutinu Get-AzMlWebService bez parametrů pro zobrazení webových služeb ve vašem předplatném. Vyhledejte webovou službu a prohlédněte si její ID webové služby. Název skupiny prostředků je čtvrtý prvek v ID, hned za *resourceGroups* element. V následujícím příkladu je název skupiny zdrojů Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Případně chcete-li určit název skupiny prostředků existující webové služby, přihlaste se k portálu Azure Machine Learning Web Services. Vyberte webovou službu. Název skupiny prostředků je pátým prvkem adresy URL webové služby, hned za elementem *resourceGroups.* V následujícím příkladu je název skupiny zdrojů Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Export objektu Definice webové služby jako JSON

Chcete-li upravit definici trénovaného modelu tak, aby používal nově trénovaný model, musíte nejprve použít rutinu [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) k jeho exportu do souboru ve formátu JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizace odkazu na objekt blob ilearner

V datových zdrojů vyhledejte [trénovaný model], aktualizujte hodnotu *uri* v uzlu *locationInfo* pomocí identifikátoru URI objektu blob ilearner. Identifikátor URI je generován kombinací *BaseLocation* a *RelativeLocation* z výstupu volání retraining BES.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Import jsonu do objektu Definice webové služby

Pomocí rutiny [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) převeďte upravený soubor JSON zpět na objekt Definice webové služby, který můžete použít k aktualizaci prediktivního experimentu.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Aktualizace webové služby

Nakonec použijte rutinu [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) k aktualizaci prediktivního experimentu.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Další kroky

Další informace o správě webových služeb nebo sledování spuštění více experimentů naleznete v následujících článcích:

* [Prozkoumání portálu webových služeb](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)
