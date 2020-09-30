---
title: Pokročilé omezování požadavků pomocí služby Azure API Management
description: Naučte se vytvářet a používat flexibilní zásady kvót a četnosti s využitím Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: ad1ad622b354215e9837b1154a13bac148d54164
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537340"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Pokročilé omezování požadavků pomocí služby Azure API Management
Schopnost omezit příchozí požadavky je klíčovou rolí Azure API Management. Díky tomu, že se řídí rychlost požadavků nebo celkový počet přenesených požadavků nebo dat, API Management umožňuje poskytovatelům rozhraní API chránit svá rozhraní API před zneužitím a vytvářet hodnoty pro různé úrovně produktu API.

## <a name="rate-limits-and-quotas"></a>Omezení přenosové rychlosti a kvóty
Omezení přenosové rychlosti a kvóty se používají pro různé účely.

### <a name="rate-limits"></a>Omezení přenosové rychlosti
Omezení přenosové rychlosti se obvykle používají k ochraně před krátkými a intenzivními shluky. Pokud například víte, že vaše back-end služba má v databázi kritický problém s vysokým objemem volání, můžete nastavit `rate-limit-by-key` zásady tak, aby nepovolovaly svazek s vysokým voláním pomocí tohoto nastavení.

### <a name="quotas"></a>Kvóty
Kvóty se obvykle používají pro řízení sazeb volání během delšího časového období. Můžou například nastavit celkový počet volání, která může určitý odběratel provést během daného měsíce. Pro Monetizing vašeho rozhraní API je možné kvóty nastavit také odlišně pro odběry založené na úrovni. Například předplatné na úrovni Basic může být schopné provést maximálně 10 000 volání za měsíc, ale úroveň Premium může každý měsíc jít až o 100 000 000 volání.

V rámci Azure API Management se limity přenosové rychlosti obvykle rozšiřují v rámci uzlů na ochranu před špičkami. Naproti tomu se informace o kvótě využití používají po delší dobu, takže její implementace je odlišná.

> [!CAUTION]
> Vzhledem k distribuované povaze architektury omezování není omezení rychlosti nikdy zcela přesné. Rozdíl mezi nakonfigurovaným a skutečným počtem povolených požadavků se liší v závislosti na objemu a míře požadavků, latenci back-endu a dalších faktorech.

## <a name="product-based-throttling"></a>Omezování na základě produktů
Do data jsou možnosti omezování míry omezené, aby byly vymezeny na konkrétní předplatné produktu definované v Azure Portal. To je užitečné pro poskytovatele rozhraní API pro použití omezení u vývojářů, kteří se zaregistrovali, aby používali své rozhraní API, ale nemůžete například při omezování jednotlivých koncových uživatelů rozhraní API. Je možné, že jednotliví uživatelé aplikace vývojáře budou využívat celou kvótu a pak můžou ostatním zákazníkům vývojářům zabránit v používání aplikace. Několik zákazníků, kteří by mohli vygenerovat velký počet požadavků, může také omezit přístup k příležitostnému uživateli.

## <a name="custom-key-based-throttling"></a>Vlastní omezování na základě klíčů

> [!NOTE]
> `rate-limit-by-key`Zásady a `quota-by-key` nejsou k dispozici, když je ve vrstvě spotřeby API Management Azure. 

Nové zásady [sazeb-limit-by-Key](./api-management-access-restriction-policies.md#LimitCallRateByKey) a [Quota-by-Key](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) poskytují pružnější řešení řízení provozu. Tyto nové zásady umožňují definovat výrazy k identifikaci klíčů, které se používají ke sledování využití provozu. Způsob, jak to funguje, je nejjednodušší příklad. 

## <a name="ip-address-throttling"></a>Omezování IP adres
Následující zásady omezují jednu IP adresu klienta na jenom 10 volání každou minutu, celkem 1 000 000 volání a 10 000 kilobajtů šířky pásma měsíčně. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Pokud všichni klienti na Internetu používali jedinečnou IP adresu, může to být účinný způsob, jakým uživatel omezuje využití. Je však možné, že více uživatelů sdílí jednu veřejnou IP adresu, protože přistupuje k Internetu prostřednictvím zařízení NAT. I když to `IpAddress` může být nejlepší volbou pro rozhraní API, která umožňují neověřený přístup.

## <a name="user-identity-throttling"></a>Omezení identity uživatele
Pokud je koncový uživatel ověřený, je možné vygenerovat klíč omezování na základě informací, které tento uživatel jednoznačně identifikují.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Tento příklad ukazuje, jak extrahovat autorizační hlavičku, převést ji na `JWT` Object a použít předmět tokenu k identifikaci uživatele a použít ho jako klíč omezující rychlost. Pokud je identita uživatele uložená v `JWT` jako jedna z ostatních deklarací identity, pak se tato hodnota dá použít na svém místě.

## <a name="combined-policies"></a>Kombinované zásady
I když nové zásady omezování poskytují větší kontrolu než stávající zásady omezování, stále je kombinována i hodnota obou možností. Omezení podle klíče předplatného produktu ([Omezení četnosti volání podle](./api-management-access-restriction-policies.md#LimitCallRate) předplatného a [nastavení kvóty využití podle předplatného](./api-management-access-restriction-policies.md#SetUsageQuota)) je skvělým způsobem, jak povolit Monetizing rozhraní API na základě úrovní využití. Přesnější kontrolu nad tím, jak je možné omezit uživatele, je doplňkové a brání chování jednoho uživatele v důsledku zhoršení prostředí jiného. 

## <a name="client-driven-throttling"></a>Omezování na základě klientů
Pokud je klíč omezení definovaný pomocí [výrazu zásady](./api-management-policy-expressions.md), pak se jedná o poskytovatele rozhraní API, který zvolí způsob, jakým je vymezený rozsah omezování. Vývojář ale může chtít určit, jak bude tato rychlost omezovat svým zákazníkům. To může poskytovatel rozhraní API povolit tím, že zavádí vlastní hlavičku, která klientské aplikaci vývojářů umožní komunikovat klíč k rozhraní API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Tím umožníte klientským aplikacím vývojáře zvolit, jak chce vytvořit klíč omezující rychlost. Vývojáři klientů mohou vytvořit své vlastní úrovně sazeb přidělením sad klíčů uživatelům a otočením použití klíče.

## <a name="summary"></a>Shrnutí
Azure API Management poskytuje rychlost a cenovou omezení pro ochranu a přidání hodnoty do služby API. Nové zásady omezování s vlastními pravidly oboru vám umožní přesnější kontrolu nad těmito zásadami, aby vaši zákazníci mohli vytvářet ještě lepší aplikace. Příklady v tomto článku ukazují použití těchto nových zásad podle sazeb za zpracovatelských procesů s IP adresami klienta, identitou uživatelů a hodnotami generovanými klientem. Existuje však mnoho dalších částí zprávy, které by mohly být použity jako uživatelský agent, fragmenty cesty URL a velikost zprávy.

## <a name="next-steps"></a>Další kroky
Sdělte nám svůj názor jako problém GitHubu pro toto téma. Měli byste se seznámit s dalšími potenciálními klíčovými hodnotami, které jsou ve vašich scénářích logickou volbou.
