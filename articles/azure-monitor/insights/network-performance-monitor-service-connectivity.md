---
title: Řešení služby Sledování výkonu sítě Připojení služby – Azure Log Analytics
description: Pomocí funkce Sledování připojení služby v programu Sledování výkonu sítě můžete sledovat připojení k síti ke všem koncovým bodům, které má otevřený port TCP.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249240"
---
# <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Funkci Sledování připojení služby v [programu Sledování výkonu sítě](network-performance-monitor.md) můžete použít ke sledování připojení k síti k libovolnému koncovému bodu s otevřeným portem TCP. Mezi tyto koncové body patří weby, aplikace SaaS, aplikace PaaS a databáze SQL. 

Pomocí monitoru připojení služby můžete provádět následující funkce: 

- Sledujte síťové připojení k aplikacím a síťovým službám z více poboček nebo umístění. Mezi aplikace a síťové služby patří Office 365, Dynamics CRM, interní obchodní aplikace a databáze SQL.
- Integrované testy slouží ke sledování síťového připojení ke koncovým bodům Office 365 a Dynamics 365. 
- Určete dobu odezvy, latenci sítě a ztrátu paketů při připojování ke koncovému bodu.
- Zjistěte, zda je nízký výkon aplikace z důvodu sítě nebo z důvodu nějakého problému na konci poskytovatele aplikace.
- Identifikujte aktivní body v síti, které mohou způsobovat nízký výkon aplikace zobrazením latence, kterou přispívá každý přesun na mapě topologie.


![Monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurace 
Chcete-li otevřít konfiguraci nástroje Sledování výkonu sítě, otevřete [řešení Sledování výkonu sítě](network-performance-monitor.md) a vyberte **konfigurovat**.

![Konfigurace Network Performance Monitoru](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů analýzy protokolů pro monitorování
Povolte následující pravidla brány firewall na uzlech používaných pro monitorování, aby řešení mohlo zjistit topologii z uzlů do koncového bodu služby: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Vytvořit testy sledování připojení služby 

Začněte vytvářet testy pro sledování připojení k síti ke koncovým bodům služby.

1. Vyberte kartu **Monitor připojení služby.**
2. Vyberte **Přidat test**a zadejte název a popis testu. Můžete vytvořit maximálně 450 testů na pracovní prostor. 
3. Vyberte typ testu:<br>

    * Výběrem **možnosti Web** můžete sledovat připojení ke službě, která reaguje na požadavky HTTP/S, například outlook.office365.com nebo bing.com.<br>
    * Vyberte **síť,** chcete-li sledovat připojení ke službě, která reaguje na požadavky TCP, ale nereaguje na požadavky HTTP/S, jako je například server SQL, server FTP nebo port SSH. 
    * Příklad: Chcete-li vytvořit webový test pro účet úložiště objektů blob, vyberte **web** a zadejte cíl jako *váš účet úložiště*.blob.core.windows.net. Podobně můžete vytvořit testy pro jiné úložiště tabulek, úložiště front a soubory Azure pomocí [tohoto odkazu.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Pokud nechcete provádět měření sítě, jako je latence sítě, ztráta paketů a zjišťování topologie, zrušte zaškrtnutí políčka **Provést měření sítě.** Nechte ji vybranou, abyste získali maximální užitek z této schopnosti. 
5. Do **pole Cíl**zadejte adresu URL/FQDN/IP adresu, na kterou chcete sledovat připojení k síti.
6. Do **pole Číslo portu**zadejte číslo portu cílové služby. 
7. Do **pole Frekvence testování**zadejte hodnotu pro četnost spuštění testu. 
8. Vyberte uzly, ze kterých chcete sledovat připojení k síti ke službě. Ujistěte se, že počet agentů přidaných na test je menší než 150. Každý agent může testovat maximálně 150 koncových bodů/agentů.

    >[!NOTE]
    > U uzlů založených na serveru systému Windows tato funkce používá k měření v síti požadavky založené na protokolu TCP. U klientských uzlů systému Windows tato funkce používá k měření v síti požadavky založené na protokolu ICMP. V některých případech cílová aplikace blokuje příchozí požadavky založené na protokolu ICMP, pokud jsou uzly založené na systému Windows na základě klienta. Řešení nemůže provádět měření sítě. V takových případech doporučujeme používat uzly založené na serveru systému Windows. 

9. Pokud nechcete vytvářet události stavu pro vybrané položky, zrušte zaškrtnutí **políčka Povolit monitorování stavu v cílech, na které se vztahuje tento test**. 
10. Zvolte podmínky monitorování. Vlastní prahové hodnoty pro generování událostí stavu můžete nastavit zadáním prahových hodnot. Vždy, když hodnota podmínky překročí vybranou prahovou hodnotu pro vybranou síť nebo dvojici podsítě, je vygenerována událost stavu. 
11. Chcete-li uložit konfiguraci, vyberte **uložit.** 

    ![Testovací konfigurace nástroje Sledování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Názorný postup 

Přejděte do zobrazení řídicího panelu Sledování výkonu sítě. Chcete-li získat souhrn stavu různých testů, které jste vytvořili, podívejte se na stránku **Sledování připojení služby.** 

![Stránka Monitor připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Vyberte dlaždici, chcete-li zobrazit podrobnosti o testech na stránce **Testy.** V tabulce vlevo můžete zobrazit stav v čase a hodnotu doby odezvy služby, latence sítě a ztráty paketů pro všechny testy. Pomocí ovládacího prvku Zapisovač stavu sítě můžete zobrazit snímek sítě v jiném čase v minulosti. Vyberte test v tabulce, kterou chcete prozkoumat. V grafech v podokně vpravo můžete zobrazit historický trend hodnot ztráty, latence a doby odezvy. Chcete-li zobrazit výkon z každého uzlu, vyberte odkaz **Podrobnosti testu.**

![Testy sledování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

V zobrazení **Test Uzly** můžete sledovat připojení k síti z každého uzlu. Vyberte uzel, který má snížení výkonu. Toto je uzel, kde je pozorováno, že aplikace běží pomalu.

Zjistěte, zda je nízký výkon aplikace z důvodu sítě nebo problému na konci poskytovatele aplikace sledováním korelace mezi dobou odezvy aplikace a latencí sítě. 

* **Problém s aplikací:** Špička v době odezvy, ale konzistence v latenci sítě naznačuje, že síť funguje dobře a problém může být způsoben problémem na konci aplikace. 

    ![Problém s aplikací Sledování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problém se sítí:** Špička v době odezvy spolu s odpovídající špičkou latence sítě naznačuje, že zvýšení doby odezvy může být způsobeno zvýšenílatence sítě. 

    ![Problém se sítí Sledování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Poté, co zjistíte, že problém je z důvodu sítě, vyberte odkaz zobrazení **topologie** k identifikaci problematické směrování na mapě topologie. Příklad je zobrazen na následujícím obrázku. Z celkové latence 105 ms mezi uzlovým a koncovým bodem aplikace je 96 ms způsobeno červeným označením směrování. Po identifikaci problematické hop, můžete provést nápravná opatření. 

![Testy sledování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostika 

Pokud zaznamenáte abnormalitu, postupujte takto:

* Pokud jsou doba odezvy služby, ztráta sítě a latence zobrazeny jako NA, může být příčinou jeden nebo více z následujících důvodů:

    - Aplikace je vypnutá.
    - Uzel používaný pro kontrolu připojení k síti ke službě je vypnutý.
    - Cíl zadaný v konfiguraci testu je nesprávný.
    - Uzel nemá žádné připojení k síti.

* Pokud je zobrazena platná doba odezvy služby, ale ztráta sítě a latence jsou zobrazeny jako NA, může být příčinou jeden nebo více z následujících důvodů:

    - Pokud je uzel používaný ke kontrole síťového připojení ke službě klientským počítačem se systémem Windows, cílová služba blokuje požadavky ICMP nebo síťová brána firewall blokuje požadavky ICMP, které pocházejí z uzlu.
    - **Zaškrtávací** políčko Provést měření sítě je v testovací konfiguraci prázdné. 

* Pokud je doba odezvy služby NA, ale ztráta sítě a latence jsou platné, cílová služba nemusí být webová aplikace. Upravte konfiguraci testu a zvolte typ testu jako **síť** místo **jako web**. 

* Pokud aplikace běží pomalu, zjistěte, zda je nízký výkon aplikace z důvodu sítě nebo problému na konci poskytovatele aplikace.

## <a name="gcc-office-urls-for-us-government-customers"></a>Adresy URL sady GCC Office pro zákazníky státní správy USA
Pro oblast Virginie vlády USA jsou vestavěné adresy URL dod. Zákazníci používající adresy URL GCC musí vytvořit vlastní testy a přidat každou adresu URL jednotlivě.

| Pole | GCC |
|:---   |:--- |
| Portál Office 365 a sdílení | portal.apps.mil |
| Ověření a identita Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| Týmy MS | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Další kroky
[Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) pro zobrazení podrobných záznamů dat o výkonu sítě.
