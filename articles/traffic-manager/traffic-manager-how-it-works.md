---
title: Jak Azure Traffic Manager funguje | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Traffic Manager směruje provoz pro vysoký výkon a dostupnost webových aplikací
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294854"
---
# <a name="how-traffic-manager-works"></a>Jak funguje Traffic Manager

Azure Traffic Manager umožňuje řídit distribuci provozu mezi koncovými body aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager poskytuje dvě klíčové výhody:

- Rozdělení provozu podle jedné z několika [metod směrování provozu](traffic-manager-routing-methods.md)
- [Průběžné sledování stavu koncového bodu](traffic-manager-monitoring.md) a automatické převzetí služeb při selhání koncových bodů

Když se klient pokusí připojit se ke službě, musí nejprve přeložit název DNS služby na IP adresu. Potom se klient připojí k této IP adrese, aby získal přístup ke službě.

**Nejdůležitějším bodem k pochopení je, že Traffic Manager pracuje na úrovni DNS.**  Traffic Manager používá službu DNS k přesměrování klientů na konkrétní koncové body služby na základě pravidel metody směrování provozu. Klienti se připojují k vybranému koncovému bodu **přímo**. Traffic Manager není proxy nebo brána. Traffic Manager nevidí přenosy procházející mezi klientem a službou.

## <a name="traffic-manager-example"></a>Příklad správce provozu

Společnost Contoso Corp vyvinula nový partnerský portál. Adresa URL tohoto `https://partners.contoso.com/login.aspx`portálu je . Aplikace je hostovaná ve třech oblastech Azure. Chcete-li zlepšit dostupnost a maximalizovat globální výkon, používají Traffic Manager k distribuci provozu klienta do nejbližšího dostupného koncového bodu.

K dosažení této konfigurace, které dokončí následující kroky:

1. Nasaďte tři instance jejich služby. Názvy DNS těchto nasazení jsou "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" a "contoso-asia.cloudapp.net".
1. Vytvořte profil Traffic Managers s názvem "contoso.trafficmanager.net" a nakonfigurujte jej tak, aby používal metodu směrování provozu "Výkon" ve třech koncových bodech.
1. Nakonfigurujte jejich název domény "partners.contoso.com", aby ukazoval na "contoso.trafficmanager.net" pomocí záznamu DNS CNAME.

![Konfigurace DNS správce provozu][1]

> [!NOTE]
> Při použití dodomény marnosti s Azure Traffic Manager, musíte použít CNAME nasměrovat název domény marnost na název domény Traffic Manager. Standardy DNS neumožňují vytvořit CNAME na vrcholu (nebo root) domény. Proto nelze vytvořit CNAME pro 'contoso.com' (někdy volal 'nahý' domény). CNAME můžete vytvořit pouze pro doménu pod "contoso.com", například "www.contoso.com". Chcete-li toto omezení obejít, doporučujeme hostování domény DNS ve [službě Azure DNS](../dns/dns-overview.md) a použití záznamů [aliasu,](../dns/tutorial-alias-tm.md) které odkazují na profil správce provozu. Případně můžete použít jednoduché přesměrování HTTP k přímému požadavku na "contoso.com" alternativnímu názvu, například "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak se klienti připojují pomocí Traffic Manageru

Pokračování z předchozího příkladu, když klient `https://partners.contoso.com/login.aspx`požaduje stránku , klient provede následující kroky k vyřešení názvu DNS a navázat připojení:

![Navázání připojení pomocí Traffic Manageru][2]

1. Klient odešle dotaz DNS na konfigurované rekurzivní službě DNS, aby přeložil název "partners.contoso.com". Rekurzivní služba DNS, někdy nazývaná "místní služba DNS", nehostuje domény DNS přímo. Klient spíše off-zatížení práce kontaktovat různé autoritativní služby DNS přes Internet potřebné k vyřešení názvu DNS.
2. Chcete-li přeložit název DNS, vyhledá rekurzivní služba DNS názvové servery pro doménu contoso.com. Poté kontaktuje tyto názvové servery a požádá o záznam DNS "partners.contoso.com". Servery DNS contoso.com vrátí záznam CNAME, který odkazuje na contoso.trafficmanager.net.
3. Dále rekurzivní služba DNS vyhledá názvové servery pro doménu "trafficmanager.net", které poskytuje služba Azure Traffic Manager. Poté odešle těmto serverům DNS požadavek na záznam DNS contoso.trafficmanager.net.
4. Názevové servery Traffic Managerobdrží požadavek. Vyberou si koncový bod na základě:

    - Nakonfigurovaný stav každého koncového bodu (zakázané koncové body nejsou vráceny)
    - Aktuální stav každého koncového bodu, jak je určeno traffic manager kontroly stavu. Další informace naleznete v tématu [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md).
    - Zvolená metoda směrování provozu. Další informace naleznete v tématu [Traffic Manager Routing Methods](traffic-manager-routing-methods.md).

5. Zvolený koncový bod je vrácen jako jiný záznam DNS CNAME. V tomto případě předpokládejme, že contoso-us.cloudapp.net je vrácena.
6. Dále rekurzivní služba DNS vyhledá názvové servery pro doménu cloudapp.net. Kontaktuje tyto názvové servery a požádá o záznam DNS "contoso-us.cloudapp.net". Je vrácen záznam DNS "A", který obsahuje IP adresu koncového bodu služby v USA.
7. Rekurzivní služba DNS sloučí výsledky a vrátí klientovi jednu odpověď DNS.
8. Klient obdrží výsledky DNS a připojí se k dané adrese IP. Klient se připojuje ke koncovému bodu aplikační služby přímo, nikoli prostřednictvím Traffic Manageru. Vzhledem k tomu, že se jedná o koncový bod HTTPS, klient provede nezbytné ssl/tls handshake a potom vytvoří požadavek HTTP GET pro stránku '/login.aspx'.

Rekurzivní služba DNS ukládá odpovědi DNS, které obdrží. Překladač DNS na klientském zařízení také ukládá výsledek do mezipaměti. Ukládání do mezipaměti umožňuje rychlejší zodpovězení následných dotazů DNS pomocí dat z mezipaměti, nikoli dotazováním jiných názvových serverů. Doba trvání mezipaměti je určena vlastností Time-to-live (TTL) každého záznamu DNS. Kratší hodnoty mají za následek rychlejší vypršení mezipaměti a tím i další round-trips na názvové servery Traffic Manager. Delší hodnoty znamenají, že může trvat déle směrovat provoz od neúspěšného koncového bodu. Traffic Manager umožňuje nakonfigurovat TTL používané v Traffic Manager DNS odpovědi tak nízké, jak 0 sekund a tak vysoko, jak 2,147,483,647 sekund (maximální rozsah kompatibilní s [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), což umožňuje zvolit hodnotu, která nejlépe vyvažuje potřeby vaší aplikace.

## <a name="faqs"></a>Nejčastější dotazy

* [Jakou IP adresu traffic manager používá?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Jaké typy provozu lze směrovat pomocí Traffic Manageru?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Podporuje Traffic Manager "lepkavé" relace?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Proč se při používání Traffic Manageru zobrazuje chyba HTTP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Jaký je dopad používání Traffic Manageru na výkon?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Jaké aplikační protokoly lze použít s Traffic Managerem?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Mohu použít Traffic Manager s "nahým" názvem domény?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Bere Traffic Manager při zpracování dotazů DNS v úvahu adresu podsítě klienta?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Co je DNS TTL a jaký to má vliv na mé uživatele?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Jak vysoké nebo nízké lze nastavit TTL pro traffic manager odpovědi?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Jak mohu porozumět objemu dotazů přicházejících do mého profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o sledování koncového bodu Traffic [Manageru a automatickém převzetí služeb při selhání](traffic-manager-monitoring.md).

Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md)traffic manageru .

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

