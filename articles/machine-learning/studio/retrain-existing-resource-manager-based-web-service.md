---
title: "Přeučování existující prediktivní webovou službu | Microsoft Docs"
description: "Zjistěte, jak aktualizovat webovou službu, která používá nově trénovaného modelu v Azure Machine Learning a přeučování modelu."
services: machine-learning
documentationcenter: 
author: aashishb
ms.author: aashishb
manager: hjerez
editor: 
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: 040b52ae41dc83f103762764b6f2ccea23d36983
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="retrain-an-existing-predictive-web-service"></a>Přeučování existující prediktivní webovou službu
Tento dokument popisuje proces retraining pro následující scénář:

* Máte výukový experiment a prediktivní experiment, který jste nasadili jako operationalized webové služby.
* Máte nová data, že chcete, aby vaši prediktivní webovou službu používat k provádění jeho vyhodnocování.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Počínaje existující webovou službu a experimentů, je třeba postupovat podle těchto kroků:

1. Aktualizace modelu.
   1. Upravte experimentu školení pro webové služby vstupy a výstupy.
   2. Výukový experiment nasaďte jako retraining webovou službu.
   3. Pomocí výukový experiment dávky spuštění služby (BES) přeučování modelu.
2. Rutiny Azure Machine Learning PowerShell použijte k aktualizaci prediktivní experiment.
   1. Přihlaste se ke svému účtu Azure Resource Manager.
   2. Získáte definice webové služby.
   3. Exportujte definice webové služby jako JSON.
   4. Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
   5. Importujte JSON do definice webové služby.
   6. Aktualizujte webovou službu pomocí nové definice webové služby.

## <a name="deploy-the-training-experiment"></a>Nasazení výukový experiment
Pokud chcete nasadit výukový experiment jako retraining webové služby, musíte přidat webové služby vstupy a výstupy do modelu. Připojením *výstup webové služby* modulu experimentu  *[Train Model] [ train-model]*  modulu, povolte výukový experiment k vytvoření nové trained model, který můžete použít v prediktivní experiment. Pokud máte *Evaluate Model* modul, můžete taky přiložit výstup webové služby se získat výsledky hodnocení jako výstup.

Aktualizace výukový experiment:

1. Připojit *webové služby vstupní* modulu datového vstupu (například *vyčištění chybějících dat* modulu). Obvykle budete chtít zajistit, že se vstupní data zpracovány stejným způsobem jako původní data školení.
2. Připojit *výstup webové služby* modulu výstup vaše *Train Model* modulu.
3. Pokud máte *Evaluate Model* modulu a chcete výstup výsledky hodnocení, připojit *výstup webové služby* modulu výstup vaše *Evaluate Model* modulu.

Spuštění experimentu.

Dále je nutné nasadit výukový experiment jako webová služba, která vytváří modulu trained model a výsledky vyhodnocení modelu.  

V dolní části plátna experimentu, klikněte na tlačítko **nastavit webové služby**a potom vyberte **nasazení [nové] webové služby**. Webové služby Azure Machine Learning portál se otevře **nasazení webové služby** stránky. Zadejte název pro webovou službu, zvolte plán platby a pak klikněte na tlačítko **nasadit**. Metoda Batch Execution můžete použít pouze pro vytváření trénované modely.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Přeučování model s nová data pomocí BES
V tomto příkladu používáme C# k vytvoření retraining aplikace. Ukázka kódu Pythonu nebo R můžete použít také k provedení této úlohy.

K volání rozhraní retraining API:

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio: **nový** > **projektu** > **Visual C#** > **Windows Classic Desktop** > **konzolovou aplikaci (rozhraní .NET Framework)**.
2. Přihlaste se k portálu webové služby Machine Learning.
3. Klikněte na tlačítko webové služby, která kterými pracujete.
4. Klikněte na tlačítko **využívat**.
5. V dolní části **spotřebě** stránky v **ukázkový kód** klikněte na tlačítko **Batch**.
6. Ukázka kódu C# pro spuštění dávky zkopírujte a vložte jej do souboru Program.cs. Ujistěte se, že obor názvů zůstává beze změn.

Přidejte balíček NuGet Microsoft.AspNet.WebApi.Client, jak je uvedeno v komentářích. Pokud chcete přidat odkaz na Microsoft.WindowsAzure.Storage.dll, může být nejdřív musíte nainstalovat [Klientská knihovna pro úložiště Azure services](https://www.nuget.org/packages/WindowsAzure.Storage).

Následující snímek obrazovky ukazuje **spotřebě** na portálu Azure Machine Learning webové služby.

![Využívat stránky][1]

### <a name="update-the-apikey-declaration"></a>Aktualizace apikey deklarace
Vyhledejte **apikey** deklarace:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

V **informace o základní spotřeby** části **spotřebě** stránky, vyhledejte primární klíč a zkopírujte ho do **apikey** deklarace.

### <a name="update-the-azure-storage-information"></a>Aktualizovat informace o Azure Storage
Ukázkový kód BES se uloží soubor z místního disku (například "C:\temp\CensusIpnput.csv") do služby Azure Storage, procesy a zapíše výsledky zpět do služby Azure Storage.  

Po spuštění experimentu, výsledná pracovního postupu by měl vypadat přibližně takto:

![Po spuštění výsledného pracovního postupu][4]

1. Přihlaste se k portálu Azure.
2. Ve sloupci levém navigačním panelu klikněte na tlačítko **další služby**, vyhledejte **účty úložiště**a vyberte ho.
3. Ze seznamu účtů úložiště vyberte jeden pro uložení retrained modelu.
4. Ve sloupci levém navigačním panelu klikněte na tlačítko **přístupové klíče**.
5. Zkopírujte a uložte **primární přístupový klíč**.
6. Ve sloupci levém navigačním panelu klikněte na tlačítko **kontejnery**.
7. Vyberte existující kontejner, nebo vytvořte novou a uložit název.

Vyhledejte *StorageAccountName*, *StorageAccountKey*, a *StorageContainerName* deklarace a aktualizujte hodnoty, které jste uložili z portálu.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Je také nutné zajistit, že vstupní soubor je k dispozici v umístění, které zadáte v kódu.

### <a name="specify-the-output-location"></a>Zadejte umístění výstupu
Pokud zadáte umístění výstupu v žádosti o datové části, přípona souboru, který je uveden v *RelativeLocation* musí být zadány jako `ilearner`. Podívejte se na následující příklad:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Následuje příklad výstupu retraining:

![Retraining výstup][6]

## <a name="evaluate-the-retraining-results"></a>Vyhodnoťte retraining výsledky
Při spuštění aplikace, zahrnuje adresu URL a token podpisů sdíleného přístupu, který jsou potřebné pro přístup k výsledky hodnocení.

Zobrazí výsledky retrained modelu tím, že zkombinujete *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstup pro *output2* (jak je znázorněno na předchozím obrázku výstup retraining) a vkládání úplnou adresu URL do panelu Adresa prohlížeče.  

Podívejte se na výsledky, abyste zjistili, jestli pro nově cvičný model provádí dostatečně dobře nahradit stávající.

Kopírování *BaseLocation*, *RelativeLocation*, a *SasBlobToken* z výsledků výstupu.

## <a name="retrain-the-web-service"></a>Přeučování webové služby
Pokud jste přeučování novou webovou službu, je aktualizovat definice prediktivní webové služby, chcete-li nový trained model. Definice webové služby je interní reprezentací pro cvičný model webové služby a není přímo změn. Ujistěte se, že jsou načítání definice webové služby prediktivní experiment a není experimentu školení.

## <a name="sign-in-to-azure-resource-manager"></a>Přihlaste se k Azure Resource Manager
Musíte nejprve se přihlásit k účtu Azure z prostředí PowerShell pomocí [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) rutiny.

## <a name="get-the-web-service-definition-object"></a>Získání objektu definice webové služby
V dalším kroku získání objektu definice webové služby pomocí volání [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) rutiny.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Chcete-li zjistit název skupiny prostředků existující webovou službu, spusťte rutinu Get-AzureRmMlWebService bez parametrů pro zobrazení webové služby v rámci vašeho předplatného. Vyhledejte webovou službu a podívejte se na jeho identifikátorem webové služby. Název skupiny prostředků je čtvrtý element v ID, bezprostředně za *Skupinyprostředků* element. V následujícím příkladu je název skupiny prostředků výchozí-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternativně k určení názvu skupiny prostředků existující webovou službu, přihlaste se k portálu Azure Machine Learning webové služby. Vyberte webovou službu. Název skupiny prostředků je pátý element adresy URL webové služby, bezprostředně za *Skupinyprostředků* element. V následujícím příkladu je název skupiny prostředků výchozí-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Export objektu definice webové služby jako JSON
Chcete-li upravit definici pro cvičný model použít nově trained model, musíte nejprve použít [Export AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) k exportu do souboru formátu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualizovat odkaz na objekt blob ilearner
V prostředky, vyhledejte [trained model], aktualizujte *identifikátor uri* hodnotu *locationInfo* uzlu s identifikátorem URI objektu ilearner blob. Identifikátor URI je generován kombinování *BaseLocation* a *RelativeLocation* z výstupu BES retraining volání.

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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importujte JSON do objektu definice webové služby
Je nutné použít [Import AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) rutiny převést změněný soubor JSON zpět do objektu definice webové služby, který můžete použít k aktualizaci predicative experimentu.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Aktualizovat webovou službu
Nakonec použijte [aktualizace AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) rutiny aktualizovat prediktivní experiment.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
