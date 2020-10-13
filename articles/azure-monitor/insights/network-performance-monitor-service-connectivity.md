---
title: Připojení služby řešení Network Performance Monitor – Azure Log Analytics
description: Pomocí možnosti monitorování připojení služby v Network Performance Monitor můžete monitorovat síťové připojení ke koncovému bodu, který má otevřený port TCP.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 18c07266cec68aaf6e95fe4085b9d21244fecff4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761035"
---
# <a name="service-connectivity-monitor"></a>Monitorování připojení služby

Pomocí možnosti monitorování připojení služby v nástroji [Network Performance Monitor](network-performance-monitor.md) můžete monitorovat síťové připojení ke koncovému bodu, který má otevřený port TCP. K takovým koncovým bodům patří weby, aplikace SaaS, PaaS aplikace a databáze SQL. 

S monitorováním připojení služby můžete provádět následující funkce: 

- Monitorujte síťové připojení k vašim aplikacím a síťovým službám z několika poboček nebo umístění. Mezi aplikace a síťové služby patří Microsoft 365, Dynamics CRM, interní obchodní aplikace a databáze SQL.
- Pomocí integrovaných testů můžete monitorovat připojení k síti Microsoft 365 a koncovým bodům Dynamics 365. 
- Určete dobu odezvy, latenci sítě a ztrátu paketů, které se při připojování ke koncovému bodu vyskytly.
- Určete, zda je slabý výkon aplikace z důvodu sítě nebo kvůli nějakému problému na konci poskytovatele aplikace.
- Identifikujte aktivní body v síti, které mohou způsobovat špatný výkon aplikace, zobrazením latence, na kterou přispělo každé směrování na mapě topologie.


![Monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurace 
Chcete-li otevřít konfiguraci pro Network Performance Monitor, otevřete [řešení Network Performance Monitor](network-performance-monitor.md) a vyberte **Konfigurovat**.

![Konfigurace Network Performance Monitoru](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů Log Analytics pro monitorování
U uzlů používaných pro monitorování Povolte následující pravidla brány firewall, aby řešení mohlo zjistit topologii z vašich uzlů do koncového bodu služby: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Vytvořit testy monitorování připojení služby 

Začněte vytvářet testy pro monitorování síťového připojení k koncovým bodům služby.

1. Vyberte kartu **monitorování připojení služby** .
2. Vyberte **Přidat test**a zadejte název a popis testu. Můžete vytvořit maximálně 450 testů na jeden pracovní prostor. 
3. Vyberte typ testu:<br>

    * Vyberte **Web** a sledujte připojení ke službě, která reaguje na požadavky HTTP/S, jako je například outlook.office365.com nebo Bing.com.<br>
    * Vyberte **síť** pro monitorování připojení ke službě, která reaguje na požadavky TCP, ale nereaguje na požadavky HTTP/S, jako je třeba SQL Server, server FTP nebo port SSH. 
    * Příklad: Chcete-li vytvořit webový test na účet úložiště objektů blob, vyberte možnost **Web** a zadejte cíl jako *yourstorageaccount*. blob.Core.Windows.NET. Podobně můžete pomocí [tohoto odkazu](../../storage/common/storage-account-overview.md#storage-account-endpoints) vytvořit testy pro jiné úložiště tabulek, úložiště front a soubory Azure.
4. Pokud nechcete provádět měření sítě, například latence sítě, ztráty paketů a zjišťování topologie, zrušte zaškrtnutí políčka **provádět měření sítě** . Nechte si ho nechat vybraný, aby bylo možné získat maximální výhodu z této schopnosti. 
5. V části **cíl**zadejte adresu URL/plně kvalifikovaný název domény nebo IP adresu, na které chcete monitorovat síťové připojení.
6. Do pole **číslo portu**zadejte číslo portu cílové služby. 
7. V části **frekvence testování**zadejte hodnotu, jak často chcete spustit test. 
8. Vyberte uzly, ze kterých chcete monitorovat síťové připojení ke službě. Zajistěte, aby byl počet přidaných agentů na test menší než 150. Každý agent může testovat maximálně 150 koncových bodů a agentů.

    >[!NOTE]
    > Pro uzly založené na Windows serveru funkce pro měření sítě využívá požadavky založené na protokolu TCP. Pro klientské uzly systému Windows využívá funkce k provedení měření sítě požadavky založené na protokolu ICMP. V některých případech cílová aplikace blokuje příchozí požadavky založené na protokolu ICMP, pokud jsou uzly na bázi klienta systému Windows. Řešení nemůže provádět měření sítě. V takových případech doporučujeme používat uzly založené na Windows serveru. 

9. Pokud nechcete vytvářet události stavu pro vybrané položky, zrušte zaškrtnutí políčka **Povolit monitorování stavu v cílech, na které se tento test vztahuje**. 
10. Vyberte možnost podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování událostí stavu zadáním prahových hodnot. Pokaždé, když hodnota podmínky překročí zvolenou prahovou hodnotu pro vybranou síť nebo pár podsítí, bude vygenerována událost stavu. 
11. Kliknutím na **Uložit** uložte konfiguraci. 

    ![Konfigurace testu monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Názorný postup 

Přejít do zobrazení řídicího panelu Network Performance Monitor. Chcete-li získat souhrn stavu různých testů, které jste vytvořili, podívejte se na stránku **monitorování připojení služby** . 

![Stránka monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Výběrem dlaždice zobrazíte podrobnosti o testech na stránce **testy** . V tabulce na levé straně můžete zobrazit stav a hodnotu doby odezvy služby, latenci sítě a ztráty paketů pro všechny testy. Pomocí ovládacího prvku pro záznam o stavu sítě můžete snímek sítě zobrazit v jinou dobu v minulosti. V tabulce, kterou chcete prozkoumat, vyberte test. V grafech v pravém podokně můžete zobrazit historický trend hodnot doby ztráty, latence a doby odezvy. Vyberte odkaz **Podrobnosti testu** pro zobrazení výkonu z každého uzlu.

![Testy monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

V zobrazení **testovacích uzlů** můžete sledovat připojení k síti z každého uzlu. Vyberte uzel, který má snížení výkonu. Toto je uzel, ve kterém je aplikace spuštěna pomalu.

Zjistěte, jestli je nízký výkon aplikace v důsledku sítě nebo problému na konci poskytovatele aplikace, a to tak, že budete pozorovat korelaci mezi dobou odezvy aplikace a latencí sítě. 

* **Problém s aplikací:** Špička v době odezvy, ale konzistence v případě latence sítě naznačuje, že síť funguje správně a problém může být způsoben problémem na konci aplikace. 

    ![Monitorování připojení služby – problém aplikace](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problém se sítí:** Špička v době odezvy spolu s odpovídající špičkou v latenci sítě naznačuje, že nárůst doby odezvy může být způsoben nárůstem latence sítě. 

    ![Řešení potíží se sítí monitorování připojení služby](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Až zjistíte, že se jedná o problém z důvodu sítě, vyberte odkaz zobrazení **topologie** a Identifikujte komplikované směrování na mapě topologie. Příklad je znázorněn na následujícím obrázku. Z celkové latence 105-MS mezi uzlem a koncovým bodem aplikace je 96 MS z důvodu směrování označeného červeně. Po určení směrování komplikované můžete provést nápravná opatření. 

![Monitorování připojení služby – topologie koncových bodů](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostika 

Pokud obdržíte nezvyklosti, postupujte podle následujících kroků:

* Pokud se doba odezvy, ztráta sítě a latence zobrazují jako NA, jeden nebo více z následujících důvodů může být příčinou:

    - Aplikace nefunguje.
    - Uzel, který se používá pro kontrolu připojení k síti ke službě, je mimo provoz.
    - Cíl zadaný v konfiguraci testu není správný.
    - Uzel nemá žádné síťové připojení.

* Pokud se zobrazí platná doba odezvy služby, ale ztráta sítě a latence se zobrazí jako na, jeden nebo více z následujících důvodů může být příčinou:

    - Pokud uzel použitý k ověření síťového připojení ke službě je klientský počítač Windows, znamená to, že cílová služba blokuje žádosti ICMP, nebo síťová brána firewall blokuje žádosti ICMP, které pocházejí z uzlu.
    - Zaškrtávací políčko **provádět měření sítě** je v konfiguraci testu prázdné. 

* Pokud je doba odezvy služby NA, ale ztráta sítě a latence je platná, cílová služba nemusí být webová aplikace. Upravte konfiguraci testu a jako místo **webu**vyberte typ testu **síť** . 

* Pokud je aplikace spuštěná pomalu, zjistěte, jestli je slabý výkon aplikace v důsledku sítě nebo problému na konci poskytovatele aplikace.

## <a name="gcc-office-urls-for-us-government-customers"></a>Adresy URL Office v RSZ pro zákazníky státní správy USA
Pro oblast) – Virginia pro státní správu USA jsou integrované NPMy jenom adresy URL DOD. Zákazníci, kteří používají adresy URL RSZ, potřebují vytvořit vlastní testy a přidat každou adresu URL jednotlivě.

| Pole | GCC |
|:---   |:--- |
| Portál Office 365 a sdílený | portal.apps.mil |
| Ověřování a identita pro Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Online Office | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Další kroky
V [protokolech hledání](../log-query/log-query-overview.md) zobrazíte podrobné záznamy dat o výkonu sítě.

