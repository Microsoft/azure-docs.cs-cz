---
title: Důležité informace o výkonu pro Azure Traffic Manager | Dokumenty společnosti Microsoft
description: Pochopit výkon traffic manageru a jak otestovat výkon vašeho webu při používání Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938538"
---
# <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Tato stránka vysvětluje aspekty výkonu pomocí Traffic Manageru. Představte si následující scénář:

Máte instance svých webových stránek v oblastech WestUS a EastAsia. Jednou z instancí se nedaří kontrolu stavu pro sondu správce provozu. Provoz aplikace je směrován do oblasti v pořádku. Toto převzetí služeb při selhání se očekává, ale výkon může být problém na základě latence provozu nyní cestování do vzdálené oblasti.

## <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Jediným dopadem na výkon, který může traffic manager mít na vašem webu, je počáteční vyhledávání DNS. Požadavek DNS na název profilu traffic manageru zpracovává kořenový server Microsoft DNS, který je hostitelem zóny trafficmanager.net. Traffic Manager naplní a pravidelně aktualizuje kořenové servery DNS společnosti Microsoft na základě zásad Traffic Manager a výsledků sondy. Takže i během počátečního vyhledávání DNS nejsou do Traffic Manageru odesílány žádné dotazy DNS.

Traffic Manager se skládá z několika součástí: názvových serverů DNS, služby rozhraní API, vrstvy úložiště a služby sledování koncového bodu. Pokud se součást služby Traffic Manager nezdaří, nemá to žádný vliv na název DNS přidružený k vašemu profilu traffic manageru. Záznamy na serverech Microsoft DNS zůstanou nezměněny. Monitorování koncového bodu a aktualizace DNS však nedojde. Traffic Manager proto není schopen aktualizovat službu DNS tak, aby ukazovala na lokalitu s podporou převzetí služeb při selhání, když primární lokalita přejde dolů.

Překlad názvů DNS je rychlý a výsledky jsou ukládány do mezipaměti. Rychlost počátečního vyhledávání DNS závisí na serverech DNS, které klient používá pro překlad názvů. Obvykle může klient dokončit vyhledávání DNS v rámci ~50 ms. Výsledky vyhledávání jsou ukládány do mezipaměti po dobu trvání data aktivního času DNS (TTL). Výchozí hodnota TTL pro traffic manager je 300 sekund.

Provoz neprochází traffic managerem. Po dokončení vyhledávání DNS má klient IP adresu pro instanci vašeho webu. Klient se připojí přímo k této adrese a neprochází Traffic Manager. Vybraná zásada Traffic Manager nemá žádný vliv na výkon DNS. Metoda směrování výkonu však může negativně ovlivnit prostředí aplikace. Pokud například vaše zásada přesměruje provoz ze Severní Ameriky na instanci hostovohodanou v Asii, latence sítě pro tyto relace může být problém s výkonem.

## <a name="measuring-traffic-manager-performance"></a>Měření výkonu manažera provozu

Existuje několik webů, které můžete použít k pochopení výkonu a chování profilu Traffic Manageru. Mnohé z těchto stránek jsou zdarma, ale mohou mít omezení. Některé weby nabízejí rozšířené monitorování a vykazování za poplatek.

Nástroje na těchto webech měří latenci DNS a zobrazují vyřešené IP adresy pro umístění klientů po celém světě. Většina těchto nástrojů neukládá výsledky DNS do mezipaměti. Proto nástroje zobrazit úplné vyhledávání DNS při každém spuštění testu. Při testování z vlastního klienta dochází pouze k úplnému výkonu vyhledávání DNS jednou během trvání TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ukázkové nástroje pro měření výkonu DNS

* [Vyřešit DNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS nabízí mnoho nástrojů pro výkon. Nástroj Porovnání DNS vám může ukázat, jak dlouho trvá vyřešení názvu DNS a jak se to porovná s ostatními poskytovateli služeb DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Jedním z nejjednodušších nástrojů je WebSitePulse. Zadejte adresu URL, chcete-li zobrazit čas rozlišení DNS, první bajt, poslední bajt a další statistiky výkonu. Můžete si vybrat ze tří různých testovacích míst. V tomto příkladu uvidíte, že první spuštění ukazuje, že vyhledávání DNS trvá 0.204 sec.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Vzhledem k tomu, že výsledky jsou uloženy do mezipaměti, druhý test pro stejný koncový bod Traffic Manager vyhledávání DNS trvá 0.002 sec.

    ![puls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Syntetický monitor aplikace CA](https://asm.ca.com/en/checkit.php)

    Dříve známý jako nástroj Watch-mouse Check Website, tento web vám ukazuje čas rozlišení DNS z více zeměpisných oblastí současně. Zadejte adresu URL a zobrazí se doba rozlišení DNS, doba připojení a rychlost z několika geografických umístění. Pomocí tohoto testu můžete zjistit, která hostovaná služba je vrácena pro různá místa po celém světě.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Tento nástroj poskytuje statistiky výkonu pro každý prvek webové stránky. Karta Analýza stránky zobrazuje procento času stráveného vyhledáváním DNS.

* [Co je moje DNS?](https://www.whatsmydns.net/)

    Tento web provádí vyhledávání DNS z 20 různých umístění a zobrazuje výsledky na mapě.

* [Webové rozhraní Dig](https://www.digwebinterface.com)

    Tato stránka zobrazuje podrobnější informace DNS včetně Záznamů CNAMA a A. Ujistěte se, že jste v části možnosti zaškrtli "Colorize output" a "Stats" a v části Nameservers vyberte možnost "Vše".

## <a name="next-steps"></a>Další kroky

[O metodách směrování provozu traffic manageru](traffic-manager-routing-methods.md)

[Test nastavení Traffic Manageru](traffic-manager-testing-settings.md)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manageru](https://docs.microsoft.com/powershell/module/az.trafficmanager)

