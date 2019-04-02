---
title: Zásady ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet, upravovat a konfigurovat zásady ve službě API Management.
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
ms.openlocfilehash: 99f756b5415811b3d4c2ee0167f98b31c905df1a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793643"
---
# <a name="policies-in-azure-api-management"></a>Zásady ve službě Azure API Management

V Azure API Management (APIM), zásady jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které jsou spouštěny postupně na požadavku nebo odezvy z rozhraní API. Oblíbené příkazy patří převod formátu XML do formátu JSON a četnosti omezení omezit množství příchozích volání od vývojáře. Mnoho více zásad jsou dostupné ihned.

Zásady se použijí v bránu, která je umístěna mezi spotřebiteli rozhraní API a spravovaných rozhraní API. Brána přijme všechny požadavky a obvykle předává je beze změny základního rozhraní API. Zásady ale provést změny u příchozího požadavku i odchozí odpovědi.

Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, jako [řízení toku] [ Control flow] a [nastavená proměnná] [ Set variable] jsou založené na výrazech zásad. Další informace najdete v tématu [pokročilé zásady] [ Advanced policies] a [výrazy zásad][Policy expressions].

## <a name="sections"> </a>Principy zásad konfigurace

Definice zásad není jednoduché dokumentu XML, který popisuje pořadí příkazů příchozí a odchozí. Soubor XML můžete upravit přímo v okně definice. Seznam příkazů, kterou vpravo, jsou povolené a zvýrazní příkazy aktuálního oboru.

Kliknutím na příkaz povoleno přidat odpovídající kód XML na pozici kurzoru v definici zobrazení. 

> [!NOTE]
> Pokud není povolena zásadami, které chcete přidat, ujistěte se, že jste ve správném oboru pro tuto zásadu. Každý prohlášení o zásadách je určená pro použití v určité obory a části zásad. Chcete-li zkontrolovat zásady oddíly a obory pro zásadu, zkontrolujte **využití** oddíl pro tuto zásadu v [referenční příručce o zásadách][Policy Reference].
> 
> 

Konfigurace se dělí `inbound`, `backend`, `outbound`, a `on-error`. Řadu příkazů určené zásadě se spustí mohl požadavek a odpověď.

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

Pokud dojde k chybě během zpracování požadavku, všechny zbývající kroky `inbound`, `backend`, nebo `outbound` oddíly se přeskočí a spuštění přejde do příkazů v `on-error` části. Tak, že zásady příkazů v `on-error` části chybu můžete zkontrolovat pomocí `context.LastError` vlastnost, zkontrolovat a upravit pomocí chybová odpověď `set-body` zásady a nakonfigurujte, co se stane, když dojde k chybě. Kódy chyb pro vestavěné kroky a chyby, které mohou nastat při zpracování zásady příkazy nejsou k dispozici. Další informace najdete v tématu [zpracování chyb v zásady služby API Management](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Jak nakonfigurovat zásady

Informace o tom, jak nakonfigurovat zásady, najdete v části [nastavení nebo úprava zásad](set-edit-policies.md).

## <a name="policy-reference"></a>Referenční příručce o zásadách

Zobrazit [referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení.

## <a name="policy-samples"></a>Ukázky zásad

Zobrazit [ukázky zásad](policy-samples.md) Další příklady kódu.

## <a name="examples"></a>Příklady

### <a name="apply-policies-specified-at-different-scopes"></a>Použít zásady zadaná v různých oborech

Pokud máte zásadu na globální úrovni a zásady nakonfigurované pro rozhraní API, potom při každém použití daného rozhraní API obě zásady se použijí. API Management umožňuje deterministické pořadí příkazů kombinované zásady prostřednictvím base element. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

V definici zásad příklad výše `cross-domain` příkazu by běžet, než všechny vyšší zásady, které by naopak být následován znakem `find-and-replace` zásad. 

### <a name="restrict-incoming-requests"></a>Omezit příchozí žádosti

Chcete-li přidat nový příkaz omezit příchozí požadavky na konkrétní IP adresy, umístěte kurzor pouze mezi obsah `inbound` – element XML a klikněte na tlačítko **volající omezení IP adres** příkaz.

![Zásady omezení][policies-restrict]

Tato možnost přidá fragment kódu XML, který `inbound` element, který poskytuje pokyny o tom, jak konfigurovat příkaz.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Chcete-li omezit příchozí požadavky a přijme že jenom na ty z IP adresy 1.2.3.4 upravením kódu XML následujícím způsobem:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
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
