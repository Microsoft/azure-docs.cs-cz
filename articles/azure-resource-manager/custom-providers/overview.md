---
title: Přehled vlastních poskytovatelů
description: Přečtěte si o vlastních zprostředkovatelích prostředků Azure a o tom, jak rozšířit rovinu rozhraní API Azure tak, aby vyhovovala vašim pracovním postupům.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650484"
---
# <a name="azure-custom-resource-providers-overview"></a>Přehled poskytovatelů vlastních prostředků Azure

Azure Vlastní zprostředkovatelé prostředků je rozšiřitelnost platformy pro Azure. Umožňuje definovat vlastní api, která lze použít k obohacení výchozího prostředí Azure. Tato dokumentace popisuje:

- Jak vytvořit a nasadit zprostředkovatele vlastních prostředků Azure.
- Jak využít Azure vlastní zprostředkovatelé prostředků rozšířit existující pracovní postupy.
- Kde najít průvodce a ukázky kódu, abyste mohli začít.

![Přehled vlastního poskytovatele](./media/overview/overview.png)

> [!IMPORTANT]
> Vlastní zprostředkovatelé jsou aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Co mohou vlastní poskytovatelé prostředků dělat

Tady je několik příkladů toho, čeho můžete dosáhnout s vlastními poskytovateli prostředků Azure:

- Rozšiřte rozhraní REST Správce prostředků Azure tak, aby zahrnovalo interní a externí služby.
- Povolte vlastní scénáře nad existujícími pracovními postupy Azure.
- Přizpůsobte si řízení a efekt šablon Azure Resource Manager.

## <a name="what-is-a-custom-resource-provider"></a>Co je vlastní poskytovatel prostředků

Azure Vlastní zprostředkovatelé prostředků se vytvoří vytvořením smlouvy mezi Azure a koncovýbod. Tato smlouva definuje seznam nových prostředků a akcí prostřednictvím nového prostředku **Microsoft.CustomProviders/resourceProviders**. Vlastní poskytovatel prostředků pak zpřístupní tato nová řešení API v Azure. Zprostředkovatelé vlastních prostředků Azure se skládají ze tří částí: vlastní zprostředkovatel prostředků, **koncové body**a vlastní prostředky.

## <a name="how-to-build-custom-resource-providers"></a>Jak vytvořit vlastní zprostředkovatele prostředků

Vlastní poskytovatelé prostředků jsou seznam smluv mezi Azure a koncovými body. Tato smlouva popisuje, jak by měl Azure pracovat s koncovým bodem. Poskytovatel prostředků se chová jako proxy server a předá požadavky a odpovědi do a ze zadaného **koncového bodu**. Zprostředkovatel prostředků může zadat dva typy smluv: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) a [**actions**](./custom-providers-action-endpoint-how-to.md). Ty jsou povoleny prostřednictvím definice koncového bodu. Definice koncového bodu se skládá ze tří polí: **název**, **routingType**a **koncový bod**.

Ukázkový koncový bod:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Vlastnost | Požaduje se | Popis
---|---|---
jméno | *Ano* | Název definice koncového bodu. Azure tento název zobrazí prostřednictvím svého rozhraní API v části '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
typ směrování | *ne* | Určuje typ smlouvy s **koncovým bodem**. Pokud není zadán, bude výchozí "Proxy".
endpoint | *Ano* | Koncový bod pro směrování požadavků. To bude zpracovávat odpověď, stejně jako všechny vedlejší účinky požadavku.

### <a name="building-custom-resources"></a>Vytváření vlastních prostředků

**ResourceTypes** popisují nové vlastní prostředky, které se přidají do Azure. Tyto vystavit základní RESTful CRUD metody. Další informace [o vytváření vlastních zdrojů](./custom-providers-resources-endpoint-how-to.md)

Ukázkový vlastní zprostředkovatel prostředků s **resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API přidaná do Azure pro výše uvedenou ukázku:

Metoda http | Ukázkový identifikátor URI | Popis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní API Azure REST k vytvoření nového prostředku.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní API Azure REST k odstranění existujícího prostředku.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní API Azure REST k načtení existujícího prostředku.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Volání rozhraní API Azure REST k načtení seznamu existujících prostředků.

### <a name="building-custom-actions"></a>Vytváření vlastních akcí

**Akce** popisují nové akce, které se přidají do Azure. Ty mohou být vystaveny nad zprostředkovatelem prostředků nebo vnořené pod **resourceType**. Další informace [o vytváření vlastních akcí](./custom-providers-action-endpoint-how-to.md)

Ukázkový vlastní zprostředkovatel prostředků s **akcemi**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API přidaná do Azure pro výše uvedenou ukázku:

Metoda http | Ukázkový identifikátor URI | Popis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Volání rozhraní API Azure REST k aktivaci akce.

## <a name="looking-for-help"></a>Hledání pomoci

Pokud máte otázky týkající se vývoje zprostředkovatele vlastních prostředků Azure, zkuste se zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být položena a zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte ```azure-custom-providers``` značku, abyste získali rychlou odpověď!

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastní zprostředkovatelé. Chcete-li vytvořit vlastního zprostředkovatele, přejděte k dalšímu článku.

- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postup: Přidání vlastních akcí do rozhraní API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do rozhraní API Azure REST](./custom-providers-resources-endpoint-how-to.md)
