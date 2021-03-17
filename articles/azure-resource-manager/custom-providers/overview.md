---
title: Přehled vlastních zprostředkovatelů
description: Přečtěte si o vlastních poskytovatelích prostředků Azure a o tom, jak rozšiřuje plochu rozhraní API Azure tak, aby odpovídala pracovním postupům.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80398465"
---
# <a name="azure-custom-resource-providers-overview"></a>Vlastní poskytovatelé prostředků Azure – přehled

Vlastní poskytovatelé prostředků Azure je rozšiřitelná platforma pro Azure. Umožňuje definovat vlastní rozhraní API, která se dají použít k rozšíření výchozích možností Azure. Tato dokumentace popisuje:

- Jak vytvořit a nasadit poskytovatele vlastního prostředku Azure
- Jak využívat vlastní poskytovatele prostředků Azure pro rozšiřování stávajících pracovních postupů.
- Kde najít příručky a ukázky kódu, které vám pomohou začít.

![Přehled vlastního zprostředkovatele](./media/overview/overview.png)

> [!IMPORTANT]
> Vlastní zprostředkovatelé jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Co můžou vlastní poskytovatelé prostředků dělat

Tady je několik příkladů toho, co můžete dosáhnout pomocí vlastních poskytovatelů prostředků Azure:

- Rozšíří Azure Resource Manager REST API tak, aby zahrnovalo interní a externí služby.
- Povolte vlastní scénáře nad stávajícími pracovními postupy Azure.
- Přizpůsobení ovládacího prvku a efektu šablon Azure Resource Manager.

## <a name="what-is-a-custom-resource-provider"></a>Co je vlastní poskytovatel prostředků

Vlastní poskytovatelé prostředků Azure se vytvářejí vytvořením kontraktu mezi Azure a koncovým bodem. Tato Smlouva definuje seznam nových prostředků a akcí pomocí nového prostředku, **Microsoft. CustomProviders/resourceProviders**. Vlastní poskytovatel prostředků pak tato nová rozhraní API zveřejní v Azure. Vlastní poskytovatelé prostředků Azure se skládají ze tří částí: vlastní poskytovatel prostředků, **koncové body**a vlastní prostředky.

## <a name="how-to-build-custom-resource-providers"></a>Sestavování vlastních poskytovatelů prostředků

Vlastní poskytovatelé prostředků jsou seznam smluv mezi Azure a koncovými body. Tato smlouva popisuje, jak by měl Azure spolupracovat s koncovým bodem. Poskytovatel prostředků funguje jako proxy a předává žádosti a odpovědi na zadaný **koncový bod**a. Poskytovatel prostředků může určit dva typy [**smluv: prostředky**](./custom-providers-resources-endpoint-how-to.md) a [**Akce**](./custom-providers-action-endpoint-how-to.md). Tyto jsou povolené prostřednictvím definic koncových bodů. Definice koncového bodu se skládá ze tří polí: **název**, **routingType**a **koncový bod**.

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
name | *Ano* | Název definice koncového bodu. Azure zveřejní tento název prostřednictvím rozhraní API v části/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *ne* | Určuje typ kontraktu s **koncovým bodem**. Pokud není zadaný, použije se výchozí hodnota "proxy".
endpoint | *Ano* | Koncový bod, do kterého mají být směrovány požadavky. Tím se zpracuje odpověď i všechny vedlejší účinky žádosti.

### <a name="building-custom-resources"></a>Sestavování vlastních prostředků

Resources **popisují nové** vlastní prostředky, které se přidají do Azure. Ty zveřejňují základní metody RESTful CRUD. [Další informace o vytváření vlastních prostředků](./custom-providers-resources-endpoint-how-to.md)

Ukázkový vlastní poskytovatel prostředků s prostředky k **dissourcetype**:

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

Rozhraní API přidané do Azure pro výše uvedenou ukázku:

HttpMethod | Ukázkový identifikátor URI | Popis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-Preview | Azure REST API volání pro vytvoření nového prostředku.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-Preview | Volání Azure REST API k odstranění existujícího prostředku.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-Preview | Rozhraní Azure REST API volá načtení existujícího prostředku.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018-09 -01-Preview | Rozhraní Azure REST API volá načtení seznamu existujících prostředků.

### <a name="building-custom-actions"></a>Sestavování vlastních akcí

**Akce** popisují nové akce, které se přidají do Azure. Můžou být vystavené na poskytovateli prostředků nebo můžou být vnořené pod možností **ResourceType**. [Další informace o vytváření vlastních akcí](./custom-providers-action-endpoint-how-to.md)

Ukázkový vlastní poskytovatel prostředků s **akcemi**:

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

Rozhraní API přidané do Azure pro výše uvedenou ukázku:

HttpMethod | Ukázkový identifikátor URI | Popis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-Version = 2018-09 -01-Preview | Volání služby Azure REST API k aktivaci akce.

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy pro vývoj vlastních poskytovatelů prostředků Azure, zkuste požádat o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte značku ```azure-custom-providers``` , abyste získali rychlou odpověď.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastních poskytovatelích. Pokud chcete vytvořit vlastního poskytovatele, přečtěte si další článek.

- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
