---
title: Důležité informace o výkonu pro Azure Traffic Manageru | Microsoft Docs
description: Pochopení výkonu Traffic Manager a pro testování výkonu webu při použití Správce provozu
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876387"
---
# <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Tato stránka vysvětluje aspekty výkonu pomocí Traffic Manager. Vezměte v úvahu následující scénář:

Máte v oblastech WestUS a EastAsia instancí vašeho webu. Jedna z instancí selhává Kontrola stavu kontroly správce provozu. Aplikace je přenášená na oblasti v pořádku. Je očekávána toto převzetí služeb při selhání ale výkon může být problém podle čekací doba provozu teď cestě vzdáleným oblasti.

## <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Dopad na pouze výkonu, která může mít Traffic Manager na vašem webu je počáteční vyhledávání DNS. Kořenový server DNS od Microsoftu, který je hostitelem zóny trafficmanager.net zpracovává žádost DNS pro název vašeho profilu Traffic Manageru. Správce provozu naplní a pravidelně aktualizuje kořenové servery DNS společnosti Microsoft na základě zásadu Traffic Manageru a výsledky testu. Takže i během počáteční vyhledávání DNS žádné dotazy DNS se odesílají do Traffic Manageru.

Správce provozu se skládá z několika komponent: název DNS, servery, služby rozhraní API, vrstvy úložiště a koncový bod služby monitorování. Pokud součást služby Traffic Manager selže, neexistuje žádný vliv na DNS název spojený s vašeho profilu Traffic Manageru. Záznamy v serverech DNS od Microsoftu zůstanou beze změny. Ale monitorování koncového bodu a aktualizace DNS není dojít. Traffic Manager proto není možné aktualizovat DNS tak, aby odkazovaly na váš web převzetí služeb při selhání, pokud primární lokalita ocitne mimo provoz.

Překlad názvu DNS je rychlá a výsledky jsou uloženy v mezipaměti. Rychlost počáteční vyhledávání DNS závisí na serverech DNS, které klient používá pro překlad názvů. Klienta můžete obvykle dokončení vyhledávání DNS v rámci ~ 50 ms. Výsledky vyhledávání se uloží do mezipaměti po dobu trvání DNS Time-to-live (TTL). Výchozí hodnota TTL pro správce provozu je 300 sekund.

NENÍ přenos prostřednictvím Traffic Manager. Po dokončení vyhledávání DNS má klient IP adresu pro instanci webového serveru. Klient připojuje přímo na tuto adresu a nepředává prostřednictvím Traffic Manager. Zásadu Traffic Manageru, který zvolíte nemá vliv na výkon DNS. Výkonu směrování metody však může negativně ovlivnit prostředí aplikace. Například pokud vaše zásady přesměruje provoz z USA a Kanady do instance hostované v Asii, latence sítě pro tyto relace může být problém s výkonem.

## <a name="measuring-traffic-manager-performance"></a>Měření výkonu Traffic Manageru

Existuje několik webových stránek, které vám pomůže pochopit výkon a chování profil Traffic Manageru. Mnoho z těchto lokalit jsou zdarma, ale mohou mít omezení. Některé servery nabízejí rozšířené monitorování a vytváření sestav pro poplatek.

Nástroje na tyto weby měr DNS latenci a zobrazení přeložit IP adresy pro umístění klienta po celém světě. Většina těchto nástrojů Neukládat do mezipaměti výsledky DNS. Proto nástroje zobrazit úplnou vyhledávání DNS při každém spuštění testu. Při testování z vlastního klienta zaznamenáte jenom úplné výkon vyhledávání DNS jednou po dobu trvání TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ukázka nástroje pro měření výkonu DNS

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS nabízí celou řadu nástrojů výkonu. Nástroj pro porovnání DNS může zobrazit, jak dlouho trvá přeložit název DNS a jak, porovná se ostatní poskytovatele služeb DNS.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Jedním z nejjednodušší nástrojů je WebSitePulse. Zadejte adresu URL zobrazíte dobu překladu názvů DNS, prvním bajtem, posledního bajtu a další statistiky výkonu. Můžete ze tří různých testovací umístění. V tomto příkladu se zobrazí první spuštění ukazuje, že vyhledávání DNS trvá 0.204 sekundu.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Protože výsledky jsou uložené v mezipaměti, druhý test pro stejný koncový bod Traffic Manager vyhledávání DNS trvá 0,002 sekundu.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitorování aplikace Synthetic certifikační Autority](https://asm.ca.com/en/checkit.php)

    Dříve označované jako nástroj Watchmouse kontrola webu, tento web ukážeme dobu překladu názvů DNS z různých geografických oblastech současně. Zadejte adresu URL zobrazíte dobu překladu názvů DNS, čas připojení a rychlost z několika zeměpisné umístění. Tento test slouží k zobrazení, které hostované služby se vrátí pro různých umístěních po celém světě.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Tento nástroj poskytuje statistiku výkonu pro každý prvek webové stránky. Na kartě analýza stránky zobrazuje procento času stráveného na vyhledávání DNS.

* [Co je můj DNS?](http://www.whatsmydns.net/)

    Tento web nemá vyhledávání DNS z různých míst na 20 a zobrazí výsledky na mapě.

* [Prozkoumat webové rozhraní](http://www.digwebinterface.com)

    Tento web obsahuje že podrobnější informace DNS včetně záznamů CNAME a záznamy. Zajistěte, aby zkontrolujte 'Kolorovat výstup' a 'statistiky, v nabídce Možnosti a vyberte 'všechny pod Nameservers.

## <a name="next-steps"></a>Další kroky

[O metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)

[Test nastavení Traffic Manager](traffic-manager-testing-settings.md)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

