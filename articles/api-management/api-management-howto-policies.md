---
title: Zásady ve službě Azure API Management | Microsoft Docs
description: Naučte se vytvářet, upravovat a konfigurovat zásady ve službě API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 54fbba197f6609731ffaf3ff15143a28e70a955f
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29712864"
---
# <a name="policies-in-azure-api-management"></a>Zásady ve službě Azure API Management

V Azure API Management (APIM), zásady jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které jsou prováděny postupně v požadavku nebo odezvy z rozhraní API. Oblíbené příkazy patří převod formátu XML do formátu JSON a četnosti omezení omezit množství příchozích volání od vývojáře. Jsou dostupné ihned mnoho další zásady.

Jsou zásady použity uvnitř bránu, která je umístěna mezi příjemce rozhraní API a spravované rozhraní API. Brána přijme všechny požadavky a obvykle předává je do základního rozhraní API v nezměněném stavu. Zásady ale můžete použít změny požadavek příchozí i odchozí odpovědi.

Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, jako [řízení toku] [ Control flow] a [nastavená proměnná] [ Set variable] jsou založené na výrazech zásad. Další informace najdete v tématu [pokročilé zásady] [ Advanced policies] a [výrazy zásad][Policy expressions].

## <a name="sections"> </a>Principy zásad konfigurace

Definice zásady je jednoduchý dokument XML, který popisuje posloupnost příchozí a odchozí příkazy. Soubor XML můžete upravovat přímo v okně definice. Seznam příkazů jsou poskytovány doprava a jsou povolené a zvýrazní příkazů pro aktuální obor.

Kliknutím na povoleno příkaz přidá příslušný kód XML v umístění kurzoru v definici zobrazení. 

> [!NOTE]
> Pokud není povoleno zásadami, které chcete přidat, ujistěte se, že jste ve správném oboru pro tuto zásadu. Každý prohlášení o zásadách je určená pro použití v určité obory a zásad oddílů. Chcete-li zkontrolovat zásady části a obory pro zásadu, zkontrolujte **využití** oddíl pro tuto zásadu v [referenční informace o zásadách][Policy Reference].
> 
> 

Konfigurace je rozdělené do `inbound`, `backend`, `outbound`, a `on-error`. Řadu příkazy určené zásadě se spustí v pořadí pro požadavek a odpověď.

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

Pokud dojde k chybě během zpracování požadavku, všechny zbývající kroky `inbound`, `backend`, nebo `outbound` oddíly se přeskočí a provádění přejde na příkazy v `on-error` části. Tím, že příkazy zásad v `on-error` části Chyba můžete zkontrolovat pomocí `context.LastError` vlastnost, zkontrolovat a upravit pomocí odpovědi chyba `set-body` zásady a nakonfigurovat, co se stane, když dojde k chybě. Existují kódy chyb pro vestavěné kroky a chyby, které mohou nastat při zpracovávání příkazy zásad. Další informace najdete v tématu [zpracování chyb v zásady služby API Management](https://msdn.microsoft.com/library/azure/mt629506.aspx).

## <a name="scopes"> </a>Postup konfigurace zásad

Informace o tom, jak nakonfigurovat zásady najdete v tématu [nastavit nebo upravit zásady](set-edit-policies.md).

## <a name="policy-reference"></a>Referenční informace o zásadách

Najdete v článku [referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení.

## <a name="policy-samples"></a>Ukázky zásad

V tématu [ukázky zásad](policy-samples.md) Další příklady kódu.

## <a name="examples"></a>Příklady

### <a name="apply-policies-specified-at-different-scopes"></a>Použít zásady zadaný na různých místech

Pokud máte zásadu na globální úrovni a zásada nakonfigurovaná pro rozhraní API, pak vždy, když se používá toto rozhraní API konkrétní obě zásady se použijí. API Management umožňuje deterministickou řazení příkazy kombinované zásad prostřednictvím base element. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

V definici zásady příkladu výše `cross-domain` příkaz by provést před všechny vyšší zásady, které by naopak následovat `find-and-replace` zásad. 

### <a name="restrict-incoming-requests"></a>Omezit příchozí požadavky

Chcete-li přidat nové prohlášení omezit příchozí požadavky na zadané IP adresy, umístěte kurzor pouze uvnitř obsah `inbound` – element XML a klikněte na tlačítko **volající omezení IP adres** příkaz.

![Zásady omezení][policies-restrict]

Bude přidáno fragmentu kódu XML do `inbound` element, který poskytuje pokyny o tom, jak nakonfigurovat příkaz.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Omezit příchozí požadavky a přijímat pouze ty z IP adresy 1.2.3.4 upravte soubor XML následujícím způsobem:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
