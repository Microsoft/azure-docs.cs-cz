---
title: Síťová řešení pro sledování výkonu v Azure Log Analytics | Microsoft Docs
description: Ke sledování připojení k síti pro libovolný koncový bod, který má otevřený port TCP použijte funkce správce koncový bod služby v nástroji Sledování výkonu sítě.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: b21d711e59ddc762eaf72f49e501d9f324d75105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="service-endpoint-monitor"></a>Monitorování koncového bodu služby

Můžete použít funkce monitorování koncového bodu služby v [sledování výkonu sítě](log-analytics-network-performance-monitor.md) monitorování připojení k síti pro libovolný koncový bod, který má otevřený port TCP. Tyto koncové body můžou být webů, aplikací SaaS, PaaS aplikací a databází SQL. 

Můžete provádět následující funkce s monitorování koncového bodu služby: 

- Monitorování připojení k síti k vašim aplikacím a síťových služeb z více firemní pobočky nebo umístění. Aplikace a síťové služby patří Office 365, Dynamics CRM, interní-obchodní aplikace a databází SQL.
- Pomocí předdefinovaných testů a monitorování síťové připojení k Office 365, Dynamics 365 koncové body. 
- Určete dobu odezvy, latence sítě a ztráta paketů zkušeného při připojení ke koncovému bodu.
- Určete, zda je nízký aplikace výkon z důvodu sítě nebo z důvodu některých problém na konci poskytovatele aplikace.
- Identifikujte aktivní body v síti, která může být příčinou aplikace nízký výkon zobrazením latence přispěli každého směrování na mapě topologie.


![Monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurace 
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení pro sledování výkonu sítě](log-analytics-network-performance-monitor.md) a vyberte **konfigurace**.

![Konfigurace programu Sledování výkonu sítě](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurace služby Operations Management Suite agentů pro monitorování
Povolte následující pravidla brány firewall na uzlech používá pro monitorování, aby řešení můžete zjistit topologii z uzlů pro koncový bod služby: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Vytváření testů monitorování koncového bodu služby 

Začít vytvářet testy monitorování síťové připojení ke koncovým bodům služby.

1. Vyberte **monitorování koncového bodu služby** kartě.
2. Vyberte **přidat Test**a zadejte testovací název a popis. 
3. Vyberte typ testu:<br>

    * Vyberte **webové** k připojení ke službě, která reaguje na požadavky HTTP/S, jako je například outlook.office365.com nebo vyhledávače bing.com monitorování.<br>
    * Vyberte **sítě** monitorování připojení pro službu, která odpovídá na požadavky protokolu TCP, ale nebude reagovat na požadavky HTTP/S, jako je například SQL server, FTP server nebo portu SSH. 
4. Pokud nechcete provést měření sítě, jako je latence sítě, ztráta paketů a zjišťování topologie, zrušte **provést měření sítě** zaškrtávací políčko. Udržování ho Pokud chcete získat maximální výhody z možnosti. 
5. V **cíl**, zadejte adresu URL nebo plně kvalifikovaný název domény nebo IP adresu, do které chcete monitorovat připojení k síti.
6. V **číslo portu**, zadejte číslo portu z cílových služeb. 
7. V **testování frekvence**, zadejte hodnotu pro jak často chcete spustit test. 
8. Vyberte uzel, z nichž chcete sledovat síťové připojení ke službě. 

    >[!NOTE]
    > Pro uzly na serveru Windows používá funkce založených na protokolu TCP požadavky k provedení měření sítě. Pro uzly na základě klienta Windows používá možnosti založené na protokolu ICMP požadavky k provedení měření sítě. V některých případech cílová aplikace blokuje příchozí požadavky na základě protokolu ICMP, při uzly jsou na základě klienta systému Windows. Řešení není schopen provést měření sítě. Doporučujeme použít uzly na serveru systému Windows v takových případech. 

9. Pokud nechcete, aby k vytvoření události stavu pro položky vyberete, zrušte **povolit sledování stavu v cíle pro tento test předmětem**. 
10. Vyberte sledování podmínek. Vlastní prahové hodnoty pro generování událost stavu můžete nastavit tak, že zadáte prahové hodnoty. Vždy, když je hodnota stavu překročí jeho vybraná prahová hodnota pro vybranou síť nebo dvojici podsíť, je generována událost stavu. 
11. Vyberte **Uložit** konfiguraci uložíte. 

    ![Konfigurací testů monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Názorný postup 

Přejděte do zobrazení řídicího panelu monitorování výkonu sítě. Chcete-li získat shrnutí stavu různých testy, které jste vytvořili, podívejte se **monitorování koncového bodu služby** stránky. 

![Stránka monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Vyberte dlaždice, chcete-li zobrazit podrobnosti o testy na **testy** stránky. V tabulce na levé straně můžete zobrazit stav bodu v čase a hodnota Doba odezvy služby, latence sítě a ztráta paketů pro všechny testy. Použití ovládacího prvku zapisovač stavu sítě zobrazíte sítě snímku v jinou dobu v minulosti. Vyberte testovací v tabulce, který chcete prozkoumat. Grafy v podokně na pravé straně uvidíte historických trendů ztrátě, latence a hodnoty času odezvy. Vyberte **Podrobnosti testu** odkaz na zobrazení výkonu z každého uzlu.

![Testy monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

V **testovací uzly** zobrazení, můžete sledovat připojení k síti z jednotlivých uzlů. Vyberte uzel, který má snížení výkonu. Toto je uzel, kde zjistíte aplikace, aby byl spuštěn pomalu.

Určete, zda je z důvodu sítě nebo problém na konci zprostředkovatele aplikace výkon nízký aplikace pomocí sledování korelace mezi doba odezvy aplikace a latencí sítě. 

* **Problém s aplikací:** Špička doby odezvy, ale konzistence v latence sítě naznačuje, že v síti funguje správně, a tento problém může být kvůli problému na konci aplikace. 

    ![Problém s aplikací monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Problém sítě:** Špička doby odezvy společně s odpovídající Špička v sítích s latencí naznačuje, že prodloužením doby odezvy může z důvodu zvýšení latence sítě. 

    ![Problém sítě monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Po určení, že problém je z důvodu sítě, vyberte **topologie** zobrazení odkaz k identifikaci problémových směrování na mapě topologie. Na následujícím obrázku je znázorněn příklad. Mimo 105 ms nízkou celkovou latenci mezi uzlu a koncový bod aplikace kvůli směrování označen červeně je 96 ms. Po určení problémových směrování, může trvat opravné akce. 

![Testy monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostika 

Pokud zjistíte anomálií, postupujte takto:

* Pokud doba odezvy služby, ztráty sítě a prodlevy se zobrazují jako NA, může být příčinou jeden nebo více z následujících důvodů:

    - Aplikace je mimo provoz.
    - Uzel ke kontrole síťové připojení ke službě je mimo provoz.
    - Cíl zadaný v konfiguraci testovací je nesprávný.
    - Uzel nemá připojení k síti.

* Pokud doba odezvy platný služby se zobrazí, ale ztráty sítě, jakož i latence se zobrazují jako NA, může být příčinou jeden nebo více z následujících důvodů:

    - Pokud se používá pro kontrolu síťové připojení ke službě uzel klientský počítač systému Windows, Cílová služba neblokuje požadavky ICMP nebo síťová brána firewall neblokuje požadavky protokolu ICMP, které pocházejí z uzlu.
    - **Provést měření sítě** zaškrtávací políčko je prázdná v konfiguraci testu. 

* Pokud doba odezvy služby NÁ, ale jsou platné ztráty sítě, jakož i latence, Cílová služba nemusí být webové aplikace. Konfigurace testovací upravit a vyberte typ testu jako **sítě** místo **webové**. 

* Pokud aplikace běží pomalu, určete, zda je aplikace nízký výkon z důvodu sítě nebo problém na konci zprostředkovatele aplikace.


## <a name="next-steps"></a>Další postup
[V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné sítě záznamů dat výkonu.
