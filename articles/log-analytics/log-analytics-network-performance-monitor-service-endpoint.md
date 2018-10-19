---
title: Network Performance Monitor řešení v Azure Log Analytics | Dokumentace Microsoftu
description: Monitorování připojení k síti pro libovolný koncový bod, který má otevřený port TCP pomocí funkce monitorování připojení služby v nástroji Sledování výkonu sítě.
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
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: ''
ms.openlocfilehash: fb84b20630eb63cb53ccb1d13a383ed6287b802b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406615"
---
# <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Můžete použít funkci monitorování připojení služby v [Network Performance Monitor](log-analytics-network-performance-monitor.md) k monitorování připojení k síti pro libovolný koncový bod, který má otevřený port TCP. Tyto koncové body můžou být webů, aplikací SaaS, PaaS aplikací a databází SQL. 

Můžete provádět následující funkce se monitorování připojení služby: 

- Monitorujte síťové připojení k vašim aplikacím a síťových služeb z více firemní pobočky nebo umístění. Aplikace a síťové služby patří Office 365, Dynamics CRM, interní-obchodní aplikace a databáze SQL.
- Použití integrované testy monitorovat síťové připojení ke koncovým bodům služeb Office 365 a Dynamics 365. 
- Určení doby odezvy, latence sítě a ztráta paketů zkušenosti při připojování ke koncovému bodu.
- Určení, zda je nízký výkon aplikace z důvodu sítě nebo z důvodu problému na konci poskytovatele aplikace.
- Identifikace hotspotů v síti, které můžou způsobovat nízký výkon aplikace zobrazením latence z každého směrování na mapu.


![Monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurace 
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení Network Performance Monitor](log-analytics-network-performance-monitor.md) a vyberte **konfigurovat**.

![Konfigurace Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů Log Analytics pro monitorování
Povolte následující pravidla brány firewall na uzlech používá pro monitorování tak, že řešení najdou topologie z uzly do koncového bodu služby: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Vytvořit testy monitorování připojení služby 

Začněte vytvářet testy monitorování síťové připojení ke koncovým bodům služby.

1. Vyberte **monitorování připojení služby** kartu.
2. Vyberte **přidat Test**a zadejte název testu a popis. 
3. Vyberte typ testu:<br>

    * Vyberte **webové** k monitorování připojení ke službě, která bude reagovat na požadavky HTTP/S, třeba outlook.office365.com nebo bing.com.<br>
    * Vyberte **sítě** k monitorování připojení ke službě, která reaguje na požadavky protokolu TCP ale nereaguje na požadavky HTTP/S, jako je například SQL server, FTP server nebo SSH port. 
4. Pokud nechcete provést měření sítě, jako je latence sítě, ztráta paketů a zjišťování topologie, zrušte zaškrtnutí políčka **provést měření sítě** zaškrtávací políčko. Zachovat jeho vybrané získat maximální výhody z funkce. 
5. V **cílové**, zadejte adresu URL nebo plně kvalifikovaný název domény nebo IP adresu, ke které chcete monitorovat síťové připojení.
6. V **číslo portu**, zadejte číslo portu, cílové služby. 
7. V **frekvence testování**, zadejte hodnotu pro jak často chcete, aby test spuštěn. 
8. Vyberte uzly, ze kterých chcete monitorovat síťové připojení ke službě. 

    >[!NOTE]
    > Pro uzly Windows na serveru používá funkce založené na TCP požadavky k měření sítě. Pro uzly Windows na základě klienta používá funkce ICMP podle požadavků k měření sítě. V některých případech je cílová aplikace blokuje příchozí žádosti na základě protokolu ICMP, při uzly jsou Windows, na základě klienta. Řešení není schopen provést měření sítě. Doporučujeme používat Windows serverových uzlů v takových případech. 

9. Pokud nechcete vytvořit události stavu pro položky můžete vybrat, zrušte **povolit monitorování stavu v cílech kontrolovaných tímto testem**. 
10. Vyberte sledování podmínek. Můžete nastavit vlastní prahové hodnoty pro generování událost stavu tak, že zadáte prahové hodnoty. Pokaždé, když se hodnota podmínka překročí jeho zvolená prahová hodnota pro vybranou síť nebo pár podsítě, vygeneruje událost stavu. 
11. Vyberte **Uložit** uložte konfiguraci. 

    ![Konfigurace testů monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Názorný postup 

Přejděte do zobrazení řídicích panelů sledování výkonu sítě. Pokud chcete získat přehled o stavu jiné testy, které jste vytvořili, podívejte se na **monitorování připojení služby** stránky. 

![Stránka monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Vyberte tuto dlaždici zobrazíte podrobnosti o testy na **testy** stránky. V tabulce na levé straně můžete zobrazit stav bodu v čase a hodnota služby doba odezvy, latence sítě a ztráta paketů pro všechny testy. Chcete-li zobrazit snímek sítě později, v minulosti pomocí ovládacího prvku záznam stavu sítě. Výběrem testu v tabulce, kterou chcete prozkoumat. V grafu v podokně na pravé straně se zobrazí Historický trend ztráty, latence a hodnoty času odezvy. Vyberte **Podrobnosti testu** odkaz umožňující zobrazení výkonu z každého uzlu.

![Testy monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

V **testovací uzly** zobrazení, můžete sledovat připojení k síti z jednotlivých uzlů. Vyberte uzel, který má snížení výkonu. Toto je uzel, ve kterém se vyskytuje aplikace běží pomalu.

Zjistit, zda nízký výkon aplikace je z důvodu sítě nebo problém na straně aplikace zprostředkovatele pozorováním korelace mezi latence sítě a doba odezvy aplikace. 

* **Problém s aplikací:** prudký nárůst doby odezvy, ale konzistence v sítích s latencí naznačuje, že síť správně funguje a problémem může být kvůli problému na konci aplikace. 

    ![Problém s aplikací monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Síťové potíže:** prudký nárůst společně s odpovídající zásobníku v sítích s latencí doby odezvy naznačuje, že z důvodu zvýšení latence sítě může být zvýšení doby odezvy. 

    ![Potíže se sítí monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Pokud zjistíte, že problém je z důvodu sítě, vyberte **topologie** odkaz zobrazení k identifikaci problémových směrování na mapě topologie. Příklad je uveden na následujícím obrázku. Mimo 105 ms celkovou latenci mezi uzlem a koncový bod aplikace je 96 ms kvůli směrování označeny červeně. Po identifikaci problémových směrování, můžete využít nápravné opatření. 

![Testy monitorování připojení služby](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostika 

Pokud zjistíte anomálií, postupujte podle těchto kroků:

* Pokud doba odezvy služby, ztráta sítě a prodleva jsou zobrazeny jako není k dispozici, mohou být příčinou nejméně jednu z následujících důvodů:

    - Aplikace je mimo provoz.
    - Uzel použít pro kontrolu síťové připojení ke službě je mimo provoz.
    - Cíl zadaný v konfiguraci testu je nesprávný.
    - Uzel nemá žádné připojení k síti.

* Pokud se zobrazí čas odezvy platný, ale ztráty v síti, jakož i latenci, jsou uvedeny jako není k dispozici, může být příčinou nejméně jednu z následujících důvodů:

    - Pokud je uzel použít pro kontrolu síťové připojení ke službě klientské počítače s Windows, Cílová služba neblokuje požadavky ICMP nebo síťová brána firewall neblokuje požadavky protokolu ICMP, které pocházejí z uzlu.
    - **Provést měření sítě** zaškrtávací políčko je prázdný v konfiguraci testu. 

* Pokud je doba odezvy služby není k dispozici, ale platí ztráty v síti, jakož i latence, Cílová služba nemusí být webové aplikace. Upravte konfiguraci testu a zvolte typ testu jako **sítě** místo **webové**. 

* Pokud aplikace běží pomalu, určete, zda je nízký výkon aplikace z důvodu sítě nebo problém na straně aplikace zprostředkovatele.


## <a name="next-steps"></a>Další postup
[Hledání protokolů](log-analytics-log-searches.md) zobrazíte podrobné sítě výkonu datových záznamů.
