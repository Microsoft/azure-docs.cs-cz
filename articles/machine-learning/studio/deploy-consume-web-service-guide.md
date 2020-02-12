---
title: Nasazení a spotřeba
titleSuffix: ML Studio (classic) - Azure
description: K nasazení pracovních postupů a modelů machine learningu jako webových služeb můžete použít Azure Machine Learning Studio (Classic). Tyto webové služby můžete pak použita pro volání modely strojového učení z aplikací přes internet vytvářející předpovědi v reálném čase nebo v dávkovém režimu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 0090c6417921bd9bb6c64048fffadab19d4813c6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153515"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webové služby Azure Machine Learning Studio (Classic): nasazení a spotřeba

K nasazení pracovních postupů a modelů machine learningu jako webových služeb můžete použít Azure Machine Learning Studio (Classic). Tyto webové služby můžete pak použita pro volání modely strojového učení z aplikací přes Internet vytvářející předpovědi v reálném čase nebo v dávkovém režimu. Protože jsou webové služby RESTful, můžete je volat z různé programovací jazyky a platformy, jako je .NET nebo Javě a z aplikace, jako je Excel.

Následující části obsahují odkazy na návody, kód a dokumentaci, která vám pomůžou začít.

## <a name="deploy-a-web-service"></a>Nasazení webové služby

### <a name="with-azure-machine-learning-studio-classic"></a>S Azure Machine Learning Studio (Classic)

Portál studia (Classic) a Microsoft Azure Machine Learning portál webové služby vám pomůžou nasadit a spravovat webovou službu bez psaní kódu.

Následující odkazy obsahují obecné informace o tom, jak nasadit nové webové služby:

* Přehled o tom, jak nasadit novou webovou službu založenou na Azure Resource Manager, najdete v tématu [nasazení nové webové služby](deploy-a-machine-learning-web-service.md).
* Návod, jak nasadit webovou službu, najdete v tématu [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Úplný návod, jak vytvořit a nasadit webovou službu, najdete v tématu [kurz 1: předpověď úvěrového rizika](tutorial-part1-credit-risk.md).
* Konkrétní příklady, které nasazení webové služby naleznete v tématu:

  * [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)
  * [Nasazení webové služby do více oblastí](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>U poskytovatele prostředků služby webového rozhraní API (rozhraní API Azure Resource Manager)

Poskytovatel prostředků Azure Machine Learning Studio (klasický) pro webové služby umožňuje nasazení a správu webových služeb pomocí volání REST API. Další informace najdete v referenčních informacích o [službě Machine Learning Web Service (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell

Poskytovatel prostředků Azure Machine Learning Studio (klasický) pro webové služby umožňuje nasazení a správu webových služeb pomocí rutin prostředí PowerShell.

Pokud chcete rutiny použít, musíte se nejdřív přihlásit k účtu Azure z prostředí PowerShell pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Pokud nejste obeznámeni s tím, jak volat příkazy prostředí PowerShell založené na Správce prostředků, přečtěte si téma [použití Azure PowerShell s Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

K exportu prediktivní experimentu použijte [Tento vzorový kód](https://github.com/ritwik20/AzureML-WebServices). Po vytvoření souboru .exe z kódu, můžete zadat:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Spuštění aplikace vytvoří šablonu JSON webové služby. Použití šablony k nasazení webové služby, je nutné přidat následující informace:

* Název účtu úložiště a klíč

    Název a klíč účtu úložiště můžete získat z [Azure Portal](https://portal.azure.com/).
* ID plánu závazku

    ID plánu můžete získat z portálu [Azure Machine Learning Web Services](https://services.azureml.net) tak, že se přihlásíte a kliknete na název plánu.

Přidejte je do šablony JSON jako podřízené položky uzlu *vlastnosti* na stejné úrovni jako uzel *MachineLearningWorkspace* .

Tady je příklad:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Naleznete v následujících článcích a ukázkový kód pro další podrobnosti:

* Reference k [rutinám Azure Machine Learning Studio (Classic)](https://docs.microsoft.com/powershell/module/az.machinelearning) na webu MSDN
* Ukázkový [návod](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) na GitHubu

## <a name="consume-the-web-services"></a>Využívání webových služeb

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z Azure Machine Learning Web Services uživatelského rozhraní (testování)

Můžete testovat webové služby z portálu Azure Machine Learning Web Services. Jedná se o testování služba Request-Response (RRS) a služba Batch Execution (BES) rozhraní.

* [Nasazení nové webové služby](deploy-a-machine-learning-web-service.md)
* [Nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z aplikace Excel

Si můžete stáhnout šablony aplikace Excel, která využívá webovou službu:

* [Využívání webové služby Azure Machine Learning z Excelu](consuming-from-excel.md)
* [Doplněk aplikace Excel pro Azure Machine Learning webové služby](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Z klienta založené na REST

Azure Machine Learning Web Services jsou rozhraní RESTful API. Tato rozhraní API můžete využívat na různých platformách, jako je .NET, Python, R, Java atd. Stránka **využívání** webové služby na [portálu Microsoft Azure Machine Learning Web Services](https://services.azureml.net) obsahuje vzorový kód, který vám může pomáhat začít. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).
