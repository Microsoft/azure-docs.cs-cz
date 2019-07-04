---
title: Přehled poskytovatelů vlastního prostředku Azure
description: Další informace o poskytovatelů prostředků vlastní Azure a jak rozšířit rovinu rozhraní API služby Azure podle vašich pracovních postupů.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: f418cd6c5470740ce123448ddbbe54cb6e89dabe
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67475935"
---
# <a name="azure-custom-resource-providers-overview"></a>Přehled poskytovatelů vlastního prostředku Azure

Azure Vlastní zprostředkovatelé prostředků je platforma rozšíření do Azure. Je možné, že definujete vlastní rozhraní API, která je možné rozšířit výchozí prostředí Azure. Tato dokumentace popisuje:

- Jak sestavit a nasadit vlastní poskytovatele prostředků Azure.
- Jak využívat poskytovatele prostředků vlastní Azure k rozšíření stávajících pracovních postupů.
- Kde najít vodítka a ukázky kódu, abyste mohli začít.

![Přehled vlastního zprostředkovatele](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Vlastní zprostředkovatelé je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Co nabízí poskytovatelé vlastních prostředků?

Tady je několik příkladů můžete dosáhnout zprostředkovatelů prostředků služeb Azure vlastní:

- Rozšíření REST API Azure Resource Manageru k zahrnuje interní a externí služby.
- Povolte vlastní scénáře nad stávajících pracovních postupů Azure.
- Přizpůsobení ovládacího prvku šablon Azure Resource Manageru a účinnosti.

## <a name="what-is-a-custom-resource-provider"></a>Co je poskytovatel vlastní prostředek

Poskytovatelé prostředků Azure vlastní probíhají tak, že vytvoříte kontrakt mezi Azure a koncový bod. Tento kontrakt definuje seznam nových prostředků a akcí prostřednictvím nového prostředku, **Microsoft.CustomProviders/resourceProviders**. Zprostředkovatel vlastní prostředek bude vystavovat pak tato nová rozhraní API v Azure. Azure vlastního poskytovatele prostředků se skládají ze tří částí: vlastní prostředek zprostředkovatele **koncové body**a vlastní prostředky.

## <a name="how-to-build-custom-resource-providers"></a>Jak vytvořit vlastní prostředek zprostředkovatelů

Vlastní prostředek poskytovatelé jsou přehled smluv mezi Azure a koncovými body. Tato smlouva popisuje interakci Azure s koncovým bodem. Funguje poskytovatele prostředků jako proxy a předá požadavky a odpovědi do a ze zadaného **koncový bod**. Poskytovatel prostředků můžete určit dva typy kontraktů: [ **tyto typy:** ](./custom-providers-resources-endpoint-how-to.md) a [ **akce**](./custom-providers-action-endpoint-how-to.md). Tyto akce jsou povolené prostřednictvím definic koncových bodů. Definice koncového bodu se skládá ze tří polí: **název**, **routingType**, a **koncový bod**.

Ukázka koncového bodu:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Vlastnost | Požaduje se | Popis
---|---|---
name | *Ano* | Název definice koncového bodu. Azure bude vystavovat tento název prostřednictvím jejího rozhraní API v části "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders / {název resourceProviderName} / {endpointDefinitionName}.
routingType | *no* | Určuje typ kontraktu s **koncový bod**. Pokud není zadán, nastaví se jako výchozí "Proxy".
endpoint | *Ano* | Koncový bod pro směrování požadavků. To zpracuje odpověď, jakož i všechny vedlejší účinky požadavku.

### <a name="building-custom-resources"></a>Vytváření vlastních prostředků

**Tyto typy:** popsat nové vlastní prostředky, které jsou přidané do Azure. Tyto základní metody RESTful CRUD zveřejnit. Zobrazit [Další informace o vytváření vlastních prostředků](./custom-providers-resources-endpoint-how-to.md)

Ukázková vlastní poskytovatele prostředků s **tyto typy:** :

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

Přidané do Azure pro výše uvedené ukázkové rozhraní API:

HttpMethod | Ukázkový identifikátor URI | Popis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní REST API služby Azure k vytvoření nového prostředku.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní Azure REST API pro odstranění existující prostředek.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Volání rozhraní REST API služby Azure se načíst existující prostředek.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Volání rozhraní Azure REST API k načtení seznamu existujících prostředků.

### <a name="building-custom-actions"></a>Vytváření vlastních akcí

**Akce** popisují nové akce, které jsou přidané do Azure. Tyto vystavené na poskytovateli prostředků nebo vnořen v souladu **resourceType**. Zobrazit [Další informace o vytvoření vlastní akce](./custom-providers-action-endpoint-how-to.md)

Ukázková vlastní poskytovatele prostředků s **akce**:

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

Přidané do Azure pro výše uvedené ukázkové rozhraní API:

HttpMethod | Ukázkový identifikátor URI | Popis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Volání rozhraní REST API služby Azure k aktivaci akce.

## <a name="looking-for-help"></a>Hledáte pomoc

Pokud máte dotazy pro poskytovatele prostředků Azure vlastní vývoj, zkuste se zeptat [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobně jako dotaz může již byly dotaz a odpovědi, podívejte se proto před publikováním. Přidejte značku ```azure-custom-providers``` získat rychlou odpověď!

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o vlastní poskytovatele. Přejdete na další článek k vytvoření vlastního zprostředkovatele.

- [Kurz: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
