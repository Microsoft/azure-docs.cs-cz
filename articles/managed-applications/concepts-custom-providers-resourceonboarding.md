---
title: Registrace prostředků vlastních zprostředkovatelů Azure
description: Přečtěte si o registraci prostředků pomocí vlastních zprostředkovatelů Azure, které umožňují použít správu nebo konfiguraci na jiné typy prostředků Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609148"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Přehled registrace prostředků vlastních zprostředkovatelů Azure

Registrace prostředků vlastních zprostředkovatelů Azure je rozšiřující model pro typy prostředků Azure. Umožňuje aplikovat operace nebo správu napříč stávajícími prostředky Azure ve velkém měřítku. Další informace najdete v tématu [jak vlastní poskytovatelé Azure můžou Azure rozšiřuje](./custom-providers-overview.md). Tato dokumentace popisuje:

- K čemu slouží připojování prostředků.
- Základy připojování zdrojů a jejich použití.
- Kde najít příručky a ukázky kódu, které vám pomohou začít.

> [!IMPORTANT]
> Vlastní zprostředkovatelé jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Co je možné k registraci prostředků do provozu

Podobně jako [vlastní prostředky vlastního zprostředkovatele Azure](./custom-providers-resources-endpoint-how-to.md), registrace prostředků definuje kontrakt, který se bude zaregistrovat do požadavků na koncový bod proxy serveru. Na rozdíl od vlastních prostředků nevytváří registrace prostředků nový typ prostředku, ale spíše umožňuje rozšíření existujících typů prostředků. Zařazení prostředků do provozu funguje i Azure Policy, takže můžete provádět správu a konfiguraci prostředků ve velkém měřítku. Některé příklady pracovních postupů připojování prostředků:

- Instalace a Správa do rozšíření Virtual Machines.
- Nahrajte a nakonfigurujte výchozí hodnoty pro účty Azure Storage.
- Povolit nastavení diagnostiky standardních hodnot na stupnici.

## <a name="resource-onboarding-basics"></a>Základy připojování zdrojů

Připojování prostředků se konfiguruje prostřednictvím vlastních zprostředkovatelů Azure pomocí typů prostředků Microsoft. CustomProviders/resourceProviders a Microsoft. CustomProviders/Associations. Pokud chcete povolit registraci prostředků pro vlastního zprostředkovatele, během procesu konfigurace vytvořte typ **ResourceType** s názvem Associations a **routingType** , který obsahuje "rozšíření". Kromě toho nemusí patřit mezi "Microsoft. CustomProviders/Associations" a "Microsoft. CustomProviders/resourceProviders" do stejné skupiny prostředků.

Ukázkový vlastní poskytovatel Azure:

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

Vlastnost | Požaduje se | Popis
---|---|---
jméno | *Ano* | Název definice koncového bodu. Pro zařazení prostředku musí být název "asociace".
routingType | *Ano* | Určuje typ kontraktu s **koncovým bodem**. V případě připojování prostředků jsou platnými **routingTypes** "proxy, cache, Extension" a "Webhook, cache, Extension".
endpoint | *Ano* | Koncový bod, do kterého mají být směrovány požadavky. Tím se zpracuje odpověď i všechny vedlejší účinky žádosti.

Jakmile se vytvoří vlastní zprostředkovatel s typem prostředku asociace, můžete cílit pomocí Microsoft. CustomProviders/Associations. "Microsoft. CustomProviders/Associations" je prostředek rozšíření, který může rozšířit jakýkoli jiný prostředek Azure. Když je vytvořena instance "Microsoft. CustomProviders/Associations", bude mít vlastnost **parametrem targetresourceid**, která by měla být platným ID prostředku Microsoft. CustomProviders/resourceProviders "nebo" Microsoft. Solutions/Applications ". V těchto případech se požadavek přesměruje na typ prostředku Associations v instanci Microsoft. CustomProviders/resourceProviders, kterou jsme vytvořili.

> [!Note]
> Pokud je jako **parametrem targetresourceid**k dispozici ID prostředku Microsoft. Solutions/Applications, musí být ve spravované skupině prostředků nasazená možnost Microsoft. CustomProviders/resourceProviders s názvem Public.

Ukázka přidružení vlastního zprostředkovatele Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Vlastnost | Požaduje se | Popis
---|---|---
Parametrem targetresourceid | *Ano* | ID prostředku "Microsoft. CustomProviders/resourceProviders" nebo "Microsoft. Solutions/Applications".

## <a name="how-to-use-resource-onboarding"></a>Jak používat připojování zdrojů

Připojování prostředků funguje tak, že rozšíří další prostředky s prostředkem rozšíření Microsoft. CustomProviders/Associations. V následující ukázce bude vytvořen požadavek pro virtuální počítač, ale kterýkoli prostředek lze rozšířit.

Nejdřív je potřeba vytvořit prostředek vlastního zprostředkovatele s typem prostředku Associations. Tím se deklaruje adresa URL zpětného volání, která se použije při vytvoření odpovídajícího prostředku Microsoft. CustomProviders/Associations, který cílí na vlastního zprostředkovatele.

Ukázka žádosti o vytvoření "Microsoft. CustomProviders/resourceProviders":

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

Po vytvoření vlastního poskytovatele teď můžeme cílit na jiné prostředky a použít na ně vedlejší účinky vlastního zprostředkovatele.

Ukázka žádosti o vytvoření "Microsoft. CustomProviders/Associations":

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

Tato žádost se pak přepošle na koncový bod zadaný v počátečním vytvořeném vlastním zprostředkovateli, na který se odkazuje ve formátu "parametrem targetresourceid".

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

Koncový bod by měl odpovídat příkazu "Application/JSON" `Content-Type` a platnému textu odpovědi JSON. Pole, která jsou vrácena pod objektem "Properties" objektu JSON, budou přidána do odpovědi na vrácení přidružení.

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy pro vývoj vlastních poskytovatelů prostředků Azure, zkuste požádat o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte ```azure-custom-providers``` značek, abyste získali rychlou odezvu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastních poskytovatelích. Pokud chcete vytvořit vlastního poskytovatele, přečtěte si další článek.

- [Kurz: připojování prostředků s vlastními poskytovateli](./tutorial-custom-providers-resource-onboarding.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-custom-providers-101.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
