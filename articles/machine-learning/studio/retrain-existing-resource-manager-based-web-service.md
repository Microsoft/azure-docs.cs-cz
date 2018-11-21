---
title: Přeučování existující prediktivní webové služby | Dokumentace Microsoftu
description: Zjistěte, jak model přetrénujete a aktualizovat webovou službu, která používá nově trénovaného modelu ve službě Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: 9fb3ccee9a50fa7758547fffe2573104679aef7e
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263447"
---
# <a name="retrain-an-existing-predictive-web-service"></a>Přeučování existující prediktivní webové služby
Tento dokument popisuje retraining proces pro následující scénáře:

* Máte výukového experimentu a prediktivní experiment, který jste nasadili jako zprovozněné webové službě.
* Máte nová data, který má prediktivní webové služby pro použití k provádění jeho vyhodnocení.

> [!NOTE]
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).

Počínaje existující webovou službu a experimenty, musíte postupovat podle následujících kroků:

1. Aktualizace modelu.
   1. Upravte vašeho výukového experimentu umožňující webové služby vstupy a výstupy.
   2. Školení experiment nasaďte jako webovou službu přeučení.
   3. Přeučování modelu pomocí výukového experimentu služby Batch Execution Service (BES).
2. Pomocí rutin Azure Machine Learning PowerShell prediktivní experiment aktualizovat.
   1. Přihlaste se ke svému účtu Azure Resource Manageru.
   2. Získáte definice webové služby.
   3. Exportujte definice webové služby jako dokumenty JSON.
   4. Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
   5. Importujte kódu JSON do definice webové služby.
   6. Aktualizujte webovou službu pomocí nové definice webové služby.

## <a name="deploy-the-training-experiment"></a>Nasazení výukového experimentu
K nasazení výukového experimentu jako retraining webovou službu, musíte přidat webovou službu vstupy a výstupy do modelu. Propojením *webové služby výstup* modulů na experiment *[Train Model] [ train-model]* modulu, povolte výukového experimentu do Vytvoření nového trénovaného modelu, který vám pomůže v prediktivní experiment. Pokud máte *Evaluate Model* modul, můžete také připojit výstup webové služby k získání požadovaných výsledků vyhodnocení jako výstup.

Aktualizace vašeho výukového experimentu:

1. Připojení *webové služby vstupní* modulu na datový vstup (například *vyčištění chybějících dat* modul). Obvykle budete chtít zajistit, že vstupní data budou zpracovány stejně jako váš původním trénovací data.
2. Připojení *webové služby výstup* k výstupu modulu vaše *Train Model* modulu.
3. Pokud máte *Evaluate Model* modulu a chcete výstup výsledků vyhodnocení, připojení *výstup webové služby* k výstupu modulu vaše *vyhodnocení modelu* modulu.

Spusťte experiment.

V dalším kroku je nutné nasadit výukového experimentu jako webovou službu, která vytváří trénovaného modelu a výsledky vyhodnocení modelu.

V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby**a pak vyberte **nasazení [nové] webová služba**. Na portálu Azure Machine Learning Web Services se otevře **nasadit webovou službu** stránky. Zadejte název pro webovou službu, zvolte plán plateb a pak klikněte na tlačítko **nasadit**. Metoda spuštění služby Batch můžete použít pouze pro vytvoření trénované modely.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Přeučování modelů s novými daty s využitím BES
V tomto příkladu používáme C# k vytvoření aplikace přeučení. K provedení této úlohy můžete také použít Python nebo R ukázkový kód.

Pro volání rozhraní API pro přeučení:

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio: **nový** > **projektu** > **Visual C#** > **Windows Klasický desktopový** > **aplikace konzoly (.NET Framework)**.
2. Přihlaste se k portálu webových služeb Machine Learning.
3. Klikněte na webové služby, pro kterou pracujete.
4. Klikněte na tlačítko **využívat**.
5. V dolní části **využívání** stránku, **ukázkový kód** klikněte na tlačítko **Batch**.
6. Zkopírujte ukázkový kód jazyka C# pro spuštění dávky a vložte ho do souboru Program.cs. Ujistěte se, že obor názvů zůstane beze změny.

Přidejte balíček NuGet Microsoft.AspNet.WebApi.Client, jak je uvedeno v komentářích. Přidat odkaz na Microsoft.WindowsAzure.Storage.dll, nejprve potřebujete nainstalovat [klientskou knihovnu služby Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage).

Následující snímek obrazovky ukazuje **využívání** stránky na portálu Azure Machine Learning Web Services.

![Využívání stránky][1]

### <a name="update-the-apikey-declaration"></a>Aktualizovat deklarace apikey
Vyhledejte **apikey** deklarace:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V **informace o základní spotřeby** část **využívání** stránky, vyhledejte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage
Ukázkový kód BES nahraje soubor z místního disku (například "C:\temp\CensusIpnput.csv") do služby Azure Storage, procesy a zapisuje výsledky zpět do služby Azure Storage.

Po spuštění experimentu, výsledné pracovní postup by měl vypadat přibližně takto:

![Výsledný pracovní postup po spuštění][4]

1. Přihlaste se k portálu Azure.
2. V levém navigačním sloupci klikněte na tlačítko **další služby**, vyhledejte **účty úložiště**a vyberte ji.
3. V seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
4. V levém navigačním sloupci klikněte na tlačítko **přístupové klíče**.
5. Zkopírujte a uložte **primární přístupový klíč**.
6. V levém navigačním sloupci klikněte na tlačítko **kontejnery**.
7. Vybrat existující kontejner, nebo vytvořte novou a uložit název.

Vyhledejte *StorageAccountName*, *StorageAccountKey*, a *StorageContainerName* deklarace a aktualizujte hodnoty, které jste si uložili z portálu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Také musíte zajistit, že vstupní soubor je k dispozici na umístění, které zadáte v kódu.

### <a name="specify-the-output-location"></a>Určení umístění výstupu
Pokud zadáte umístění výstupu v datové žádost o rozšíření souboru, který je zadán v *RelativeLocation* musí být zadán jako `ilearner`. Prohlédněte si následující příklad:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Následuje příklad retraining výstupu:

![Přeškolení výstupu][6]

## <a name="evaluate-the-retraining-results"></a>Vyhodnoťte retraining výsledky
Při spuštění aplikace výstup obsahuje adresu URL a token sdílených přístupových podpisů, které jsou nezbytné pro přístup k výsledky hodnocení.

Zobrazí se výsledky výkonu retrained modelu tím, že zkombinujete *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu pro *output2* (jak je znázorněno na předchozím obrázku retraining výstupní) a vložení úplnou adresu URL do adresního řádku prohlížeče.

Zkontrolujte výsledky a určit, zda se nově trénovaného modelu dostatečně dobře funguje nahradit stávající.

Kopírovat *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu.

## <a name="retrain-the-web-service"></a>Přeučování webové služby
Když jste přeučování nové webové služby, aktualizujte definice prediktivní webové služby tak, aby odkazovaly na nový trénovaný model. Definice webové služby je interní reprezentace trénovaného modelu webové služby a není možné přímo upravit. Ujistěte se, že jsou načítání definice webové služby pro prediktivní experiment a ne výukového experimentu.

## <a name="sign-in-to-azure-resource-manager"></a>Přihlaste se k Azure Resource Manageru
Musíte nejdřív se přihlásit ke svému účtu Azure z prostředí PowerShell pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny.

## <a name="get-the-web-service-definition-object"></a>Získejte objekt definice webové služby
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


## <a name="export-the-web-service-definition-object-as-json"></a>Export objektu definice webové služby jako JSON.
Pokud chcete upravit definici trénovaného modelu, použití nově trénovaného modelu, musíte nejprve použít [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) rutiny a jejich export do souboru ve formátu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizovat odkaz na objekt blob ilearner
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Import kódu JSON do objektu definice webové služby
Je nutné použít [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) rutiny pro převod upravený soubor JSON zpět do objektu definice webové služby, který vám pomůže aktualizovat predicative experimentu.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aktualizovat webovou službu
Nakonec použijte [aktualizace AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) rutina pro aktualizaci prediktivní experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
