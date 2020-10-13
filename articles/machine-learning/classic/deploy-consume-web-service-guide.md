---
title: 'ML Studio (Classic): nasazení a spotřeba – Azure'
description: K nasazení pracovních postupů a modelů machine learningu jako webových služeb můžete použít Azure Machine Learning Studio (Classic). Tyto webové služby pak můžete použít k volání modelů strojového učení z aplikací přes Internet, aby se předpovědi v reálném čase nebo v režimu dávky.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 52404aa1514dc3189305117181408e25e233fce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362397"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webové služby Azure Machine Learning Studio (Classic): nasazení a spotřeba

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

K nasazení pracovních postupů a modelů machine learningu jako webových služeb můžete použít Azure Machine Learning Studio (Classic). Tyto webové služby pak můžete použít k volání modelů strojového učení z aplikací přes Internet, aby se předpovědi v reálném čase nebo v režimu dávky. Vzhledem k tomu, že webové služby jsou RESTful, můžete je volat z různých programovacích jazyků a platforem, jako je například .NET a Java, a z aplikací, jako je například aplikace Excel.

Další části obsahují odkazy na návody, kód a dokumentaci, které vám pomůžou začít.

## <a name="deploy-a-web-service"></a>Nasazení webové služby

### <a name="with-azure-machine-learning-studio-classic"></a>S Azure Machine Learning Studio (Classic)

Portál studia (Classic) a Microsoft Azure Machine Learning portál webové služby vám pomůžou nasadit a spravovat webovou službu bez psaní kódu.

Následující odkazy poskytují obecné informace o tom, jak nasadit novou webovou službu:

* Přehled o tom, jak nasadit novou webovou službu založenou na Azure Resource Manager, najdete v tématu [nasazení nové webové služby](deploy-a-machine-learning-web-service.md).
* Návod, jak nasadit webovou službu, najdete v tématu [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Úplný návod, jak vytvořit a nasadit webovou službu, najdete v tématu [kurz 1: předpověď úvěrového rizika](tutorial-part1-credit-risk.md).
* Konkrétní příklady nasazení webové služby najdete v tématech:

  * [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)
  * [Nasazení webové služby do více oblastí](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>S rozhraními API pro poskytovatele prostředků webových služeb (Azure Resource Manager API)

Poskytovatel prostředků Azure Machine Learning Studio (klasický) pro webové služby umožňuje nasazení a správu webových služeb pomocí volání REST API. Další informace najdete v referenčních informacích o [službě Machine Learning Web Service (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>S rutinami PowerShellu

Poskytovatel prostředků Azure Machine Learning Studio (klasický) pro webové služby umožňuje nasazení a správu webových služeb pomocí rutin prostředí PowerShell.

Pokud chcete rutiny použít, musíte se nejdřív přihlásit k účtu Azure z prostředí PowerShell pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Pokud nejste obeznámeni s tím, jak volat příkazy prostředí PowerShell založené na Správce prostředků, přečtěte si téma [použití Azure PowerShell s Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

K exportu prediktivní experimentu použijte [Tento vzorový kód](https://github.com/ritwik20/AzureML-WebServices). Po vytvoření souboru. exe z kódu můžete zadat:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Spuštění aplikace vytvoří šablonu JSON webové služby. Chcete-li použít šablonu k nasazení webové služby, je nutné přidat následující informace:

* Název a klíč účtu úložiště

    Název a klíč účtu úložiště můžete získat z [Azure Portal](https://portal.azure.com/).
* ID plánu závazku

    ID plánu můžete získat z portálu [Azure Machine Learning Web Services](https://services.azureml.net) tak, že se přihlásíte a kliknete na název plánu.

Přidejte je do šablony JSON jako podřízené položky uzlu *vlastnosti* na stejné úrovni jako uzel *MachineLearningWorkspace* .

Tady je příklad:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Další podrobnosti najdete v následujících článcích a vzorovém kódu:

* Reference k [rutinám Azure Machine Learning Studio (Classic)](https://docs.microsoft.com/powershell/module/az.machinelearning) na webu MSDN

## <a name="consume-the-web-services"></a>Využívání webových služeb

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z uživatelského rozhraní Azure Machine Learning webové služby (testování)

Webovou službu můžete testovat na portálu Azure Machine Learning Web Services. To zahrnuje testování rozhraní služby Request-Response (RR) a služby Batch Execution Service (BES).

* [Nasazení nové webové služby](deploy-a-machine-learning-web-service.md)
* [Nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z Excelu

Můžete si stáhnout excelovou šablonu, která využívá webovou službu:

* [Využívání webové služby Azure Machine Learning z Excelu](consuming-from-excel.md)
* [Doplněk aplikace Excel pro Azure Machine Learning webové služby](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Z klienta založeného na REST

Azure Machine Learning webové služby jsou rozhraní API RESTful. Tato rozhraní API můžete využívat na různých platformách, jako je .NET, Python, R, Java atd. Stránka **využívání** webové služby na [portálu Microsoft Azure Machine Learning Web Services](https://services.azureml.net) obsahuje vzorový kód, který vám může pomáhat začít. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).
