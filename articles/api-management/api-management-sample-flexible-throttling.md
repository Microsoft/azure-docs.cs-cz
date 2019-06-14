---
title: Pokročilé omezování požadavků pomocí Azure API Management
description: Zjistěte, jak vytvořit a použít flexibilní kvóty a omezení zásad ve službě Azure API Management sazby.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61087119"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Pokročilé omezování požadavků pomocí Azure API Management
Možnost omezit příchozí žádosti je klíčovou roli služby Azure API Management. Buď pomocí řízení frekvence požadavků nebo celkový počet požadavků nebo přenesená data, API Management umožňuje poskytovateli rozhraní API pro své rozhraní API umožňuje chránit proti zneužití a vytvořit hodnotu pro různé úrovně rozhraní API produktu.

## <a name="product-based-throttling"></a>Produkt na základě omezení
Do dnešního dne sazba omezení funkce byly omezené je omezená na konkrétní předplatné produktu, definované na portálu Azure portal. To je užitečné pro zprostředkovatele rozhraní API nastavit omezení pro vývojáře, kteří si zaregistrovali k používání jejich rozhraní API, ale to nepomůže, například v omezení jednotlivých koncovým uživatelům rozhraní API. Je možné, že pro jednotlivé uživatele aplikace pro vývojáře spotřebovat celý kvótu a potom znemožnit ostatním zákazníkům vývojáře k používání aplikace. Několik zákazníků, kteří mohou vytvořit velký objem požadavků může také omezit přístup k občasné uživatelů.

## <a name="custom-key-based-throttling"></a>Vlastní klíče na základě omezení
Nové [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) a [kvóty by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) zásady poskytují pružnější řešení do správy provozu. Tyto nové zásady umožňují definovat výrazy k identifikaci klíče, které se používají ke sledování využití provozu. Způsob, jakým tento postup funguje, je znázorněn nejsnažší s příkladem. 

## <a name="ip-address-throttling"></a>Omezení IP adres
Tyto zásady omezují IP adresu konkrétního klienta jenom 10 volání každou minutu, s celkem 1 000 000 volání a 10 000 kilobajtů šířky pásma za měsíc. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Pokud všichni klienti na Internetu použili jedinečnou IP adresu, může to být účinný způsob omezení využití ze strany uživatele. Je ale pravděpodobné, že jedna veřejná IP adresa z důvodu jejich přístup k Internetu přes zařízení NAT. sdílí několik uživatelů. Bez ohledu na to, pro rozhraní API, která povolit neověřený přístup `IpAddress` může být nejlepší možností.

## <a name="user-identity-throttling"></a>Omezení identity uživatele
Koncový uživatel je ověřen, mohou být generovány omezení klíč založený na informacích, které jednoznačně identifikuje uživatele.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Tento příklad ukazuje, jak extrahovat autorizační hlavičky, převeďte ho na `JWT` objektu a použít předmětem token k identifikaci uživatele, který budete používat jako omezení klíč. Pokud je identita uživatele uložen v `JWT` jako jeden další deklarace identity, pak tato hodnota se daly použít v jeho umístění.

## <a name="combined-policies"></a>Kombinované zásady
Přestože nové zásady omezování poskytují větší kontrolu než existující zásady omezování, je stále hodnota kombinaci obou možností. Omezení šířky pásma pomocí kódu product key předplatného ([omezení četnosti volání podle předplatného](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) a [nastavení kvóty využití podle předplatného](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) je skvělý způsob, jak povolit zhodnocovat rozhraní API tím podle úrovně využití. Přesnější možnosti řízení moci omezit uživatelem je pouze doplnění a zabraňuje snížení úrovně zkušeností jiného chování jednoho uživatele. 

## <a name="client-driven-throttling"></a>Klient řízené omezování
Při omezování klíč je definován pomocí [výraz zásady](/azure/api-management/api-management-policy-expressions), pak je poskytovatel rozhraní API, který je zvolíte, jak je vymezen omezení. Však vývojář chtít řídit, jak jsou omezení přenosové rychlosti jejich zákazníků. To může lze povolit ve zprostředkovateli rozhraní API zavedením vlastní hlavičky pro vývojáře klientská aplikace mohla komunikovat klíč k rozhraní API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

To umožňuje klientská aplikace pro vývojáře rozhodnout se, jak se mají vytvořit tady klíč. Vývojáři klienta mohou vytvořit své vlastní sazby úrovně přiřazovat uživatelům sady klíčů a otáčením použití klíče.

## <a name="summary"></a>Souhrn
Azure API Management poskytuje rychlost a nabídky omezování chránit i přínos pro vaši službu rozhraní API. Nové zásady omezování pomocí vlastních pravidel stanovení rozsahu inicializací umožňují jemnější kontrolu nad tyto zásady tak, aby vaši zákazníci vytvářet aplikace ještě lepší. V příkladech v tomto článku ukazují použití těchto nových zásad výrobní míru omezení klíče s klientských IP adres, identitu uživatele a klient vygeneruje hodnoty. Existují však mnoho částí zprávy, která se dá použít například uživatelský agent, fragmenty cestu adresy URL, velikost zprávy.

## <a name="next-steps"></a>Další postup
Prosím sdělte nám svůj názor v vlákna služby Disqus pro toto téma. By bylo skvělé informace o dalších potenciálních hodnoty klíče, které byly logickou volbou ve vašich scénářích.

