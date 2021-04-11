---
title: Jak funguje Azure Traffic Manager | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak Traffic Manager směrovat provoz pro zajištění vysokého výkonu a dostupnosti webových aplikací.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 376aa04228113c56f0f797f737833802c9eca021
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029484"
---
# <a name="how-traffic-manager-works"></a>Jak Traffic Manager funguje

Azure Traffic Manager umožňuje řídit distribuci provozu napříč koncovými body vaší aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager poskytuje dvě klíčové výhody:

- Distribuce provozu podle jedné z několika [metod směrování provozu](traffic-manager-routing-methods.md)
- [Průběžné monitorování stavu koncového bodu](traffic-manager-monitoring.md) a automatického převzetí služeb při selhání koncových bodů

Když se klient pokusí připojit se ke službě, musí nejprve přeložit název DNS služby na IP adresu. Potom se klient připojí k této IP adrese, aby získal přístup ke službě.

**Nejdůležitějším bodem, který je třeba pochopit, je, že Traffic Manager pracuje na úrovni DNS, která je na vrstvě aplikace (vrstva-7).**  Traffic Manager používá DNS k přímému směrování klientů na konkrétní koncové body služby na základě pravidel metody směrování provozu. Klienti se připojují k vybranému koncovému bodu **přímo**. Traffic Manager není proxy server nebo brána. Traffic Manager se nezobrazuje přenos procházející mezi klientem a službou.

## <a name="traffic-manager-example"></a>Příklad Traffic Manager

Společnost Contoso Corp vyvinula nový Partnerský portál. Adresa URL tohoto portálu je `https://partners.contoso.com/login.aspx` . Aplikace je hostována ve třech oblastech Azure. Pro zlepšení dostupnosti a maximalizace globálního výkonu používají Traffic Manager k distribuci klientského provozu do nejbližšího dostupného koncového bodu.

Chcete-li dosáhnout této konfigurace, proveďte následující kroky:

1. Nasaďte tři instance jejich služby. Názvy DNS těchto nasazení jsou "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" a "contoso-asia.cloudapp.net".
1. Vytvořte profil Traffic Manager s názvem "contoso.trafficmanager.net" a nakonfigurujte ho tak, aby používal metodu směrování přenosu dat "Performance" v rámci tří koncových bodů.
1. Nakonfigurujte svůj název domény individuální, ' partners.contoso.com ', aby odkazoval na ' contoso.trafficmanager.net ' pomocí záznamu DNS CNAME.

![Traffic Manager konfigurace DNS][1]

> [!NOTE]
> Při použití individuální domény s Azure Traffic Manager musíte použít CNAME k nasměrování názvu domény individuální na název domény Traffic Manager. Standardy DNS neumožňují vytvořit záznam CNAME ve vrcholu (nebo kořenovém adresáři) domény. Proto nemůžete vytvořit záznam CNAME pro ' contoso.com ' (někdy se mu říká doména "holé"). Záznam CNAME pro doménu můžete vytvořit pouze v části "contoso.com", například "www.contoso.com". Pokud chcete toto omezení obejít, doporučujeme hostovat doménu DNS na [Azure DNS](../dns/dns-overview.md) a pomocí [záznamů aliasů](../dns/tutorial-alias-tm.md) Ukázat na váš profil Traffic Manageru. Alternativně můžete pomocí jednoduchého přesměrování protokolu HTTP směrovat požadavky na "contoso.com" na alternativní název, jako je například "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak se klienti připojují pomocí Traffic Manager

Když klient požádá o stránku `https://partners.contoso.com/login.aspx` , provede následující kroky, aby vyřešil název DNS a navázal připojení:

![Vytvoření připojení pomocí Traffic Manager][2]

1. Klient odešle dotaz DNS do nakonfigurované rekurzivní služby DNS, aby přeložil název partners.contoso.com. Rekurzivní služba DNS, která se někdy označuje jako místní služba DNS, nehostuje domény DNS přímo. Místo toho klient mimo jiné nahraje práci se spojením různých autoritativních služeb DNS v Internetu, které jsou potřeba k překladu názvu DNS.
2. K překladu názvu DNS vyhledá rekurzivní služba DNS názvové servery pro doménu contoso.com. Potom kontaktuje tyto názvové servery a požádá o záznam DNS partners.contoso.com. Servery DNS contoso.com vracejí záznam CNAME, který odkazuje na contoso.trafficmanager.net.
3. V dalším kroku najde rekurzivní služba DNS názvové servery pro doménu trafficmanager.net, které poskytuje služba Azure Traffic Manager. Pak pošle požadavek na záznam DNS contoso.trafficmanager.net na tyto servery DNS.
4. Traffic Manager názvové servery obdrží požadavek. Zvolí koncový bod na základě:

    - Nakonfigurovaný stav každého koncového bodu (zakázané koncové body nejsou vraceny)
    - Aktuální stav každého koncového bodu, jak je stanoven Traffic Managermi kontrolami stavu. Další informace najdete v tématu [Traffic Manager monitorování koncového bodu](traffic-manager-monitoring.md).
    - Vybraná metoda směrování provozu. Další informace najdete v tématu [metody směrování Traffic Manager](traffic-manager-routing-methods.md).

5. Zvolený koncový bod se vrátí jako jiný záznam CNAME DNS. V takovém případě se vám budeme vracet contoso-eu.cloudapp.net.
6. V dalším kroku najde rekurzivní služba DNS názvové servery pro doménu cloudapp.net. Kontaktuje tyto názvové servery, aby požádaly o záznam DNS ' contoso-eu.cloudapp.net '. Vrátí se záznam DNS, který obsahuje IP adresu koncového bodu služby založeného na EU.
7. Rekurzivní služba DNS slučuje výsledky a vrátí jednu odpověď DNS klientovi.
8. Klient obdrží výsledky DNS a připojí se k dané IP adrese. Klient se připojí ke koncovému bodu služby Application Service přímo, nikoli prostřednictvím Traffic Manager. Vzhledem k tomu, že se jedná o koncový bod HTTPS, klient provede nezbytnou metodu handshake protokolu SSL/TLS a pak vytvoří požadavek HTTP GET na stránku/Login.aspx.

Rekurzivní služba DNS ukládá do mezipaměti odpovědi DNS, které obdrží. Překladač DNS v klientském zařízení ukládá do mezipaměti také výsledek. Ukládání do mezipaměti umožňuje rychlejší zodpovězení dalších dotazů DNS pomocí dat z mezipaměti, nikoli při dotazování na jiné názvové servery. Doba trvání mezipaměti je určena vlastností TTL (Time-to-Live) každého záznamu DNS. Výsledkem kratších hodnot je rychlejší vypršení platnosti mezipaměti, což znamená větší zpáteční přenos na Traffic Manager názvové servery. Delší hodnota znamená, že může trvat delší dobu, než se přesměruje provoz z neúspěšného koncového bodu. Traffic Manager umožňuje nakonfigurovat hodnotu TTL použitou v Traffic Manager odpovědích DNS tak, aby byla menší než 0 sekund a 2 147 483 647 sekund (maximální rozsah kompatibilní se [specifikací RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), což vám umožní zvolit hodnotu, která nejlépe vyvažuje požadavky vaší aplikace.

## <a name="faqs"></a>Nejčastější dotazy

* [Jaká IP adresa Traffic Manager použít?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Jaké typy přenosů se dají směrovat pomocí Traffic Manager?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Podporuje Traffic Manager "rychlé" relace?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Proč se mi při použití Traffic Manager zobrazuje chyba protokolu HTTP?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Jaký je dopad na výkon při používání Traffic Manager?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Jaké aplikační protokoly můžu používat s Traffic Manager?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Můžu použít Traffic Manager s názvem domény "holé"?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Považuje Traffic Manager adresa klientské podsítě při zpracování dotazů DNS?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Co je DNS TTL a jak má vliv na uživatele?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Jak vysoká nebo nízká můžu nastavit hodnotu TTL pro Traffic Manager odezvy?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Jak můžu pochopit objem dotazů přicházejících do svého profilu?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Další kroky

Další informace o Traffic Manager [monitorování koncového bodu a automatické převzetí služeb při selhání](traffic-manager-monitoring.md).

Přečtěte si další informace o [metodách směrování provozu](traffic-manager-routing-methods.md)Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png
