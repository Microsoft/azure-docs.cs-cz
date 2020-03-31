---
title: Onboarding prostředků
description: Přečtěte si o provádění registrace prostředků pomocí vlastních zprostředkovatelů Azure k použití správy nebo konfigurace pro jiné typy prostředků Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650406"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Přehled registrace prostředků vlastních zprostředkovatelů Azure

Přiřazování prostředků vlastních zprostředkovatelů Azure je model rozšiřitelnosti pro typy prostředků Azure. Umožňuje použít operace nebo správu napříč existujícími prostředky Azure ve velkém měřítku. Další informace najdete v tématu [Jak vlastní zprostředkovatelé Azure můžete rozšířit Azure](overview.md). Tento článek popisuje:

- Co může registrace zdrojů dělat.
- Základy registrace zdrojů a jeho použití.
- Kde najít průvodce a ukázky kódu, abyste mohli začít.

> [!IMPORTANT]
> Vlastní zprostředkovatelé jsou aktuálně ve verzi Public Preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce mohou být nepodporované nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Co může registrace zdrojů dělat?

Podobně jako [vlastní prostředky Azure ,](./custom-providers-resources-endpoint-how-to.md)připojení prostředků definuje smlouvu, která bude proxy "onboarding" požadavky na koncový bod. Na rozdíl od vlastních prostředků registrace prostředků nevytváří nový typ prostředku. Místo toho umožňuje rozšíření existujících typů prostředků. A registrace prostředků funguje s Azure Policy, takže správu a konfiguraci prostředků lze provádět ve velkém měřítku. Některé příklady pracovních postupů registrace zdrojů:

- Instalace a správa rozšíření virtuálních strojů.
- Nahrajte a nakonfigurujte výchozí hodnoty na účtech úložiště Azure.
- Povolte základní nastavení diagnostiky ve velkém měřítku.

## <a name="resource-onboarding-basics"></a>Základy registrace zdrojů

Přikonfiguraci prostředků prostřednictvím vlastních zprostředkovatelů Azure nakonfigurujete pomocí microsoft.customproviders/resourceProviders a typů prostředků Microsoft.CustomProviders/associations. Chcete-li povolit připojení prostředků pro vlastního zprostředkovatele, vytvořte během procesu konfigurace **resourceType** s názvem "přidružení" s **routingType,** který obsahuje "Rozšíření". Microsoft.CustomProviders/associations a Microsoft.CustomProviders/resourceProviders nemusí patřit do stejné skupiny prostředků.

Tady je ukázka vlastního zprostředkovatele Azure:

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
jméno | Ano | Název definice koncového bodu. Pro připnutí zdroje musí být název "přidružení".
typ směrování | Ano | Určuje typ smlouvy s koncovým bodem. Pro připojení prostředků jsou platné **směrovací typy** "Proxy,Cache,Extension" a "Webhook,Cache,Extension".
endpoint | Ano | Koncový bod pro směrování požadavků. To bude zpracovávat odpověď a všechny vedlejší účinky požadavku.

Po vytvoření vlastního zprostředkovatele s typem prostředku přidružení můžete cílit pomocí Microsoft.CustomProviders/associations. Microsoft.CustomProviders/associations je prostředek rozšíření, který může rozšířit jakýkoli jiný prostředek Azure. Při vytvoření instance Microsoft.CustomProviders/associations bude trvat vlastnost **targetResourceId**, což by mělo být platné Microsoft.CustomProviders/resourceProviders nebo Microsoft.Solutions/applications ID prostředku. V těchto případech bude požadavek předán typu prostředku přidružení v instanci Microsoft.CustomProviders/resourceProviders, kterou jste vytvořili.

> [!NOTE]
> Pokud Microsoft.Solutions/applications ID prostředku je k dispozici jako **targetResourceId**, musí být Microsoft.CustomProviders/resourceProviders nasazenve skupině spravovaných prostředků s názvem "public".

Ukázkové přidružení vlastních zprostředkovatelů Azure:

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
targetResourceId | Ano | ID prostředku Microsoft.CustomProviders/resourceProviders nebo Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Jak používat připonování zdrojů

Přizapisování prostředků funguje rozšířením dalších prostředků o prostředek rozšíření Microsoft.CustomProviders/associations. V následující ukázce je požadavek pro virtuální počítač, ale jakýkoli prostředek lze rozšířit.

Nejprve je třeba vytvořit vlastní prostředek zprostředkovatele s typem prostředku přidružení. Tím deklarujete adresu URL zpětného volání, která bude použita při vytvoření odpovídajícího prostředku Microsoft.CustomProviders/associations, který cílí na vlastního zprostředkovatele.

Ukázkový požadavek na vytvoření microsoft.customproviders/resourceProviders:

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

Po vytvoření vlastního zprostředkovatele můžete cílit na jiné prostředky a použít na ně vedlejší účinky vlastního zprostředkovatele.

Ukázkový požadavek na vytvoření microsoft.customproviders/associations:

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

Tento požadavek pak bude předán koncovému bodu určenému ve vlastním zprostředkovateli, na který jste vytvořili, na který odkazuje **targetResourceId** v tomto formuláři:

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

Koncový bod by měl reagovat s `Content-Type` aplikací/json a platné tělo odezvy JSON. Pole, která jsou vrácena pod **objektvlastnosti** JSON budou přidány do odpovědi na návrat přidružení.

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vývoje vlastních zprostředkovatelů prostředků Azure, zkuste se jich zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte ```azure-custom-providers``` značku, abyste získali rychlou odpověď!

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastní zprostředkovatelé. Další informace najdete v těchto článcích:

- [Kurz: Připojení zdrojů s vlastními poskytovateli](./tutorial-resource-onboarding.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastních akcí do rozhraní API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do rozhraní API Azure REST](./custom-providers-resources-endpoint-how-to.md)
