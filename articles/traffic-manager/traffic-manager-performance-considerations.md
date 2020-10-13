---
title: Požadavky na výkon pro Azure Traffic Manager | Microsoft Docs
description: Vysvětlení výkonu při Traffic Manager a testování výkonu webu při použití Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: f080f5011311101ed8f9faa2ee807c030a048409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653039"
---
# <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Tato stránka vysvětluje informace o výkonu pomocí Traffic Manager. Představte si následující scénář:

Máte instance svého webu v oblastech WestUS a EastAsia. Jedna z instancí selhává při kontrole stavu pro test Traffic Manageru. Provoz aplikace je směrován do zdravé oblasti. Toto převzetí služeb při selhání se očekává, ale výkon se může nacházet v závislosti na latenci provozu, který je nyní na vzdáleném regionu.

## <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Jediným dopadem na výkon, který Traffic Manager může mít na vašem webu, je počáteční vyhledávání DNS. Požadavek DNS na název vašeho profilu Traffic Manager se zpracovává pomocí kořenového serveru DNS Microsoft, který je hostitelem zóny trafficmanager.net. Traffic Manager naplní a pravidelně aktualizuje kořenové servery DNS od Microsoftu na základě zásad Traffic Manager a výsledků testu. Takže i během počátečního vyhledávání DNS se neodesílají žádné dotazy DNS na Traffic Manager.

Traffic Manager se skládá z několika součástí: názvové servery DNS, služba API, vrstva úložiště a služba monitorování koncového bodu. Pokud dojde k chybě součásti Traffic Manager služby, nebude to mít žádný vliv na název DNS přidružený k vašemu profilu Traffic Manager. Záznamy na serverech DNS společnosti Microsoft zůstanou beze změny. Monitorování koncového bodu a aktualizace DNS ale neproběhne. Proto Traffic Manager nedokáže aktualizovat DNS tak, aby odkazoval na váš web s podporou převzetí služeb při selhání, když dojde k výpadku primární lokality.

Překlad názvů DNS je rychlý a výsledky se ukládají do mezipaměti. Rychlost počátečního vyhledávání DNS závisí na serverech DNS, které klient používá k překladu názvů. Klient obvykle může dokončit vyhledávání DNS v rámci ~ 50 ms. Výsledky hledání jsou uloženy v mezipaměti po dobu trvání hodnoty TTL (Time to Live) služby DNS. Výchozí hodnota TTL pro Traffic Manager je 300 sekund.

Přenosy nesměrují do Traffic Manager. Po dokončení vyhledávání DNS má klient IP adresu pro instanci vašeho webu. Klient se připojuje přímo k této adrese a neprojde Traffic Manager. Zásada Traffic Manager, kterou zvolíte, nemá žádný vliv na výkon DNS. Směrování výkonu však může negativně ovlivnit prostředí aplikace. Pokud vaše zásada například přesměruje provoz z Severní Amerika do instance hostované v Asii, může se jednat o problém s výkonem sítě pro tyto relace.

## <a name="measuring-traffic-manager-performance"></a>Měření výkonu Traffic Manager

K dispozici je několik webů, které můžete použít k pochopení výkonu a chování profilu Traffic Manager. Mnohé z těchto webů jsou bezplatné, ale mohou mít omezení. Některé weby nabízí rozšířené monitorování a vykazování za poplatek.

Nástroje na těchto webech měří latence DNS a zobrazují přeložené IP adresy pro umístění klientů po celém světě. Většina těchto nástrojů neukládá do mezipaměti výsledky DNS. Proto nástroje při každém spuštění testu zobrazí úplné vyhledávání DNS. Když testujete klienta z vašeho vlastního klienta, provedete během doby TTL pouze jeden výkon celého vyhledávání DNS.

## <a name="sample-tools-to-measure-dns-performance"></a>Ukázkové nástroje pro měření výkonu DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS nabízí mnoho nástrojů pro sledování výkonu. Nástroj pro porovnání DNS vám může ukázat, jak dlouho trvá překlad názvu DNS a jak porovnává s ostatními poskytovateli služeb DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Jedním z nejjednodušších nástrojů je WebSitePulse. Zadejte adresu URL pro zobrazení doby překladu DNS, prvního bajtu, posledního bajtu a dalších statistik výkonu. Můžete vybrat ze tří různých testovacích umístění. V tomto příkladu vidíte, že první spuštění ukazuje, že vyhledávání DNS trvá 0,204 s.

    ![Snímek obrazovky, který zobrazuje nástroj "WebSitePulse" se zvýrazněným výsledkem hledání "DNS".](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Vzhledem k tomu, že se výsledky ukládají do mezipaměti, druhý test stejného Traffic Manager koncového bodu, který vyhledávání DNS trvá 0,002 s.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Syntetické monitorování aplikace CA](https://asm.ca.com/en/checkit.php)

    V této lokalitě dříve označované jako nástroj pro kontrolu webu Sledujte, tato lokalita vám zobrazuje dobu překladu názvů DNS z několika geografických oblastí současně. Zadejte adresu URL, abyste viděli dobu překladu DNS, dobu připojení a rychlost z několika geografických umístění. Pomocí tohoto testu zjistíte, která hostovaná služba se vrátí pro různá umístění po celém světě.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Tento nástroj poskytuje statistiku výkonu pro každý prvek webové stránky. Karta analýza stránky zobrazuje procento času stráveného vyhledáváním DNS.

* [Co je můj DNS?](https://www.whatsmydns.net/)

    Tato lokalita provádí vyhledávání DNS z dvaceti různých umístění a zobrazuje výsledky na mapě.

* [Webové rozhraní dig](https://www.digwebinterface.com)

    Tato lokalita obsahuje podrobnější informace o DNS, včetně záznamů CNAME a záznamů. Ujistěte se, že jste v části Možnosti zaškrtli zabarvovat Output a stat, a v části názvové servery vyberte All (vše).

## <a name="next-steps"></a>Další kroky

[Informace o metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)

[Test nastavení Traffic Manageru](traffic-manager-testing-settings.md)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manager](https://docs.microsoft.com/powershell/module/az.trafficmanager)

