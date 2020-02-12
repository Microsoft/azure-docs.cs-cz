---
title: Přeučení webové služby
titleSuffix: ML Studio (classic) - Azure
description: Naučte se aktualizovat webovou službu tak, aby používala nově vyškolený model strojového učení v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 867d104b58980679dc815238fef14050e7d9e8c7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152852"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Přeučení a nasazení modelu strojového učení

Rekurze je jedním ze způsobů, jak zajistit, aby modely strojového učení byly přesné a založené na nejdůležitějších dostupných datech. Tento článek ukazuje, jak převádět a nasazovat model strojového učení jako novou webovou službu v nástroji Studio (Classic). Pokud chcete přeškolit klasickou webovou službu, Projděte si [článek s postupem.](retrain-classic-web-service.md)

V tomto článku se předpokládá, že už máte nasazenou prediktivní webovou službu. Pokud ještě nemáte prediktivní webovou službu, [Přečtěte si, jak sem nasadit webovou službu studia (Classic).](deploy-a-machine-learning-web-service.md)

Pomocí těchto kroků můžete znovu naučit a nasazovat novou webovou službu Machine Learning:

1. Nasazení **webové služby retraining**
1. Výuka nového modelu pomocí **webové služby přeškolení**
1. Aktualizace existujícího **prediktivního experimentu** pro použití nového modelu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Nasazení retraining webové služby

Webová služba retraining umožňuje přeškolovat model s novou sadou parametrů, jako jsou nová data, a uložit ho pro pozdější účely. Když připojíte **výstup webové služby** k **modelu vlaků**, zkušební experiment vyprodukuje nový model, který můžete použít.

K nasazení webové služby retraining použijte následující postup:

1. Připojte modul **vstupu webové služby** k vašemu datovému vstupu. Obvykle chcete zajistit, aby se vstupní data zpracovala stejným způsobem jako původní data školení.
1. Připojte **výstupní modul webové služby** k výstupu vašeho **modelu výuky**.
1. Pokud máte modul **vyhodnocení modelu** , můžete k výstupu výsledků hodnocení připojit modul **výstupu webové služby** .
1. Spusťte experiment.

    Po spuštění experimentu by výsledný pracovní postup měl vypadat podobně jako na následujícím obrázku:

    ![Výsledný pracovní postup](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nyní nasadíte experiment školení jako webovou službu retraining, která vytvoří výstup školicího modelu a výsledků hodnocení modelu.

1. V dolní části plátna experimentu klikněte na **nastavit webovou službu** .
1. Vyberte **nasadit webovou službu [New]** . Portál Azure Machine Learning webové služby se otevře na stránce **nasadit webovou službu** .
1. Zadejte název webové služby a vyberte platební plán.
1. Vyberte **Nasadit**.

## <a name="retrain-the-model"></a>Přeučení modelu

V tomto příkladu používáme C# k vytvoření opětovného školení aplikace. K provedení této úlohy můžete použít také vzorový kód Python nebo R.

K volání rozhraní API pro přeškolení použijte následující postup:

1. C# Vytvoření konzolové aplikace v aplikaci Visual Studio: **Nový** > **projekt** > **Visual C#**  > **Windows Classic Desktop** > **Konzolová aplikace (.NET Framework)** .
1. Přihlaste se k portálu Machine Learning Web Services.
1. Klikněte na webovou službu, se kterou právě pracujete.
1. Klikněte na možnost **spotřebovat**.
1. V dolní části stránky **využívání** klikněte v části **vzorový kód** na **Batch**.
1. Zkopírujte vzorový C# kód pro spuštění dávky a vložte ho do souboru program.cs. Ujistěte se, že obor názvů zůstane nedotčený.

Přidejte balíček NuGet Microsoft. AspNet. WebApi. Client, jak je uvedeno v komentářích. Chcete-li přidat odkaz na soubor Microsoft. WindowsAzure. Storage. dll, je možné, že bude nutné nainstalovat [klientskou knihovnu pro Azure Storage Services](https://www.nuget.org/packages/WindowsAzure.Storage).

Na následujícím snímku obrazovky vidíte stránku **spotřebovat** na portálu Azure Machine Learning Web Services.

![Vybírající stránku](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualizovat deklarace apikey

Vyhledejte deklaraci **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V části **informace o základní spotřebě** **stránky využívání** Najděte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage

Vzorový kód BES nahraje soubor z místního disku (například "C:\temp\CensusInput.csv") na Azure Storage, zpracuje ho a zapíše výsledky zpět do Azure Storage.

1. Přihlášení k webu Azure Portal
1. V levém navigačním sloupci klikněte na **Další služby**, vyhledejte **účty úložiště**a vyberte je.
1. V seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
1. V levém navigačním sloupci klikněte na **přístupové klíče**.
1. Zkopírujte a uložte **Primární přístupový klíč**.
1. V levém navigačním sloupci klikněte na **objekty blob**.
1. Vyberte existující kontejner, nebo vytvořte nový a uložte název.

Vyhledejte deklarace *StorageAccountName*, *StorageAccountKey*a *StorageContainerName* a aktualizujte hodnoty, které jste uložili na portálu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Také je nutné zajistit, aby byl vstupní soubor k dispozici v umístění, které zadáte v kódu.

### <a name="specify-the-output-location"></a>Určení umístění výstupu

Pokud zadáte umístění výstupu v datové části požadavku, Přípona souboru určeného v *RelativeLocation* musí být zadána jako `ilearner`.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Tady je příklad přeškolení pro výstup:

![Přeškolení výstupu](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Vyhodnocení výsledků rekurze

Při spuštění aplikace obsahuje výstup token adresy URL a sdíleného přístupového podpisu, který je nezbytný pro přístup k výsledkům vyhodnocení.

Výsledky předaného modelu můžete zobrazit kombinací *BaseLocation*, *RelativeLocation*a *SasBlobToken* z výstupních výsledků pro *output2* a vložením celé adresy URL do adresního řádku prohlížeče.

Zkontrolujte výsledky a zjistěte, jestli je nově vyškolený model vyšší než stávající.

Z výstupních výsledků uložte *BaseLocation*, *RelativeLocation*a *SasBlobToken* .

## <a name="update-the-predictive-experiment"></a>Aktualizace prediktivního experimentu

### <a name="sign-in-to-azure-resource-manager"></a>Přihlášení k Azure Resource Manager

Nejdřív se přihlaste ke svému účtu Azure v prostředí PowerShell pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Získat objekt definice webové služby

Dále získejte objekt definice webové služby voláním rutiny [Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) .

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pokud chcete zjistit název skupiny prostředků existující webové služby, spusťte rutinu Get-AzMlWebService bez jakýchkoli parametrů, ve které se budou zobrazovat webové služby v předplatném. Vyhledejte webovou službu a podívejte se na jeho ID webové služby. Název skupiny prostředků je čtvrtý prvek v ID, a to hned za elementem *resourceGroups* . V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Případně chcete-li zjistit název skupiny prostředků existující webové služby, přihlaste se k portálu Azure Machine Learning Web Services. Vyberte webovou službu. Název skupiny prostředků je pátý prvek adresy URL webové služby hned za elementem *resourceGroups* . V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Exportovat objekt definice webové služby jako JSON

Chcete-li upravit definici vyškolených modelů pro použití nově vyškolený model, je nutné nejprve použít rutinu [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) k jeho exportu do souboru formátu JSON.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizace odkazu na ilearner BLOB

V části assets (prostředky) vyhledejte [trained model], aktualizujte hodnotu *identifikátoru URI* v uzlu *LocationInfo* s identifikátorem URI objektu BLOB ilearner. Identifikátor URI je vygenerován kombinací *BaseLocation* a *RelativeLocation* z výstupu volání rekurze BES.

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>Import JSON do objektu definice webové služby

Pomocí rutiny [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) převeďte upravený soubor JSON zpátky do objektu definice webové služby, který můžete použít k aktualizaci experimentu predicative.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Aktualizovat webovou službu

Nakonec pomocí rutiny [Update-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) aktualizujte prediktivní experiment.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak spravovat webové služby nebo sledovat více experimentálních běhů, najdete v následujících článcích:

* [Prozkoumejte portál Web Services](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)
