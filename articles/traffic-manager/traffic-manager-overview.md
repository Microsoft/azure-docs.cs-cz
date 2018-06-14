---
title: Co je Traffic Manageru | Microsoft Docs
description: Tento článek vám pomůže pochopit, co je Traffic Manager, a zda je volba správné provoz směrování pro vaši aplikaci
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877052"
---
# <a name="overview-of-traffic-manager"></a>Přehled Traffic Manageru

Microsoft Azure Traffic Manager umožňuje řízení distribuce provozu generovaného uživateli pro koncové body služby v různých datových centrech. Koncové body služby, které jsou podporovány nástrojem Traffic Manager zahrnují virtuálních počítačích Azure, webové aplikace a cloudových služeb. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure.

Správce provozu se používá v systému DNS (Domain Name) pro přesměrování požadavků klientů na základě metodu směrování provozu a stav koncových bodů nejvhodnější koncový bod. Traffic Manager poskytuje řadu [metody směrování provozu](traffic-manager-routing-methods.md) a [koncový bod možnosti monitorování](traffic-manager-monitoring.md) tak, aby vyhovovala potřebám různé aplikace a modely automatické převzetí služeb při selhání. Správce provozu je odolný proti selhání, včetně selhání celé oblasti Azure.

## <a name="traffic-manager-benefits"></a>Výhody Traffic Manageru

Traffic Manager vám pomůžou:

* **Zvýšit dostupnost důležitých aplikací**

    Traffic Manager poskytuje vysokou dostupnost pro vaše aplikace prostřednictvím sledování koncových bodů a zajištění automatické převzetí služeb při selhání, kdy koncový bod přestane fungovat.

* **Zvýšení rychlosti odezvy pro vysoce výkonné aplikace**

    Azure umožňuje spuštění cloudové služby nebo webů v datových centrech umístěné po celém světě. Správce provozu zlepšuje odezvu aplikací přesměrováním provoz na koncový bod s nejnižší latenci sítě pro klienta.

* **Provedení údržby služby bez výpadků**

    Můžete provádět operace plánované údržby na vaše aplikace bez výpadků. Traffic Manager přesměruje přenosy na alternativní koncových bodů, když probíhá údržba.

* **Kombinovat místní a cloudové aplikace**

    Traffic Manager podporuje externí, mimo Azure koncové body mu umožní použít s hybridní cloud a místní nasazení, včetně "shluků to-cloud," "migrovat do cloudu," a "převzetí služeb při selhání cloud" scénáře.

* **Distribuce přenosů pro složitých nasazení**

    Pomocí [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md), mohou být kombinovány metody směrování provozu vytvořit sofistikované a flexibilní pravidel pro podporu potřebám rozsáhlejších, složitějších nasazení.

## <a name="how-traffic-manager-works"></a>Jak funguje Traffic Manager

Azure Traffic Manager umožňuje řízení distribuce provozu napříč koncovými body vaší aplikace. Koncový bod je všechny internetové služby hostované uvnitř nebo mimo Azure.

Správce provozu přináší dvě klíčové výhody:

1. Distribuce přenos v závislosti na jednu z několika [metody směrování provozu](traffic-manager-routing-methods.md)
2. [Nepřetržité monitorování stavu koncový bod](traffic-manager-monitoring.md) a automatické převzetí služeb při selhání koncových bodů

Když klient se pokusí připojit ke službě, se musí nejprve přeložit název DNS služby na adresu IP. Klient pak připojí k tuto IP adresu pro přístup ke službě.

**Nejvíce důležité pochopit, je, že Traffic Manager funguje na úrovni DNS.**  Přesměrovat klienty na konkrétní službu koncové body na základě pravidel metodu směrování provozu Traffic Manageru pomocí služby DNS. Klienti připojit k vybrané koncový bod **přímo**. Traffic Manager není serveru proxy nebo brána. Správce provozu nezná provozu mezi klientem a služby předávání.

### <a name="traffic-manager-example"></a>Příklad Traffic Manageru

Společnosti Contoso Corp si vytvořili nový portál pro partnery. Adresa URL pro tento portál je https://partners.contoso.com/login.aspx. Aplikace je hostitelem tři oblasti Azure. Zlepšení dostupnosti a maximalizovat výkon globální, jejich Traffic Manager slouží k distribuci přenosy klienta na nejbližší dostupný koncový bod.

K dosažení této konfigurace po dokončení následujících kroků:

1. Nasaďte tři instance své služby. Názvy DNS tato nasazení jsou 'contoso-us.cloudapp .net', 'contoso-eu.cloudapp .net nebo 'contoso-asia.cloudapp .net'.
2. Vytvoření profilu Traffic Manageru, s názvem 'contoso.trafficmanager.net' a nakonfigurujte ho na použití metodu směrování provozu, výkon, mezi tři koncových bodů.
* Konfigurace názvu domény jednoduché "partners.contoso.com" tak, aby odkazoval 'contoso.trafficmanager.net' pomocí záznam DNS CNAME.

![Konfigurace služby DNS Traffic Manageru][1]

> [!NOTE]
> Pokud používáte jednoduché doménu Azure Traffic Manager, musíte použít záznam CNAME přejděte na název domény jednoduché název domény Traffic Manageru. Standardech DNS neumožňují vytvořit záznam CNAME v 'vrcholu' (nebo kořenové) k doméně. Proto nelze vytvořit záznam CNAME pro "contoso.com" (někdy se označuje jako "holé" domény). Můžete vytvořit pouze záznam CNAME pro doménu v části "contoso.com", třeba "www.contoso.com". Toto omezení obejít, doporučujeme použít jednoduchý přesměrování protokolu HTTP na přímé požadavky pro "contoso.com" na alternativní název, třeba "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Způsob připojení klientů pomocí Traffic Manager

Pokračovat z předchozího příkladu, když klient požádá o https://partners.contoso.com/login.aspx stránky, klient provede následující kroky přeložit název DNS a připojení:

![Navázání připojení pomocí Traffic Manager][2]

1. Klient odešle dotaz DNS na jeho rekurzivní nakonfigurovaná služba DNS přeložit název "partners.contoso.com". Služba DNS rekurzivní, někdy označuje jako "místní DNS, služby, není hostitelem DNS domény přímo. Místo toho klienta off-loads práci při kontaktování služby různé DNS autoritativní přes Internet, které jsou potřebné k přeložit název DNS.
2. Služba DNS rekurzivní přeložit název DNS, najde názvové servery pro doménu "contoso.com". Potom kontaktuje tyto servery název žádosti o záznamu DNS "partners.contoso.com". Servery DNS contoso.com vrátí záznam CNAME, který odkazuje na contoso.trafficmanager.net.
3. V dalším kroku služba DNS rekurzivní vyhledá názvové servery pro doménu, trafficmanager.net', které jsou k dispozici ve službě Azure Traffic Manager. Pak odešle žádost o záznamu DNS 'contoso.trafficmanager.net' na tyto servery DNS.
4. Traffic Manager názvové servery požadavek přijmou. Vybírá koncový bod na základě:

    - Nakonfigurované stav každého koncového bodu (nebudou zobrazeny zakázané koncových bodů)
    - Kontroluje, aktuální stav každého koncového bodu, jak je určen podle stavu Traffic Manager. Další informace najdete v tématu [Traffic Manager koncového bodu monitorování](traffic-manager-monitoring.md).
    - Vybranou metodu směrování provozu. Další informace najdete v tématu [metody směrování Traffic Manager](traffic-manager-routing-methods.md).

5. Zvolený koncový bod se vrátí jako jiný záznam DNS CNAME. V takovém případě se dejte nám Předpokládejme, že je vrácen contoso us.cloudapp.net.
6. V dalším kroku služba DNS rekurzivní vyhledá názvové servery pro doménu, cloudapp.net'. Kontaktuje těchto názvové servery k vyžádání rozhraní .net contoso-us.cloudapp záznam DNS. Se vrátí záznam DNS "A", obsahující IP adresu koncového bodu služby na základě USA.
7. Služba DNS rekurzivní konsoliduje výsledky a vrátí jednu odpověď DNS klientovi.
8. Klient obdrží výsledky DNS a připojí na danou adresu IP. Se klient připojí ke koncovému bodu služby aplikace přímo, nejsou prostřednictvím Správce provozu. Vzhledem k tomu, že je koncový bod HTTPS, provede nezbytné SSL/TLS handshake klienta a pak provede požadavek HTTP GET "/ login.aspx' stránky.

Služba DNS rekurzivní ukládá do mezipaměti odpovědi DNS, které obdrží. Překladač DNS v klientském zařízení také ukládá do mezipaměti výsledek. Ukládání do mezipaměti umožňuje následné dotazy DNS k zodpovězení rychleji pomocí dat z mezipaměti a nikoli dotazování další názvové servery. Doba trvání mezipaměti je určen podle (TTL) vlastnost "time-to-live, pro každý záznam DNS. Kratší hodnoty za následek rychlejší vypršení platnosti mezipaměti a proto další odezvy na názvové servery Traffic Manager. Delší hodnoty znamená, že může trvat delší přímé provoz směrem od koncový bod se nezdařilo. Traffic Manager umožňuje nakonfigurovat interval TTL, ZÍSKÁ použít v odpovědi DNS Traffic Manager být v rozsahu od 0 sekund a až 2 147 483 647 sekund (maximální rozsah, který je kompatibilní s [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), které umožňují zvolit hodnotu, která nejlépe vyrovnává potřebám vaší aplikace.

## <a name="pricing"></a>Ceny

Informace o cenách najdete v části [Traffic Manager cenová](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>Nejčastější dotazy

Nejčastější dotazy o Traffic Manager, najdete v části [Traffic Manager nejčastější dotazy](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Další kroky

Další informace o Traffic Manager [koncového bodu monitorování a automatické převzetí služeb při selhání](traffic-manager-monitoring.md).

Další informace o Traffic Manager [metodách směrování provozu](traffic-manager-routing-methods.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

