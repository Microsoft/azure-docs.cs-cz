---
title: "Přeučování nové Azure Machine Learning webové služby pomocí prostředí PowerShell | Microsoft Docs"
description: "Zjistěte, jak programově přeučit modelu a aktualizovat webovou službu, která používá nově trénovaného modelu v Azure Machine Learning pomocí rutin prostředí PowerShell správu Machine Learning."
services: machine-learning
documentationcenter: 
author: aashishb
ms.author: aashishb
manager: hjerez
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: c260c15438afe6140fce92d8d3ce15b60867ec81
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Přeučování nového správce prostředků na základě webové službě pomocí rutin prostředí PowerShell správu Machine Learning
Pokud jste přeučování novou webovou službu, je aktualizovat definice prediktivní webové služby, chcete-li nový trained model.  

## <a name="prerequisites"></a>Požadavky
Musíte vytvořit výukový experiment a prediktivní experiment, jak je znázorněno v [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md). 

> [!IMPORTANT]
> Prediktivní experiment musí být nasazený jako počítač Azure Resource Manager (Nový) na základě učení webové služby. K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Další informace o nasazení webové služby, najdete v části [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

Tento proces vyžaduje, že jste nainstalovali rutiny Azure Machine Learning. Informace o instalaci rutin Machine Learning, najdete v článku [rutiny Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) odkaz na webu MSDN.

Kopírovat z retraining výstupu následující informace:

* BaseLocation
* RelativeLocation

Postup, kterým jsou:

1. Přihlaste se ke svému účtu Azure Resource Manager.
2. Získat definice webové služby
3. Exportovat jako JSON definice webové služby
4. Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
5. Import kódu JSON do definice webové služby
6. Aktualizovat webovou službu pomocí nové definice webové služby

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Přihlaste se k účtu Azure Resource Manager
Musíte se nejdřív přihlásit k účtu Azure z v prostředí PowerShell pomocí [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) rutiny.

## <a name="get-the-web-service-definition"></a>Získat definice webové služby
V dalším kroku získat voláním webové služby [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) rutiny. Definice webové služby je interní reprezentací pro cvičný model webové služby a není přímo změn. Ujistěte se, že jsou načítání definice webové služby prediktivní experiment a není experimentu školení.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Chcete-li zjistit název skupiny prostředků existující webovou službu, spusťte rutinu Get-AzureRmMlWebService bez parametrů pro zobrazení webové služby v rámci vašeho předplatného. Vyhledejte webovou službu a podívejte se na jeho identifikátorem webové služby. Název skupiny prostředků je čtvrtý element v ID, bezprostředně za *Skupinyprostředků* element. V následujícím příkladu je název skupiny prostředků výchozí-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Můžete taky určit název skupiny prostředků existující webové služby, přihlaste se k portálu Microsoft Azure Machine Learning webové služby. Vyberte webovou službu. Název skupiny prostředků je pátý element adresy URL webové služby, bezprostředně za *Skupinyprostředků* element. V následujícím příkladu je název skupiny prostředků výchozí-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportovat jako JSON definice webové služby
Upravit definici, abyste pro cvičný model používat nově Trained Model, musíte nejprve použít [Export AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) rutiny exportovat do souboru ve formátu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
V prostředky, vyhledejte [trained model], aktualizujte *identifikátor uri* hodnotu *locationInfo* uzlu s identifikátorem URI objektu ilearner blob. Identifikátor URI je generován kombinování *BaseLocation* a *RelativeLocation* z výstupu BES retraining volání. Tím se aktualizuje cesta odkazovat na nové naučeného modelu.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Import kódu JSON do definice webové služby
Je nutné použít [Import AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) rutiny převést změněný soubor JSON zpět do definice webové služby, který můžete použít k aktualizaci definice webové služby.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aktualizovat webovou službu pomocí nové definice webové služby
Nakonec použijte [aktualizace AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) rutiny aktualizovat definice webové služby.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Souhrn
Pomocí rutin prostředí PowerShell Machine Learning management, můžete aktualizovat pro cvičný model prediktivní povolení scénáře, jako webové služby:

* Pravidelné model retraining s nová data.
* Distribuce modelu pro zákazníky s cílem aby přeučování modelu s použitím svá vlastní data.

