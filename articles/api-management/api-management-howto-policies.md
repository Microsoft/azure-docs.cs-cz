---
title: Zásady ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Naučte se vytvářet, upravovat a konfigurovat zásady ve správě rozhraní API.
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
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072307"
---
# <a name="policies-in-azure-api-management"></a>Zásady ve službě Azure API Management

Ve správě rozhraní API Azure (APIM) jsou zásady mocnou funkcí systému, která vydavateli umožňuje změnit chování rozhraní API prostřednictvím konfigurace. Zásady jsou kolekce příkazů, které jsou prováděny postupně na požadavek nebo odpověď rozhraní API. Mezi oblíbené příkazy patří převod formátu z XML na JSON a omezení rychlosti volání, která omezuje množství příchozích hovorů od vývojáře. Mnoho dalších zásad je k dispozici izokrabice.

Zásady se používají uvnitř brány, která je mezi příjemcem rozhraní API a spravované rozhraní API. Brána přijímá všechny požadavky a obvykle je předává beze změny do základního rozhraní API. Zásada však může použít změny jak příchozí požadavek a odchozí odpověď.

Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku][Control flow] a [nastavená proměnná][Set variable], jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady][Advanced policies] a [Výrazy zásad][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Principy konfigurace zásad

Definice zásady je jednoduchý dokument XML, který popisuje posloupnost příchozích a odchozích příkazů. XML lze upravovat přímo v definičním okně. Seznam výpisů je k dispozici vpravo a příkazy použitelné pro aktuální obor jsou povoleny a zvýrazněny.

Kliknutím na povolený příkaz přidáte příslušný xml v umístění kurzoru v zobrazení definice. 

> [!NOTE]
> Pokud zásady, které chcete přidat, není povolena, ujistěte se, že jste ve správném oboru pro tuto zásadu. Každé prohlášení o zásadách je navrženo pro použití v určitých oborech a oddílech zásad. Chcete-li zkontrolovat oddíly zásad a obory zásad, podívejte se do části **Použití** pro tuto zásadu v [odkazu na zásady][Policy Reference].
> 
> 

Konfigurace je rozdělena `inbound` `backend`na `outbound`, `on-error`, a . Řada zadaných příkazů zásad je provedena v pořadí pro požadavek a odpověď.

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

Pokud dojde k chybě během zpracování požadavku, všechny zbývající `inbound` `backend`kroky `outbound` v , nebo oddíly jsou přeskočeny a spuštění přeskočí na příkazy v `on-error` části. Umístěním příkazů zásad `on-error` v části můžete zkontrolovat `context.LastError` chybu pomocí vlastnosti, zkontrolovat `set-body` a přizpůsobit odpověď na chybu pomocí zásady a nakonfigurovat, co se stane, pokud dojde k chybě. Existují kódy chyb pro předdefinované kroky a chyby, ke kterým může dojít během zpracování příkazů zásad. Další informace naleznete [v tématu Zpracování chyb v zásadách správy rozhraní API](/azure/api-management/api-management-error-handling-policies).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Konfigurace zásad

Informace o konfiguraci zásad naleznete v tématu [Nastavení nebo úpravy zásad](set-edit-policies.md).

## <a name="policy-reference"></a>Odkaz na zásady

Úplný seznam prohlášení o zásadách a jejich nastavení naleznete v [odkazu na zásady.](api-management-policy-reference.md)

## <a name="policy-samples"></a>Ukázky zásad

Další příklady kódu najdete v [tématu Ukázky](policy-samples.md) zásad.

## <a name="examples"></a>Příklady

### <a name="apply-policies-specified-at-different-scopes"></a>Použití zásad určených v různých oborech

Pokud máte zásady na globální úrovni a zásady nakonfigurované pro rozhraní API, pak při každém použití tohoto konkrétního rozhraní API budou použity obě zásady. Správa rozhraní API umožňuje deterministické řazení kombinovaných příkazů zásad prostřednictvím základního prvku. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

V příkladu definice zásad `cross-domain` výše by prohlášení provést před vyšší politiky, `find-and-replace` které by zase následovat zásady. 

### <a name="restrict-incoming-requests"></a>Omezení příchozích požadavků

Chcete-li přidat nový příkaz pro omezení příchozích požadavků na zadané adresy IP, umístěte kurzor přímo do obsahu elementu `inbound` XML a klepněte na příkaz Omezit ip adresy **volajícího.**

![Omezení][policies-restrict]

Tím přidáte fragment XML do elementu, `inbound` který poskytuje pokyny ke konfiguraci příkazu.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Chcete-li omezit příchozí požadavky a přijmout pouze požadavky z adresy IP 1.2.3.4, upravte xml následujícím způsobem:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
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
