---
title: Důležité informace o výkonu pro službu Azure Traffic Manager | Dokumentace Microsoftu
description: Výkon Traffic Manageru a testování výkonu webu, když pomocí Traffic Manageru
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 1bf2222e09644520bbfc6c5424c7f29d05b3c799
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257693"
---
# <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Tato stránka vysvětluje, používat Traffic Manager důležité informace o výkonu. Vezměte v úvahu následující scénář:

Máte instance vašeho webu v oblastech WestUS a východní Asie. Jedna z instancí selhává Kontrola stavu kontroly traffic Manageru. Aplikace provoz se směřuje do oblasti jsou v pořádku. Očekává se toto převzetí služeb při selhání, ale výkon může být problém na základě latence provoz nyní cestě do vzdálené oblasti.

## <a name="performance-considerations-for-traffic-manager"></a>Důležité informace o výkonu nástroje Traffic Manager

Pouze dopad na výkon, který Traffic Manager může mít na svém webu je počáteční vyhledávání DNS. Kořenový server DNS od Microsoftu, který hostuje zóny trafficmanager.net zpracovává žádost DNS pro název vašeho profilu Traffic Manageru. Traffic Manager naplní a pravidelně aktualizuje kořenové servery DNS od Microsoftu na základě zásadu Traffic Manageru a výsledky testu. Takže i během počáteční vyhledávání DNS bez DNS dotazy se odesílají do Traffic Manageru.

Traffic Manager se skládá z několika komponent: název serveru DNS, servery, služby rozhraní API, vrstvy úložiště a koncový bod monitorování služby. Pokud součást služby Traffic Manager selže, neexistuje žádný vliv na DNS název přidružený k profilu Traffic Manageru. Záznamy v serverech DNS od Microsoftu zůstanou beze změny. Nicméně monitorování koncových bodů a aktualizace DNS není standardní. Traffic Manager proto není možné aktualizovat DNS tak, aby odkazovala na váš web převzetí služeb při selhání, pokud primární lokalita ocitne mimo provoz.

Překlad názvů DNS je rychlá a výsledky jsou uloženy v mezipaměti. Rychlost počáteční vyhledávání DNS závisí na servery DNS, kterou klient používá k rozlišení názvu. Klienta lze obvykle provést vyhledávání DNS v rámci přibližně 50 ms. Výsledky vyhledávání jsou uložené v mezipaměti po dobu trvání DNS Time-to-live (TTL). Výchozí hodnota TTL pro Traffic Manager je 300 sekund.

Provoz nepostupuje pomocí Traffic Manageru. Po dokončení vyhledávání DNS má klient IP adresu pro instanci webu. Klient se připojí přímo k této adrese a nepředává prostřednictvím Traffic Manageru. Zásadu Traffic Manageru, kterou zvolíte, nemá vliv na výkon DNS. Však výkonu směrování metodu může negativně ovlivnit funkčnost aplikací. Například pokud vaše zásady přesměruje provoz v Severní Americe do instance hostované v Asii, latence sítě pro tyto relace může být problému s výkonem.

## <a name="measuring-traffic-manager-performance"></a>Měření výkonu Traffic Manageru

Existuje několik webů, která vám pomůže pochopit výkon a chování profil služby Traffic Manager. Mnohé z těchto webů je zdarma, ale mohou mít omezení. Některé servery nabízejí rozšířené monitorování a vytváření sestav za poplatek.

Nástroje na tyto weby míru DNS latenci a zobrazení přeložit IP adresy pro klienta umístění po celém světě. Většina těchto nástrojů Neukládat do mezipaměti výsledky DNS. Proto nástroje zobrazit úplnou vyhledávání DNS pokaždé, když je test spuštěn. Při testování z vlastního klienta zaznamenáte pouze plný výkon vyhledávání DNS jednou během doby trvání TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ukázka nástroje pro měření výkonu

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS nabízí celou řadu nástrojů výkonu. Nástroj porovnání DNS můžete zobrazit, jak dlouho trvá přeložit název DNS a, který srovnání s ostatními poskytovateli služby DNS.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Jedním z nejjednodušší nástrojů je WebSitePulse. Zadejte adresu URL zobrazíte dobu překladu názvů DNS, první bajt, poslední bajt a další statistiky výkonu. Můžete vybrat ze tří různých testovacích míst. V tomto příkladu uvidíte, že dojde k prvnímu spuštění ukazuje, že trvá vyhledání DNS 0.204 sekundu.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Protože výsledky jsou ukládány do mezipaměti, druhý test pro stejný koncový bod služby Traffic Manager vyhledávání DNS trvá 0,002 sekundu.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitorování syntetických aplikace certifikační Autority](https://asm.ca.com/en/checkit.php)

    Dříve označované jako nástroj Watchmouse kontrola webu, tento web ukazují, dobu překladu názvů DNS z více geografických oblastí najednou. Zadejte adresu URL zobrazíte dobu překladu názvů DNS, připojení a rychlost v několika geografických umístěních. Pomocí tohoto testu můžete zobrazit, které hostované služby se vrátí na jiná umístění po celém světě.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Tento nástroj poskytuje statistiku výkonu pro každý prvek na webové stránce. Analýza stránky karta zobrazuje procento času stráveného na vyhledávání DNS.

* [Co je Moje DNS?](http://www.whatsmydns.net/)

    Tato lokalita provede vyhledávání DNS z 20 různých míst a zobrazí výsledky na mapě.

* [Ponořte se webové rozhraní](http://www.digwebinterface.com)

    Tento web obsahuje že podrobnější informace o DNS, včetně záznamů CNAME a záznamů. Ujistěte se, že zkontrolujte 'Kolorovat výstup' a "Statistiky" v části Možnosti a vyberte "Vše" na názvové servery.

## <a name="next-steps"></a>Další kroky

[O metodách směrování provozu Traffic Manageru](traffic-manager-routing-methods.md)

[Test nastavení Traffic Manageru](traffic-manager-testing-settings.md)

[Operace v Traffic Manageru (referenční informace k rozhraní API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Rutiny Azure Traffic Manageru](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager)

