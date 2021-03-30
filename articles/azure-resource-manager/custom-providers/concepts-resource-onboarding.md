---
title: Onboarding prostředků
description: Přečtěte si, jak provést registraci prostředků pomocí vlastních zprostředkovatelů Azure, abyste mohli použít správu nebo konfiguraci na jiné typy prostředků Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650406"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Přehled registrace prostředků vlastních zprostředkovatelů Azure

Registrace prostředků vlastních zprostředkovatelů Azure je rozšiřující model pro typy prostředků Azure. Umožňuje aplikovat operace nebo správu napříč stávajícími prostředky Azure ve velkém měřítku. Další informace najdete v tématu [jak vlastní poskytovatelé Azure můžou Azure rozšiřuje](overview.md). Tento článek popisuje:

- Co může dělat registrace prostředku.
- Základy připojování zdrojů a jejich použití.
- Kde najít příručky a ukázky kódu, které vám pomohou začít.

> [!IMPORTANT]
> Vlastní zprostředkovatelé jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce můžou být nepodporované nebo můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>K čemu slouží prostředek pro registraci?

Podobně jako vlastní [poskytovatelé vlastních zprostředkovatelů Azure vlastní prostředky](./custom-providers-resources-endpoint-how-to.md), registraci prostředků definují kontrakt, který bude proxy požadavky na registraci do koncového bodu. Na rozdíl od vlastních prostředků nevytváří připojování prostředků nový typ prostředku. Místo toho umožňuje rozšíření existujících typů prostředků. A připojování prostředků funguje s Azure Policy, takže můžete provádět správu a konfiguraci prostředků ve velkém měřítku. Některé příklady pracovních postupů připojování prostředků:

- Nainstalujte a spravujte do rozšíření virtuálních počítačů.
- Nahrajte a nakonfigurujte výchozí hodnoty účtů úložiště Azure.
- Povolit nastavení diagnostiky standardních hodnot na stupnici.

## <a name="resource-onboarding-basics"></a>Základy připojování zdrojů

Registraci prostředků můžete nakonfigurovat pomocí vlastních zprostředkovatelů Azure pomocí typů prostředků Microsoft. CustomProviders/resourceProviders a Microsoft. CustomProviders/Association. Pokud chcete povolit registraci prostředků pro vlastního zprostředkovatele, vytvořte během procesu konfigurace typ **ResourceType** s názvem Associations a **routingType** , který obsahuje "rozšíření". Microsoft. CustomProviders/Associations a Microsoft. CustomProviders/resourceProviders nemusejí patřit do stejné skupiny prostředků.

Tady je ukázkový vlastní poskytovatel Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Vlastnost | Povinné? | Popis
---|---|---
name | Ano | Název definice koncového bodu. Pro registraci prostředků musí být název "asociace".
routingType | Ano | Určuje typ kontraktu s koncovým bodem. V případě připojování prostředků jsou platnými **routingTypes** "proxy, cache, Extension" a "Webhook, cache, Extension".
endpoint | Ano | Koncový bod, do kterého mají být směrovány požadavky. Tím se zpracuje odpověď a jakékoli vedlejší účinky žádosti.

Po vytvoření vlastního poskytovatele s typem prostředku přidružení můžete cílit pomocí Microsoft. CustomProviders/Associations. Microsoft. CustomProviders/Associations je prostředek rozšíření, který může rozšířit jakýkoli jiný prostředek Azure. Když je vytvořena instance Microsoft. CustomProviders/Associations, převezme vlastnost **parametrem targetresourceid**, která by měla být platným ID prostředku Microsoft. CustomProviders/ResourceProviders nebo Microsoft. Solutions/Applications. V těchto případech se požadavek přesměruje na typ prostředku přidružení v instanci Microsoft. CustomProviders/resourceProviders, kterou jste vytvořili.

> [!NOTE]
> Pokud je jako **parametrem targetresourceid** k dispozici ID prostředku Microsoft. Solutions/Applications, musí být ve spravované skupině prostředků nasazená aplikace Microsoft. CustomProviders/resourceProviders s názvem Public.

Ukázka přidružení vlastních poskytovatelů Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Vlastnost | Povinné? | Popis
---|---|---
Parametrem targetresourceid | Ano | ID prostředku Microsoft. CustomProviders/resourceProviders nebo Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Jak používat připojování zdrojů

Připojování prostředků funguje tak, že rozšíří další prostředky s prostředkem rozšíření Microsoft. CustomProviders/Associations. V následující ukázce je vytvořen požadavek pro virtuální počítač, ale kterýkoli prostředek lze rozšířit.

Nejprve je třeba vytvořit vlastní prostředek poskytovatele s typem prostředku přidružení. Tím se deklaruje adresa URL zpětného volání, která se použije při vytvoření odpovídajícího prostředku Microsoft. CustomProviders/přidružení, který cílí na vlastního zprostředkovatele.

Ukázka žádosti o vytvoření Microsoft. CustomProviders/resourceProviders:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Po vytvoření vlastního poskytovatele můžete cílit na jiné prostředky a použít na ně vedlejší účinky vlastního zprostředkovatele.

Ukázka žádosti o vytvoření služby Microsoft. CustomProviders/Associations:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Tato žádost se pak přepošle na koncový bod zadaný ve vlastním poskytovateli, který jste vytvořili, na který odkazuje **parametrem targetresourceid** v tomto formátu:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Koncový bod by měl reagovat pomocí aplikace/JSON `Content-Type` a platného textu odpovědi JSON. Pole, která jsou vrácena v objektu **Properties** objektu JSON, budou přidána do odpovědi na vrácení přidružení.

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vývoje vlastních poskytovatelů prostředků Azure, zkuste je zeptatte na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, před odesláním proto nejprve proveďte kontrolu. Přidejte značku ```azure-custom-providers``` , abyste získali rychlou odpověď.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastních poskytovatelích. Další informace najdete v těchto článcích:

- [Kurz: připojování prostředků s vlastními poskytovateli](./tutorial-resource-onboarding.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
