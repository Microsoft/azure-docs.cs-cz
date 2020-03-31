---
title: Nasazení a spotřeba
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasické) můžete použít k nasazení pracovních postupů a modelů strojového učení jako webových služeb. Tyto webové služby pak lze volat modely strojového učení z aplikací přes internet dělat předpovědi v reálném čase nebo v dávkovém režimu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204388"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasické) webové služby: Nasazení a spotřeba

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasické) můžete použít k nasazení pracovních postupů a modelů strojového učení jako webových služeb. Tyto webové služby pak lze volat modely strojového učení z aplikací přes Internet k předpovědi v reálném čase nebo v dávkovém režimu. Vzhledem k tomu, že webové služby jsou RESTful, můžete volat z různých programovacích jazyků a platforem, jako je například .NET a Java, a z aplikací, jako je například Excel.

V dalších částech jsou uvedeny odkazy na návody, kód a dokumentaci, které vám pomůžou začít.

## <a name="deploy-a-web-service"></a>Nasazení webové služby

### <a name="with-azure-machine-learning-studio-classic"></a>S Azure Machine Learning Studio (klasické)

Portál Studio (klasický) a portál Microsoft Azure Machine Learning Web Services vám pomůžou nasadit a spravovat webovou službu bez psaní kódu.

Následující odkazy poskytují obecné informace o nasazení nové webové služby:

* Přehled o tom, jak nasadit novou webovou službu založenou na Azure Resource Manageru, najdete [v tématu Nasazení nové webové služby](deploy-a-machine-learning-web-service.md).
* Návod, jak nasadit webovou službu, najdete v [tématu Nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Úplný návod k vytvoření a nasazení webové služby naleznete v [kurzu 1: Předvídání úvěrového rizika](tutorial-part1-credit-risk.md).
* Konkrétní příklady nasazení webové služby najdete v tématu:

  * [Kurz 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)
  * [Jak nasadit webovou službu do více oblastí](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>S webovými weby poskytovatele prostředků API (Azure Resource Manager API)

Azure Machine Learning Studio (klasický) poskytovatel prostředků pro webové služby umožňuje nasazení a správu webových služeb pomocí volání rozhraní REST API. Další informace naleznete v odkazu [na webovou službu Machine Learning Web Service (REST).](/rest/api/machinelearning/index)

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>S rutinami prostředí PowerShell

Azure Machine Learning Studio (klasický) poskytovatel prostředků pro webové služby umožňuje nasazení a správu webových služeb pomocí rutin prostředí PowerShell.

Chcete-li rutiny používat, musíte se nejprve přihlásit ke svému účtu Azure z prostředí PowerShell pomocí rutiny [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Pokud nevíte, jak volat příkazy PowerShellu, které jsou založené na Správci prostředků, přečtěte si informace [o použití Azure PowerShellu ve Správci prostředků Azure](../../azure-resource-manager/management/manage-resources-powershell.md).

Chcete-li exportovat prediktivní experiment, použijte [tento ukázkový kód](https://github.com/ritwik20/AzureML-WebServices). Po vytvoření souboru EXE z kódu můžete zadat:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Spuštěním aplikace se vytvoří šablona JSON webové služby. Chcete-li použít šablonu k nasazení webové služby, musíte přidat následující informace:

* Název a klíč účtu úložiště

    Název a klíč účtu úložiště můžete získat z [webu Azure Portal](https://portal.azure.com/).
* ID plánu závazku

    ID plánu můžete získat z portálu [Azure Machine Learning Web Services](https://services.azureml.net) po přihlášení a kliknutí na název plánu.

Přidejte je do šablony JSON jako podřízené objekty uzlu *Vlastnosti* na stejné úrovni jako uzel *MachineLearningWorkspace.*

Tady je příklad:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Další podrobnosti naleznete v následujících článcích a ukázkovém kódu:

* [Azure Machine Learning Studio (klasické) Rutiny](https://docs.microsoft.com/powershell/module/az.machinelearning) odkaz na MSDN
* [Ukázkový návod](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) na GitHubu

## <a name="consume-the-web-services"></a>Využití webových služeb

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z uživatelského uživatelského terminálu (testování) webových služeb Azure Machine Learning

Webovou službu můžete otestovat z portálu Azure Machine Learning Web Services. To zahrnuje testování rozhraní služby odezvy (RRS) a služby dávkového spuštění (BES).

* [Nasazení nové webové služby](deploy-a-machine-learning-web-service.md)
* [Nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Kurz 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z Excelu

Můžete si stáhnout šablonu aplikace Excel, která využívá webovou službu:

* [Náročné webové služby Azure Machine Learning z Excelu](consuming-from-excel.md)
* [Doplněk Excelu pro Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Od klienta rest

Azure Machine Learning Web Services jsou RESTful API. Tato rozhraní API můžete využívat z různých platforem, jako je například .NET, Python, R, Java atd. Stránka **Spotřebovávat** pro vaši webovou službu na [portálu Microsoft Azure Machine Learning Web Services](https://services.azureml.net) obsahuje ukázkový kód, který vám pomůže začít. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).
