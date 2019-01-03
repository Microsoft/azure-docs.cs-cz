---
title: Obsah zásady klíčů ve službě Media Services – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou zásady obsahu klíčů a jejich použití Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f12632b20d516c81e21a50cfdda7e40d4163afc1
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742214"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Azure Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí služby Media Services můžete doručovat na vyžádání a živé obsah dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

V Azure Media Services v3 [zásad klíče k obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) umožňuje určit, jak je doručen koncovým klientům prostřednictvím součást Media Services klíč doručení klíče k obsahu. Další informace najdete v tématu [obsahu Přehled služby Endpoint protection](content-protection-overview.md).

Doporučuje se pro všechny své Assety znovu použít stejné ContentKeyPolicy. ContentKeyPolicies je možné aktualizovat, takže pokud ji chcete obměna klíčů pak můžete buď přidat nové ContentKeyPolicyOption na existující ContentKeyPolicy s omezení s tokenem pomocí nových klíčů. Nebo můžete aktualizovat primární ověřovací klíč a seznam klíčů alternativní ověření v existující zásady a možnost. Může trvat až 15 minut pro doručení klíče mezipaměti aktualizace a vyzvednutí aktualizované zásady.

## <a name="contentkeypolicy-definition"></a>Definice ContentKeyPolicy

Následující tabulka uvádí vlastnosti ContentKeyPolicy a umožňuje jejich definice.

|Název|Popis|
|---|---|
|id|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|Název prostředku.|
|Properties.Created |Datum vytvoření zásad|
|Properties.Description |Popis zásady.|
|properties.lastModified|Datum poslední změny zásad|
|Properties.Options |Možnosti zásad klíče.|
|properties.policyId|Starší verze ID zásad.|
|type|Typ prostředku.|

Kompletní definici, naleznete v tématu [obsahu zásady klíčů](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Služba Media Services podporuje následující možnosti dotazu OData pro ContentKeyPolicies: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Popis operátoru:

* EQ = rovno
* Ne = není rovno
* Ge = větší než nebo rovno
* Le = menší než nebo rovno
* Gt = je větší než
* Lt = menší než

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti může použít u vlastnosti ContentKeyPolicies: 

|Název|Filtr|Objednání|
|---|---|---|
|id|||
|jméno|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Created |Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Options |||
|properties.policyId|Eq, ne||
|type|||

### <a name="pagination"></a>Stránkování

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 10.

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. 

Pokud ContentKeyPolicies jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla.) 

Následující příklad jazyka C# ukazuje, jak zobrazit výčet prostřednictvím všech ContentKeyPolicies v účtu.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

ZBÝVAJÍCÍ příklady naleznete v tématu [obsahu klíč zásady – seznam](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>Další postup

[Použití dynamického šifrování AES-128 a doručení klíče služby](protect-with-aes128.md)

[Pomocí DRM dynamického šifrování a licence služby pro doručování](protect-with-drm.md)
