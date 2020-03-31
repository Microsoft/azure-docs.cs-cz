---
title: Pokročilé omezování požadavků pomocí služby Azure API Management
description: Naučte se vytvářet a používat flexibilní zásady kvót a omezení rychlosti pomocí Azure API Management.
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
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066752"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Pokročilé omezování požadavků pomocí služby Azure API Management
Možnost omezení příchozích požadavků je klíčovou úlohou správy rozhraní AZURE API. Buď řízením rychlosti požadavků nebo celkového počtu přenesených požadavků/dat umožňuje správa rozhraní API poskytovatelům rozhraní API chránit jejich rozhraní API před zneužitím a vytvářet hodnotu pro různé úrovně produktů rozhraní API.

## <a name="product-based-throttling"></a>Omezení na základě produktu
K dnešnímu dni možnosti omezení rychlosti byly omezeny na rozsah na konkrétní předplatné produktu, definované na webu Azure Portal. To je užitečné pro poskytovatele rozhraní API použít omezení na vývojáře, kteří se zaregistrovali k použití jejich rozhraní API, ale nepomůže, například v omezení jednotlivých koncových uživatelů rozhraní API. Je možné, že pro jednoho uživatele aplikace vývojáře využívat celou kvótu a pak zabránit ostatním zákazníkům vývojáře z moci používat aplikaci. Také několik zákazníků, kteří mohou generovat velký objem požadavků, může omezit přístup příležitostným uživatelům.

## <a name="custom-key-based-throttling"></a>Omezení založené na vlastních klíčích

> [!NOTE]
> `rate-limit-by-key` Zásady `quota-by-key` a nejsou k dispozici, když v úrovni spotřeby Azure API Management. 

Nové [zásady omezení rychlosti podle klíče](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) a [kvóty podle klíče](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) poskytují flexibilnější řešení řízení provozu. Tyto nové zásady umožňují definovat výrazy k identifikaci klíčů, které se používají ke sledování využití provozu. Způsob, jakým to funguje, je nejjednodušší ilustrovaný s příkladem. 

## <a name="ip-address-throttling"></a>Omezení adresy IP
Následující zásady omezují jednu ip adresu klienta na pouze 10 volání za minutu, s celkem 1 000 000 volání a 10 000 kilobajtů šířky pásma za měsíc. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Pokud všichni klienti v Internetu používali jedinečnou adresu IP, může to být účinný způsob, jak omezit využití uživatelem. Je však pravděpodobné, že více uživatelů sdílí jednu veřejnou IP adresu kvůli tomu, že přistupují k Internetu prostřednictvím zařízení NAT. Navzdory tomu pro api, které umožňují `IpAddress` neověřený přístup může být nejlepší volbou.

## <a name="user-identity-throttling"></a>Omezení identity uživatele
Pokud je koncový uživatel ověřen, pak lze vygenerovat klíč omezení na základě informací, které jednoznačně identifikují tohoto uživatele.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Tento příklad ukazuje, jak extrahovat `JWT` hlavičku Autorizace, převést ji na objekt a použít předmět tokenu k identifikaci uživatele a použít jej jako klíč omezující rychlost. Pokud je identita uživatele `JWT` uložena jako jeden z dalších deklarací, může být tato hodnota použita na jejím místě.

## <a name="combined-policies"></a>Kombinované zásady
Přestože nové zásady omezení poskytují větší kontrolu než existující zásady omezení, je stále hodnota kombinující obě možnosti. Omezení podle klíče předplatného produktu[(Omezit míru volání podle předplatného](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) a Nastavit [kvótu využití podle předplatného](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) je skvělý způsob, jak povolit zpeněžení rozhraní API účtováním na základě úrovní využití. Jemnější odstupňované řízení, které je možné omezit uživatelem, se doplňuje a zabraňuje chování jednoho uživatele v degradaci zkušeností jiného uživatele. 

## <a name="client-driven-throttling"></a>Omezení řízené klientem
Pokud je klíč omezení definován pomocí [výrazu zásady](/azure/api-management/api-management-policy-expressions), pak je zprostředkovatelrozhraní ROZHRANÍ API, který vybírá způsob, jakým je omezení vymezeno. Vývojář však může chtít řídit, jak rychlost omezuje své vlastní zákazníky. To by mohlo být povoleno poskytovatelem rozhraní API zavedením vlastní záhlaví, aby klientská aplikace vývojáře komunikovat klíč rozhraní API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

To umožňuje klientské aplikaci vývojáře zvolit, jak chtějí vytvořit klíč omezující rychlost. Vývojáři klienta mohou vytvářet vlastní úrovně sazeb přidělením sad klíčů uživatelům a otočením využití klíče.

## <a name="summary"></a>Souhrn
Azure API Management poskytuje omezení rychlosti a nabídky pro ochranu a přidanou hodnotu pro vaši službu rozhraní API. Nové zásady omezení s vlastními pravidly oboru umožňují jemnější odstupňovanou kontrolu nad těmito zásadami, aby vaši zákazníci mohli vytvářet ještě lepší aplikace. Příklady v tomto článku ukazují použití těchto nových zásad pomocí klíče omezující rychlost s IP adresami klienta, identitou uživatele a klientem generovanými hodnotami. Existuje však mnoho dalších částí zprávy, které by mohly být použity, jako je například uživatelský agent, fragmenty cesty URL, velikost zprávy.

## <a name="next-steps"></a>Další kroky
Sdělte nám prosím svůj názor jako problém GitHubu pro toto téma. Bylo by skvělé slyšet o dalších potenciálních klíčových hodnot, které byly logickou volbou ve vašich scénářích.

