---
title: Nasazení prostředků pomocí rozhraní API REST a šablony | Microsoft Docs
description: Nasazení prostředky do Azure pomocí Azure Resource Manageru a REST API Resource Manageru. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6ae77eb1f619928f43a502cd4631a0895a9e91f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603730"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API

Tento článek vysvětluje, jak používat rozhraní REST API služby Správce prostředků pomocí šablony Resource Manageru k nasazení vašich prostředků Azure.  

> [!TIP]
> Pomoc s laděním chybu během nasazení najdete v tématu:
> 
> * [Zobrazit operace nasazení](resource-manager-deployment-operations.md) Další informace o získání informací, která pomáhá odstranění vaší chyby
> * [Odstraňování běžných chyb při nasazování prostředků do Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md) Další informace o řešení běžných chyb při nasazení
> 
> 

Šablony může být místní soubor nebo externí soubor, který je k dispozici prostřednictvím identifikátoru URI. Pokud vaše šablona se nachází v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdílený přístupový podpis (SAS).

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Nasazení pomocí rozhraní REST API
1. Nastavit [společné parametry a hlavičky](/rest/api/azure/), včetně ověřování tokenů.

2. Pokud nemáte existující skupinu prostředků, vytvořte skupinu prostředků. Zadejte svoje ID předplatného, název nové skupiny prostředků a umístění, které potřebujete pro vaše řešení. Další informace najdete v tématu [vytvořte skupinu prostředků](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Ověření nasazení před provedením spuštěním [ověření nasazení šablony](/rest/api/resources/deployments/validate) operaci. Při testování nasazení, zadejte parametry přesně stejně jako při provádění nasazení (zobrazené v dalším kroku).

4. Vytvořte nasazení. Zadejte svoje ID předplatného, název skupiny prostředků, název nasazení a odkaz na šablonu. Informace o souboru šablony najdete v tématu [soubor parametrů](#parameter-file). Další informace o rozhraní REST API pro vytvoření skupiny prostředků najdete v tématu [vytvořit šablonu nasazení](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Upozornění **režimu** je nastaven na **přírůstkové**. Chcete-li spustit kompletní nasazení, nastavte **režimu** k **Complete**. Buďte opatrní při používání dokončení režimu jako nechtěně může odstranit prostředky, které nejsou ve vaší šabloně.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    Pokud chcete protokolovat obsah odpovědi, obsah žádosti nebo obojí, zahrnout **debugSetting** v požadavku.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    Můžete nastavit účtu úložiště používat token sdílený přístupový podpis (SAS). Další informace najdete v tématu [delegování přístupu k pomocí sdíleného přístupového podpisu](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

5. Získáte stav nasazení šablony. Další informace najdete v tématu [získat informace o nasazení šablony](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Znovu nasaďte při selhání nasazení

Pro nasazení, které nesplní můžete určit, že se k předchozímu nasazení z historie nasazení automaticky nasazovat. Chcete-li použít tuto možnost, vaše nasazení musí mít jedinečné názvy, je možné zjistit v historii. Pokud nemáte jedinečné názvy, aktuální neúspěšné nasazení přepsat dříve úspěšné nasazení v historii. Tuto možnost můžete použít pouze u nasazení kořenové úrovni. Nasazení ze šablony vnořené nejsou k dispozici pro opakované nasazení.

Pokud chcete znovu zavést poslední úspěšné nasazení, pokud se aktuální nasazení nezdaří, použijte:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Chcete-li znovu nasadit konkrétní nasazení, pokud se aktuální nasazení nezdaří, použijte:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

Zadané nasazení musí mít bylo úspěšné.

## <a name="parameter-file"></a>Soubor parametrů

Pokud použijete soubor s parametry předat hodnoty parametrů během nasazování, budete muset vytvořte soubor JSON ve formátu podobně jako v následujícím příkladu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Velikost souboru parametr nemůže být delší než 64 KB.

Pokud potřebujete zajistit citlivou hodnotu pro parametr (třeba heslo), přidejte tuto hodnotu do trezoru klíčů. Během nasazování načtěte trezoru klíčů, jak je znázorněno v předchozím příkladu. Další informace najdete v tématu [předat zabezpečené hodnoty během nasazení](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Další postup
* Další informace o zpracování asynchronní operace REST najdete v tématu [sledovat asynchronní operace Azure](resource-manager-async-operations.md).
* Příklad nasazení prostředků prostřednictvím klientské knihovny .NET, naleznete v části [nasazení prostředků s použitím knihovny .NET a šablonu](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](solution-dev-test-environments.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

