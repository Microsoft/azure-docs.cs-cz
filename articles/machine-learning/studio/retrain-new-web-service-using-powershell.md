---
title: Přeučování webové služby nové Azure Machine Learning pomocí Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak programově přeučit modelu a aktualizovat webovou službu, která používá nově trénovaného modelu ve službě Azure Machine Learning pomocí rutin prostředí PowerShell pro Machine Learning Management.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 77ae0c9f8af82e238b58945d1427afd6a01173e3
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263025"
---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Přeučování webové služby založené na nové Resource Manageru pomocí rutin prostředí PowerShell pro Machine Learning Management
Když jste přeučování nové webové služby, aktualizujte definice prediktivní webové služby tak, aby odkazovaly na nový trénovaný model.

## <a name="prerequisites"></a>Požadavky
Musíte vytvořit výukový experiment a prediktivní experiment, jak je znázorněno v [modelů Machine Learning Přeučování](retrain-models-programmatically.md).

> [!IMPORTANT]
> Prediktivní experiment musí být nasazený jako (nové) založené na Azure Resource Manageru webovou službu machine learning.
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).

Další informace o nasazování webových služeb najdete v tématu [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

Tento proces vyžaduje, že jste nainstalovali rutiny Azure Machine Learning. Informace o instalaci rutin Machine Learning, najdete v článku [rutiny pro Azure Machine Learning](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/) odkaz na webové stránce MSDN.

Z výstupu retraining zkopírovány následující informace:

* BaseLocation
* RelativeLocation

Kroky, které jsou:

1. Přihlaste se ke svému účtu Azure Resource Manageru.
2. Získat definice webové služby
3. Export definice webové služby jako JSON.
4. Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
5. Import kódu JSON do definice webové služby
6. Aktualizovat webovou službu pomocí definice nové webové služby

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Přihlaste se ke svému účtu Azure Resource Manageru
Musíte se nejprve přihlásit ke svému účtu Azure z v rámci pomocí prostředí PowerShell [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny.

## <a name="get-the-web-service-definition"></a>Získat definice webové služby
Potom získejte webové službě voláním [Get-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservice) rutiny. Definice webové služby je interní reprezentace trénovaného modelu webové služby a není možné přímo upravit. Ujistěte se, že jsou načítání definice webové služby pro prediktivní experiment a ne výukového experimentu.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

K určení názvu skupiny prostředků existující webové služby, spusťte rutinu Get-AzureRmMlWebService bez parametrů pro zobrazení webových služeb v rámci vašeho předplatného. Vyhledejte webovou službu a podívejte se na jeho ID webové služby. Název skupiny prostředků je čtvrtý prvek v poli ID bezprostředně po *resourceGroups* elementu. V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Můžete také určit název skupiny prostředků existující webové služby, přihlaste se k portálu Microsoft Azure Machine Learning Web Services. Vyberte webovou službu. Název skupiny prostředků je pátého prvku pole Adresa URL webové služby, bezprostředně po *resourceGroups* elementu. V následujícím příkladu je název skupiny prostředků výchozí. MachineLearning SouthCentralUS.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Export definice webové služby jako JSON.
Upravit definici trénovaného modelu na použití nově Trénovaného modelu, musíte nejprve použít [Export AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/export-azurermmlwebservice) rutiny a jejich export do souboru ve formátu JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Aktualizujte odkaz na objekt blob ilearner v kódu JSON.
V prostředky, vyhledejte [trénovaný model], aktualizujte *identifikátor uri* hodnotu *locationInfo* uzlu s identifikátorem URI objektu ilearner blob. Identifikátor URI je generován kombinování *BaseLocation* a *RelativeLocation* z výstupu BES přetrénování volání. Tím se aktualizuje cestu tak, aby odkazovaly na nový trénovaný model.

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
Je nutné použít [Import AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/import-azurermmlwebservice) rutiny pro převedení upravený soubor JSON zpět na definice webové služby, který můžete použít k aktualizaci definice webové služby.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Aktualizovat webovou službu pomocí definice nové webové služby
Nakonec použijte [aktualizace AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/update-azurermmlwebservice) rutina pro aktualizaci definice webové služby.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Souhrn
Pomocí rutin správy Machine Learning PowerShell, můžete aktualizovat trénovaného modelu umožňuje scénáře, jako prediktivní webové služby:

* Pravidelné model přetrénování s novými daty.
* Distribuce modelu pro zákazníky s cílem aby programovém přeučení modelů pomocí svá vlastní data.

