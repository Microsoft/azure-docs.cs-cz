---
title: Práce s existujícími místními proxy servery a službou Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak pracovat s existujícími místními proxy servery.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe3a63e119fed6825982b9de13bc78cb7da5415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481394"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Práce s existujícími místními proxy servery

Tento článek vysvětluje, jak nakonfigurovat konektory proxy aplikací azure active directory (Azure AD) pro práci s odchozíproxy servery. Je určen pro zákazníky se síťovými prostředími, kteří mají existující proxy servery.

Začneme tím, že se podíváme na tyto hlavní scénáře nasazení:

* Nakonfigurujte konektory tak, aby obcovaly místní odchozí proxy servery.
* Nakonfigurujte konektory tak, aby pro přístup k proxy aplikací Azure AD používaly odchozí proxy server.

Další informace o tom, jak konektory fungují, [najdete v tématu Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Obcházet odchozí proxy servery

Konektory mají základní součásti operačního systému, které provádějí odchozí požadavky. Tyto součásti se automaticky pokoušejí vyhledat proxy server v síti pomocí funkce Automatické zjišťování webového serveru proxy (WPAD).

Součásti operačního systému se pokoušejí najít proxy server provedením vyhledávání DNS pro wpad.domainsuffix. Pokud se vyhledávání v dns vyřeší, je požadavek HTTP proveden na adresu IP souboru wpad.dat. Tento požadavek se stane skriptem konfigurace proxy ve vašem prostředí. Konektor používá tento skript k výběru odchozího proxy serveru. Provoz konektoru však stále nemusí projít, z důvodu další nastavení konfigurace potřebné na serveru proxy.

Můžete nakonfigurovat konektor obejít místní proxy server a zajistit, že používá přímé připojení ke službám Azure. Doporučujeme tento přístup, pokud to umožňují zásady sítě, protože to znamená, že máte o jednu konfiguraci méně, kterou chcete udržovat.

Chcete-li zakázat použití odchozího proxy serveru pro konektor, upravte soubor C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config a přidejte *oddíl system.net* zobrazený v této ukázce kódu:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Chcete-li zajistit, aby služba Aktualizace konektoru také obchází proxy server, proveďte podobnou změnu souboru ApplicationProxyConnectorUpdaterService.exe.config. Tento soubor je umístěn na adrese C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Ujistěte se, že kopie původních souborů, v případě, že potřebujete vrátit k výchozím souborům .config.

## <a name="use-the-outbound-proxy-server"></a>Použití odchozího proxy serveru

Některá prostředí vyžadují, aby veškerý odchozí provoz procházel odchozím proxy serverem bez výjimky. V důsledku toho vynechání proxy není možnost.

Můžete nakonfigurovat přenoskonektor u dálného proxy serveru, jak je znázorněno na následujícím diagramu:

 ![Konfigurace provozu konektoru pro průchod odchozího proxy serveru proxy služby Proxy aplikace Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

V důsledku toho, že máte pouze odchozí provoz, není nutné konfigurovat příchozí přístup přes brány firewall.

> [!NOTE]
> Proxy aplikace nepodporuje ověřování pro jiné proxy servery. Účty síťových služeb konektoru nebo aktualizačního programu by měly být schopny se připojit k serveru proxy, aniž by byly vyzvány k ověření.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurace konektoru a souvisejících služeb pro cházet přes odchozí proxy server

Pokud je wpad povolen v prostředí a správně nakonfigurován, konektor automaticky zjišťuje odchozí proxy server a pokusí se jej použít. Konektor však můžete explicitně nakonfigurovat tak, aby procházel odchozím proxy serverem.

Chcete-li tak učinit, upravte soubor C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config a přidejte *system.net* oddíl zobrazený v této ukázce kódu. Změňte *proxyserver:8080* tak, aby odrážel název místního proxy serveru nebo IP adresu a port, na který naslouchá. Hodnota musí mít předponu http:// i v případě, že používáte adresu IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Dále nakonfigurujte službu Aktualizace konektoru pro použití proxy serveru provedením podobné změny jako soubor C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Konfigurace proxy serveru tak, aby provoz z konektoru a souvisejících služeb toku přes

Existují čtyři aspekty, které je třeba zvážit na odchozí proxy server:

* Odchozí pravidla proxy serveru
* Ověřování serveru proxy
* Porty proxy serveru
* Kontrola TLS

#### <a name="proxy-outbound-rules"></a>Odchozí pravidla proxy serveru

Povolit přístup k následujícím adresám URL:

| zprostředkovatele identity | Jak se používá |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikace mezi konektorem a cloudovou službou Proxy aplikace |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Konektor používá tyto adresy URL k ověření certifikátů |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*Msftauth.net<br>.msecnd.net*.msftauth.net<br>*.msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Konektor používá tyto adresy URL během procesu registrace. |

Pokud brána firewall nebo proxy server umožňuje konfigurovat seznamy povolených služeb DNS, můžete povolit připojení k \*msappproxy.net a \*servicebus.windows.net. Pokud ne, musíte povolit přístup k [rozsahům IP adres Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653). Rozsahy IP adres jsou aktualizovány každý týden.

Pokud nemůžete povolit připojení pomocí programu ReQDN a potřebujete místo toho zadat rozsahy IP adres, použijte tyto možnosti:

* Povolte konektoru odchozí přístup do všech cílů.
* Povolte konektoru odchozí přístup ke všem [rozsahům IP adres datového centra Azure](https://www.microsoft.com//download/details.aspx?id=41653). Problém s použitím seznamu rozsahů IP adres datového centra Azure spočívá, že se aktualizuje každý týden. Je třeba zavést proces, který zajistí, že vaše pravidla přístupu budou odpovídajícím způsobem aktualizována. Pouze použití podmnožiny IP adres může způsobit přerušení konfigurace.

#### <a name="proxy-authentication"></a>Ověřování serveru proxy

Ověřování serveru proxy není aktuálně podporováno. Naším současným doporučením je umožnit konektoru anonymní přístup k internetovým cílům.

#### <a name="proxy-ports"></a>Porty proxy serveru

Konektor provádí odchozí připojení založená na TLS pomocí metody CONNECT. Tato metoda v podstatě nastaví tunelové propojení prostřednictvím odchozí proxy serveru. Nakonfigurujte proxy server tak, aby tunelové propojení umožňovalo porty 443 a 80.

> [!NOTE]
> Při spuštění služby Service Bus přes protokol HTTPS používá port 443. Ve výchozím nastavení se však service bus pokusí o přímé připojení TCP a vrátí se zpět na protokol HTTPS pouze v případě, že se nezdaří přímé připojení.

#### <a name="tls-inspection"></a>Kontrola TLS

Nepoužívejte kontrolu TLS pro provoz konektoru, protože způsobuje problémy pro provoz konektoru. Konektor používá certifikát k ověření služby Proxy aplikace a tento certifikát může být během kontroly TLS ztracen.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Poradce při potížích s proxy konektory a problémy s připojením služby

Nyní byste měli vidět veškerý provoz protékající proxy. Pokud máte problémy, následující informace o řešení potíží by měly pomoci.

Nejlepší způsob, jak identifikovat a řešit problémy s připojením konektoru, je zachytit síť při spuštění služby konektoru. Zde je několik rychlých tipů pro zachycení a filtrování trasování sítě.

Můžete použít monitorovací nástroj dle vašeho výběru. Pro účely tohoto článku jsme použili Microsoft Message Analyzer. Můžete [si jej stáhnout od společnosti Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Následující příklady jsou specifické pro Analyzátor zpráv, ale zásady lze použít pro libovolný analytický nástroj.

### <a name="take-a-capture-of-connector-traffic"></a>Zachyťte provoz konektoru

Počáteční řešení potíží provedete následující kroky:

1. Z services.msc, zastavit službu Azure AD Proxy Connector.

   ![Služba Konektor proxy aplikací Azure AD v services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Spusťte analyzátor zpráv jako správce.
1. Vyberte **možnost Spustit místní trasování**.
1. Spusťte službu Konektor proxy aplikací Azure AD.
1. Zastavte zachytávání sítě.

   ![Snímek obrazovky zobrazuje tlačítko Zastavit digitalizaci sítě](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Zkontrolujte, zda přenos konektoru obchází odchozí proxy servery

Pokud jste nakonfigurovali konektor proxy aplikací tak, aby obcoval proxy servery a připojil se přímo ke službě Proxy aplikace, chcete v síťovém sběru vyhledat neúspěšné pokusy o připojení TCP.

K identifikaci těchto pokusů použijte filtr Analyzátor zpráv. Zadejte `property.TCPSynRetransmit` do pole filtru a vyberte **Použít**.

Paket SYN je prvním odeslanou paketem pro navázání připojení TCP. Pokud tento paket nevrátí odpověď, syn je opakován. Pomocí předchozího filtru můžete zobrazit všechny opakovaně přenášené kódy SYN. Potom můžete zkontrolovat, zda tyto sítě SYN odpovídají jakémukoli provozu souvisejícímu s konektorem.

Pokud očekáváte, že konektor k přímému připojení ke službám Azure, SynRetransmit odpovědi na portu 443 jsou známkou, že máte problém se sítí nebo bránou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Zkontrolujte, zda provoz konektoru používá odchozí proxy servery

Pokud jste nakonfigurovali provoz konektoru proxy aplikace pro servery proxy, chcete vyhledat neúspěšná připojení https k serveru proxy.

Chcete-li filtrovat zachycení sítě pro `(https.Request or https.Response) and tcp.port==8080` tyto pokusy o připojení, zadejte do filtru Analyzátor zpráv a nahraďte 8080 portem služby proxy. Chcete-li zobrazit výsledky filtru, vyberte **použít.**

Předchozí filtr zobrazuje pouze požadavky HTTP a odpovědi na/z portu proxy. Hledáte connect požadavky, které ukazují komunikaci s proxy serverem. Po úspěchu získáte odpověď HTTP OK (200).

Pokud se zobrazí jiné kódy odpovědí, například 407 nebo 502, znamená to, že proxy server vyžaduje ověření nebo nepovoluje provoz z nějakého jiného důvodu. V tomto okamžiku můžete zapojit váš tým podpory proxy serveru.

## <a name="next-steps"></a>Další kroky

* [Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
* Pokud máte problémy s problémy s připojením konektoru, zeptejte se na svůj dotaz ve [fóru Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) nebo vytvořte lístek s naším týmem podpory.
