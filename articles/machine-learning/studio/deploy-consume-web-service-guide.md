---
title: "Azure Machine Learning webové služby: Nasazení a používání | Microsoft Docs"
description: "Prostředky pro nasazení a využívají webové služby."
services: machine-learning
documentationcenter: 
author: aashishb
manager: hjerez
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 7eecfe4a80499fbba0533f0d410dd920db61e62c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Webové služby Azure Machine Learning: Nasazení a využití
Azure Machine Learning můžete použít k nasazení pracovní postupy a modely jako webové služby machine learning. Tyto webové služby pak lze volat modely machine learningu z aplikací přes Internet udělat předpovědi v reálném čase nebo v dávkovém režimu. Protože webových služeb jsou dosáhl standardu RESTful, můžete je volat z různé programovací jazyky a platformy, jako je například rozhraní .NET a Javu a z aplikace, jako je například aplikace Excel.

Další části obsahují odkazy na postupy, kód a dokumentaci, která vám pomůžou začít.

## <a name="deploy-a-web-service"></a>Nasazení webové služby

### <a name="with-azure-machine-learning-studio"></a>S Azure Machine Learning Studio
Machine Learning Studio a portálu Microsoft Azure Machine Learning webové služby můžete nasazovat a spravovat bez nutnosti psaní kódu webové služby.

Následující odkazy obsahují obecné informace o tom, jak nasadit novou webovou službu:

* Přehled o tom, jak nasadit novou webovou službu, která je založená na Azure Resource Manager, najdete v tématu [nasadit novou webovou službu](publish-a-machine-learning-web-service.md).
* Návod, o tom, jak nasadit webovou službu, naleznete v [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Úplné návod o tom, jak vytvořit a nasadit webové služby najdete v tématu [návod krok 1: vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md).
* Konkrétní příklady, které nasazení webové služby najdete v tématu:

  * [Návod krok 5: Nasazení webové služby Azure Machine Learning](walkthrough-5-publish-web-service.md)
  * [Postup nasazení webové služby do několika oblastí](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Pomocí zprostředkovatele prostředků služby webové rozhraní API (rozhraní API Správce Azure Resource Manager)
Zprostředkovatel prostředků Azure Machine Learning pro webové služby umožňuje nasazení a správy webové služby pomocí volání rozhraní REST API. Další podrobnosti najdete v tématu [Machine Learning webové služby (REST)](/rest/api/machinelearning/index) odkaz.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Zprostředkovatel prostředků Azure Machine Learning pro webové služby umožňuje nasazení a správy webové služby pomocí rutin prostředí PowerShell.

Pokud chcete používat rutiny, musí prvním přihlášení k účtu Azure z prostředí PowerShell pomocí [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) rutiny. Pokud jste obeznámeni s jak volat příkazy prostředí PowerShell, které jsou založeny na najdete v části správce prostředků, [použití Azure Powershellu s Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

Chcete-li exportovat prediktivní experiment, použijte [ukázkový kód](https://github.com/ritwik20/AzureML-WebServices). Jakmile vytvoříte soubor .exe z kódu, můžete zadat:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Spuštění aplikace vytvoří šablonu JSON webové služby. Abyste mohli použít šablonu nasazení webové služby, je nutné přidat následující informace:

* Název účtu úložiště a klíč

    Můžete získat název účtu úložiště a klíč z [portál Azure](https://portal.azure.com/).
* ID plánu závazků

    Můžete získat ID plánu z [webové služby Azure Machine Learning](https://services.azureml.net) portálu přihlášení a kliknutím na název plánu.

Přidejte je do šablony JSON jako podřízené objekty *vlastnosti* uzel na stejné úrovni jako *MachineLearningWorkspace* uzlu.

Tady je příklad:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Naleznete v následujících článcích a ukázkový kód pro další podrobnosti:

* [Rutiny Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) odkaz na webu MSDN
* Ukázka [návod](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) na Githubu

## <a name="consume-the-web-services"></a>Využívání webových služeb
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z webové služby Azure Machine Learning uživatelského rozhraní (zkušební režim)
Můžete otestovat webovou službu na portálu Azure Machine Learning webové služby. To zahrnuje testování požadavků a odpovědí služby (záznamy RR) a služba Batch Execution (BES) rozhraní.

* [Nasazení nové webové služby](publish-a-machine-learning-web-service.md)
* [Nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Návod krok 5: Nasazení webové služby Azure Machine Learning](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Z aplikace Excel
Si můžete stáhnout šablony aplikace Excel, který využívá webové služby:

* [Využívají webové služby Azure Machine Learning z Excelu](consuming-from-excel.md)
* [Add-in pro webové služby Azure Machine Learning v aplikaci Excel](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Z klienta na základě REST
Webové služby sady Azure Machine Learning jsou rozhraní RESTful API. Můžete využívat tato rozhraní API z různých platformách, jako je například .NET, Python, R, Java, atd. **Spotřebě** stránky pro webovou službu na [portálu Microsoft Azure Machine Learning webové služby](https://services.azureml.net) obsahuje ukázkový kód, který můžete začít pracovat. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).
