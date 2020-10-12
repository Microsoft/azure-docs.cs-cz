---
title: Práce se stávajícími místními proxy servery a Azure AD | Microsoft Docs
description: Obsahuje informace o tom, jak pracovat se stávajícími místními proxy servery.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: d177dce250d65b4f9d825c9d70916f70c4076d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88077505"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Práce se stávajícími místními proxy servery

Tento článek vysvětluje, jak nakonfigurovat konektory proxy aplikací aplikace Azure Active Directory (Azure AD) pro práci s odchozími proxy servery. Je určená pro zákazníky se síťovými prostředími, která mají existující proxy servery.

Začneme tím, že si vyhledáme tyto hlavní scénáře nasazení:

* Nakonfigurujte konektory pro obejití místních odchozích proxy serverů.
* Nakonfigurujte konektory pro přístup k Azure Proxy aplikací služby AD pro použití odchozího proxy serveru.
* Nakonfigurujte použití proxy serveru mezi konektorem a back-end aplikací.

Další informace o tom, jak fungují konektory, najdete v tématu [vysvětlení konektorů Azure proxy aplikací služby AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Nepoužívat odchozí proxy

Konektory mají základní součásti operačního systému, které vytvářejí odchozí požadavky. Tyto součásti se automaticky pokusí najít proxy server v síti pomocí automatického zjišťování (WPAD) webového proxy serveru.

Součásti operačního systému se pokusí najít proxy server provedením vyhledání DNS pro WPAD. domainsuffix. Pokud se vyhledávání v DNS vyřeší, požadavek HTTP se pak provede na IP adresu pro WPAD. dat. Tento požadavek se ve vašem prostředí stal skriptem konfigurace proxy serveru. Konektor používá tento skript k výběru odchozího proxy server. Provoz konektoru ale nemusí dál probíhat, protože na proxy serveru je potřeba další nastavení konfigurace.

Konektor můžete nakonfigurovat tak, aby vynechal místní proxy server, aby se zajistilo, že používá přímé připojení ke službám Azure. Doporučujeme tento přístup, pokud to vaše zásada sítě umožňuje, protože to znamená, že máte jednu méně konfigurací, kterou je třeba udržovat.

Pokud chcete pro konektor zakázat použití odchozího proxy serveru, upravte soubor C:\Program Files\Microsoft AAD App proxy Connector\ApplicationProxyConnectorService.exe.config a přidejte část *System.NET* , která se zobrazuje v této ukázce kódu:

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

Chcete-li zajistit, že služba Aktualizátor konektorů také obchází proxy server, udělejte v souboru ApplicationProxyConnectorUpdaterService.exe.config podobné změny. Tento soubor se nachází ve složce C:\Program Files\Microsoft AAD App proxy Aktualizátor konektorů.

Nezapomeňte vytvořit kopie původních souborů pro případ, že budete potřebovat vrátit se k souboru Default. config.

## <a name="use-the-outbound-proxy-server"></a>Použít odchozí proxy server

Některá prostředí vyžadují, aby veškerý odchozí provoz procházel prostřednictvím odchozího proxy serveru bez výjimky. Výsledkem je, že obcházení proxy serveru není možnost.

Provoz konektoru můžete nakonfigurovat tak, aby procházel prostřednictvím odchozího proxy serveru, jak je znázorněno na následujícím obrázku:

 ![Konfigurace provozu konektoru pro přechod prostřednictvím odchozího proxy serveru do Azure Proxy aplikací služby AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

V důsledku existence pouze odchozího provozu není potřeba konfigurovat příchozí přístup přes brány firewall.

> [!NOTE]
> Proxy aplikace nepodporuje ověřování u jiných proxy serverů. Účty síťové služby konektoru a aktualizace by měly být schopné se připojit k proxy serveru bez výzvy k ověření.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurace konektoru a souvisejících služeb pro přechod prostřednictvím odchozího proxy serveru

Pokud je v prostředí povolená možnost WPAD a správně nakonfigurovaná, konektor automaticky zjistí odchozí proxy server a pokusí se ho použít. Konektor ale můžete explicitně nakonfigurovat tak, aby procházel prostřednictvím odchozího proxy serveru.

Provedete to tak, že upravíte soubor proxy aplikace C:\Program Files\Microsoft AAD Connector\ApplicationProxyConnectorService.exe.config a přidáte část *System.NET* , která se zobrazuje v této ukázce kódu. Změňte *ProxyServer: 8080* tak, aby odrážela místní název proxy server nebo IP adresu a port, na kterém naslouchá. Hodnota musí mít http://předponu, i když používáte IP adresu.

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

Dále nakonfigurujte službu Aktualizátor konektorů tak, aby používala proxy, a to tak, že se v souboru Updater\ApplicationProxyConnectorUpdaterService.exe.config konektoru C:\Program Files\Microsoft AAD App proxy vytvoří podobná změna.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: konfigurace proxy serveru tak, aby povoloval přenosy z konektoru a souvisejících služeb, které se mají proflow

Existují čtyři aspekty, které je třeba vzít v úvahu při použití odchozího proxy serveru:

* Odchozí pravidla proxy serveru
* Ověřování proxy
* Proxy porty
* Kontrola TLS

#### <a name="proxy-outbound-rules"></a>Odchozí pravidla proxy serveru

Povolte přístup k následujícím adresám URL:

| URL | Jak se používá |
| --- | --- |
| \*. msappproxy.net<br>\*. servicebus.windows.net | Komunikace mezi konektorem a cloudovou službou proxy aplikací |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Konektor používá tyto adresy URL k ověření certifikátů. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com <br> *. microsoftonline-p.com<br>*. msauth.NET <br> *. msauthimages.net<br>*. msecnd.NET <br> *. msftauth.net<br>*. msftauthimages.NET <br> *. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com:80 | Konektor tyto adresy URL používá během procesu registrace. |

Pokud vaše brána firewall nebo proxy server umožňuje konfigurovat seznamy povolených serverů DNS, můžete povolit připojení k \* příponám. msappproxy.NET a \* . ServiceBus.Windows.NET. V takovém případě je potřeba, abyste povolili přístup k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Rozsahy IP adres se aktualizují každý týden.

Pokud nemůžete připojení podle plně kvalifikovaného názvu domény a potřebujete místo toho zadat rozsahy IP adres, použijte tyto možnosti:

* Povolí konektoru odchozí přístup ke všem cílům.
* Povolí konektoru odchozí přístup ke všem [rozsahům IP adres datacentra Azure](https://www.microsoft.com//download/details.aspx?id=41653). Výzvou k použití seznamu rozsahů IP adres datacentra Azure je, že se každý týden aktualizuje. K zajištění toho, aby se pravidla přístupu aktualizovala, je potřeba umístit proces. Pouze použití podmnožiny IP adres může způsobit přerušení vaší konfigurace.

#### <a name="proxy-authentication"></a>Ověřování proxy

Ověřování proxy serveru se momentálně nepodporuje. Naše aktuální doporučení je povolení konektoru anonymního přístupu k internetovým cílům.

#### <a name="proxy-ports"></a>Proxy porty

Konektor zpřístupňuje odchozí připojení založená na protokolu TLS pomocí metody CONNECT. Tato metoda v podstatě nastaví tunelové propojení prostřednictvím odchozího proxy serveru. Nakonfigurujte proxy server, aby povolovaly tunelování na porty 443 a 80.

> [!NOTE]
> Když Service Bus spouští přes protokol HTTPS, používá port 443. Ve výchozím nastavení se ale Service Bus pokusí přímá připojení TCP a vrátí se k HTTPS jenom v případě, že se přímé připojení nepovede.

#### <a name="tls-inspection"></a>Kontrola TLS

Nepoužívejte kontrolu TLS pro přenos konektorů, protože způsobuje problémy s přenosy konektoru. Konektor používá certifikát k ověření ve službě proxy aplikací a tento certifikát může být během kontroly TLS ztracen.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Konfigurace použití proxy serveru mezi konektorem a back-end aplikací
Použití předávacího proxy serveru pro komunikaci směrem k back-endové aplikaci může být v některých prostředích speciálním požadavkem.
Pokud to chcete povolit, postupujte prosím podle následujících kroků:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Krok 1: přidejte do serveru požadovanou hodnotu registru
1. Pokud chcete povolit použití výchozího proxy serveru, přidejte `UseDefaultProxyForBackendRequests = 1` do klíče registru konfigurace konektoru, který se nachází v HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector, následující hodnotu registru (DWORD).

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Krok 2: ruční konfigurace proxy server pomocí příkazu netsh
1.  Povolte zásadám skupiny vytvořit nastavení proxy serveru na počítač. Najdete ho v: počítač \ \ součásti systému Windows\internet Explorer. Tento postup je potřeba nastavit, aby tato zásada nebyla nastavená na jednotlivé uživatele.
2.  Spusťte `gpupdate /force` na serveru nebo restartujte server, aby se zajistilo, že bude používat aktualizované nastavení zásad skupiny.
3.  Spusťte příkazový řádek se zvýšenými oprávněními s právy správce a zadejte `control inetcpl.cpl` .
4.  Nakonfigurujte požadovaná nastavení proxy serveru. 

Tato nastavení nastaví konektor pro komunikaci do Azure a back-endu stejný proxy server pro předávání. Pokud konektor ke komunikaci Azure nevyžaduje žádný dopředný proxy server nebo jiný dopředný proxy server, můžete ho nastavit změnou ApplicationProxyConnectorService.exe.config, jak je popsáno v částech obejití odchozích proxy serverů nebo použití odchozího proxy server.

> [!NOTE]
> Existují různé způsoby konfigurace internetového proxy serveru v operačním systému. Nastavení proxy serveru nakonfigurovaná pomocí příkazu NETSH WINHTTP (spustit `NETSH WINHTTP SHOW PROXY` pro ověření) přepsat nastavení proxy serveru, které jste nakonfigurovali v kroku 2. 

Služba aktualizačního konektoru bude používat i proxy počítače. Toto chování lze změnit úpravou ApplicationProxyConnectorUpdaterService.exe.config souboru.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Řešení problémů s proxy konektorem a problémy s připojením služby

Nyní byste měli vidět veškerý tok provozu prostřednictvím proxy serveru. Pokud máte problémy, měli byste využít následující informace pro řešení potíží.

Nejlepším způsobem, jak identifikovat a řešit potíže s připojením konektoru, je zařídit síťové zachycení při spuštění služby konektoru. Tady jsou některé rychlé tipy pro zachytávání a filtrování trasování sítě.

Můžete použít nástroj pro monitorování podle vašeho výběru. Pro účely tohoto článku jsme použili Microsoft Message Analyzer.

Následující příklady jsou specifické pro analyzátor zpráv, ale zásady je možné použít na jakýkoli nástroj pro analýzu.

### <a name="take-a-capture-of-connector-traffic"></a>Pořiďte si zachycení provozu konektoru.

Při prvotním řešení potíží proveďte následující kroky:

1. Ve službě Services. msc zastavte službu konektoru Azure Proxy aplikací služby AD.

   ![Služba konektoru Azure Proxy aplikací služby AD v Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Spusťte analyzátor zpráv jako správce.
1. Vyberte **Spustit místní trasování**.
1. Spusťte službu konektoru Azure Proxy aplikací služby AD.
1. Zastavte síťové zachycení.

   ![Snímek obrazovky se zobrazí tlačítko Zastavit síťové zachycení](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ověřte, jestli přenos konektoru obchází odchozí proxy servery.

Pokud jste proxy server aplikace nakonfigurovali tak, aby se nepoužívaly proxy servery a připojili se přímo k proxy službě aplikace, chcete se podívat na síťové zachycení pro neúspěšné pokusy o připojení TCP.

Tyto pokusy Identifikujte pomocí filtru analyzátoru zpráv. `property.TCPSynRetransmit`Do pole Filtr zadejte a vyberte **použít**.

Paket SYN je první paket odeslaný k navázání připojení TCP. Pokud tento paket nevrátí odpověď, dojde k pokusu o SYN. K zobrazení všech přenesených SYNs můžete použít předchozí filtr. Pak můžete ověřit, zda tyto SYNs odpovídají jakýmkoli přenosům spojeným s konektorem.

Pokud očekáváte, že konektor bude přímá připojení ke službám Azure, SynRetransmit odpovědi na portu 443 jsou známkou toho, že máte potíže se sítí nebo bránou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Ověřte, jestli provoz konektoru používá odchozí proxy servery.

Pokud jste nakonfigurovali provoz konektoru proxy aplikací tak, aby procházel servery proxy, chcete vyhledat neúspěšná připojení HTTPS k vašemu proxy serveru.

Pokud chcete pro tyto pokusy o připojení filtrovat zachytávání sítě, zadejte `(https.Request or https.Response) and tcp.port==8080` do filtru analyzátoru zpráv a nahraďte 8080 portem služby proxy. Výběrem **použít** zobrazíte výsledky filtru.

Předchozí filtr ukazuje pouze požadavky HTTPs a odpovědi na nebo z portu proxy serveru. Hledáte žádosti o připojení, které ukazují komunikaci s proxy server. Po úspěchu získáte odpověď HTTP OK (200).

Pokud vidíte další kódy odpovědí, například 407 nebo 502, znamená to, že proxy vyžaduje ověření nebo nepovoluje provoz z nějakého jiného důvodu. V tuto chvíli propojíte tým podpory proxy server.

## <a name="next-steps"></a>Další kroky

* [Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
* Pokud máte problémy s připojením konektoru, zeptejte se na stránku s [dotazem na Microsoft Q&pro Azure Active Directory](https://docs.microsoft.com/answers/topics/azure-active-directory.html) nebo vytvořte lístek s týmem podpory.
