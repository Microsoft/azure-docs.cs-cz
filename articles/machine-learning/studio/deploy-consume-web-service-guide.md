---
title: 'Azure Machine Learning Web Services: Nasazení a využití | Dokumentace Microsoftu'
description: Prostředky pro nasazení a používání webových služeb.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 321edd134fd2ac6b03ad6d3117944f0f59c24669
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055025"
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Webové služby Azure Machine Learning: Nasazení a využití
Azure Machine Learning můžete použít k nasazení pracovních postupů a modely jako webové služby machine learning. Tyto webové služby lze pak volat modely strojového učení z aplikací přes Internet vytvářející předpovědi v reálném čase nebo v dávkovém režimu. Protože jsou webové služby RESTful, můžete je volat z různé programovací jazyky a platformy, jako je .NET nebo Javě a z aplikace, jako je Excel.

Následující části obsahují odkazy na návody, kód a dokumentaci, která vám pomůžou začít.

## <a name="deploy-a-web-service"></a>Nasazení webové služby

### <a name="with-azure-machine-learning-studio"></a>S Azure Machine Learning Studio
Machine Learning Studio a na portálu Microsoft Azure Machine Learning Web Services umožňují nasadit a spravovat webové služby bez nutnosti psaní kódu.

Následující odkazy obsahují obecné informace o tom, jak nasadit nové webové služby:

* Přehled o tom, jak nasadit novou webovou službu, která je založená na Azure Resource Manageru najdete v tématu [nasazení nové webové služby](publish-a-machine-learning-web-service.md).
* Návod, jak nasadit webovou službu, naleznete v tématu [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Úplný návod o tom, jak vytvořit a nasadit webovou službu, naleznete v tématu [návod krok 1: vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md).
* Konkrétní příklady, které nasazení webové služby naleznete v tématu:

  * [Krok 5 průvodce: Nasazení webové služby Azure Machine Learning](walkthrough-5-publish-web-service.md)
  * [Jak nasadit webovou službu do více oblastí](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>U poskytovatele prostředků služby webového rozhraní API (rozhraní API Azure Resource Manager)
Poskytovatel prostředků Azure Machine Learning pro webové služby umožňuje nasazení a Správa webových služeb s použitím volání rozhraní REST API. Další podrobnosti najdete v tématu [Machine Learning webových služeb (REST)](/rest/api/machinelearning/index) odkaz.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Poskytovatel prostředků Azure Machine Learning pro webové služby umožňuje nasazení a Správa webových služeb pomocí rutin prostředí PowerShell.

Pokud chcete použít rutiny, musíte nejdřív se přihlásit ke svému účtu Azure z prostředí PowerShell pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny. Pokud nejste obeznámeni s jak volat příkazy Powershellu, které jsou založené na Resource Manageru, najdete v [pomocí Azure Powershellu s Azure Resource Managerem](../../azure-resource-manager/powershell-azure-resource-manager.md).

Chcete-li exportovat prediktivní experiment, použijte [ukázkový kód](https://github.com/ritwik20/AzureML-WebServices). Po vytvoření souboru .exe z kódu, můžete zadat:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Spuštění aplikace vytvoří šablonu JSON webové služby. Použití šablony k nasazení webové služby, je nutné přidat následující informace:

* Název účtu úložiště a klíč

    Můžete získat název účtu úložiště a klíč z [webu Azure portal](https://portal.azure.com/).
* ID plánu závazku

    Můžete získat ID plánu z [Azure Machine Learning Web Services](https://services.azureml.net) portálu přihlásit a kliknutím na název plánu.

Přidat do šablony JSON jako podřízených prvků *vlastnosti* uzel na stejné úrovni jako *MachineLearningWorkspace* uzlu.

Tady je příklad:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Naleznete v následujících článcích a ukázkový kód pro další podrobnosti:

* [Rutiny Azure Machine Learning](https://docs.microsoft.com/powershell/module/azurerm.machinelearning) odkaz na webu MSDN
* Ukázka [návod](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) na Githubu

## <a name="consume-the-web-services"></a>Využívání webových služeb
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z Azure Machine Learning Web Services uživatelského rozhraní (testování)
Můžete testovat webové služby z portálu Azure Machine Learning Web Services. Jedná se o testování služba Request-Response (RRS) a služba Batch Execution (BES) rozhraní.

* [Nasazení nové webové služby](publish-a-machine-learning-web-service.md)
* [Nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Krok 5 průvodce: Nasazení webové služby Azure Machine Learning](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Z aplikace Excel
Si můžete stáhnout šablony aplikace Excel, která využívá webovou službu:

* [Využívání webové služby Azure Machine Learning z Excelu](consuming-from-excel.md)
* [Doplněk Excelu pro Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Z klienta založené na REST
Azure Machine Learning Web Services jsou rozhraní RESTful API. Můžete využívat tato rozhraní API z různých platforem, jako je .NET, Python, R, Java atd. **Využívání** stránky pro webovou službu na [portálu Microsoft Azure Machine Learning Web Services](https://services.azureml.net) obsahuje ukázkový kód, který může pomoci vám začít. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).
