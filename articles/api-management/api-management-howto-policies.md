---
title: Zásady v Azure API Management | Microsoft Docs
description: Naučte se vytvářet, upravovat a konfigurovat zásady v API Management. Podívejte se na příklady kódu a zobrazte další dostupné prostředky.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 3886f82180728bc6f454b1a49b5551bc4831869e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070890"
---
# <a name="policies-in-azure-api-management"></a>Zásady ve službě Azure API Management

V Azure API Management (APIM) jsou zásady účinnou funkcí systému, které umožňují vydavateli změnit chování rozhraní API prostřednictvím konfigurace. Zásady jsou kolekce příkazů, které jsou spouštěny postupně na žádost nebo na reakci rozhraní API. Mezi oblíbené příkazy patří převod formátu z XML na JSON a omezení rychlosti volání, aby se omezilo množství příchozích volání od vývojáře. Mnoho dalších zásad je dostupných mimo box.

Zásady se aplikují v bráně, která je umístěná mezi příjemcem rozhraní API a spravovaným rozhraním API. Brána obdrží všechny požadavky a obvykle je předává beze změny na základní rozhraní API. Zásady ale můžou použít změny v příchozím požadavku i v odchozí odpovědi.

Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku][Control flow] a [nastavená proměnná][Set variable], jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady][Advanced policies] a [Výrazy zásad][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Principy konfigurace zásad

Definice zásady je jednoduchý dokument XML, který popisuje sekvenci příchozích a odchozích příkazů. KÓD XML lze upravit přímo v okně definice. Vpravo a příkazy, které se vztahují k aktuálnímu oboru, jsou povoleny a zvýrazněny v seznamu příkazů.

Kliknutím na příkaz Enabled přidáte příslušný kód XML do umístění kurzoru v zobrazení definice. 

> [!NOTE]
> Pokud není povolená zásada, kterou chcete přidat, ujistěte se, že jste ve správném oboru pro tyto zásady. Jednotlivé příkazy zásad jsou určené pro použití v určitých oborech a oddílech zásad. Pokud chcete zkontrolovat oddíly a obory zásad pro zásady, podívejte se v části **použití** této zásady v tématu [referenční informace o zásadách][Policy Reference].
> 
> 

Konfigurace je rozdělena do `inbound` , `backend` , `outbound` a `on-error` . Řada zadaných příkazů zásad se spustí v pořadí podle požadavku a odpovědi.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Pokud během zpracování požadavku dojde k chybě, všechny zbývající kroky v `inbound` , `backend` nebo se přeskočí `outbound` a provádění přejde na příkazy v `on-error` části. Vložením příkazů zásad v `on-error` části můžete zkontrolovat chybu pomocí `context.LastError` vlastnosti, zkontrolovat a přizpůsobit chybovou odpověď pomocí `set-body` zásad a nakonfigurovat, co se stane, když dojde k chybě. K dispozici jsou kódy chyb pro integrované kroky a chyby, ke kterým může dojít během zpracování příkazů zásad. Další informace najdete v tématu [zpracování chyb v zásadách API Management](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Postup konfigurace zásad

Informace o tom, jak nakonfigurovat zásady, najdete v tématu [nastavení nebo úprava zásad](set-edit-policies.md).

## <a name="policy-reference"></a>Reference k zásadám

Úplný seznam příkazů zásad a jejich nastavení najdete v [referenčních informacích k zásadám](./api-management-policies.md) .

## <a name="policy-samples"></a>Ukázky zásad

Další příklady kódu najdete v tématu [ukázky zásad](./policy-reference.md) .

## <a name="examples"></a>Příklady

### <a name="apply-policies-specified-at-different-scopes"></a>Použití zásad zadaných v různých oborech

Pokud máte zásadu na globální úrovni a zásadu nakonfigurovanou pro rozhraní API, pak se při každém použití konkrétního rozhraní API použijí obě zásady. API Management umožňuje deterministické řazení kombinovaných příkazů zásad prostřednictvím základního prvku. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

V příkladu výše uvedené definice zásad se `cross-domain` příkaz spustí před všemi vyššími zásadami, které by pak následovala `find-and-replace` zásada. 

### <a name="restrict-incoming-requests"></a>Omezit příchozí požadavky

Chcete-li přidat nový příkaz pro omezení příchozích požadavků na zadané IP adresy, umístěte kurzor přímo do obsahu `inbound` elementu XML a klikněte na příkaz omezit počet **IP adres volajícího** .

![Zásady omezení][policies-restrict]

Tím se přidá fragment kódu XML k `inbound` elementu, který poskytuje pokyny ke konfiguraci příkazu.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Chcete-li omezit příchozí požadavky a přijmout pouze ty z IP adresy 1.2.3.4, upravte XML následujícím způsobem:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png