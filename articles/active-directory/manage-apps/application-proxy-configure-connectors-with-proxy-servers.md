---
title: Práce s existující místní proxy servery a službou Azure AD | Dokumentace Microsoftu
description: Popisuje, jak pracovat s existující místní proxy servery.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: aa13d7ccbadde5f8b813c0265df31014b773df7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164414"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Práce s existující místní proxy servery

Tento článek vysvětluje, jak konfigurovat konektory Proxy aplikací Azure Active Directory (Azure AD) pro práci s odchozí proxy servery. Je určená pro zákazníky pomocí síťových prostředí, které mají existující proxy servery.

Můžeme začít hledáním v těchto scénářích hlavní nasazení:
* Nakonfigurujte konektory pro obejití odchozí proxy vaší místní služby.
* Nakonfigurujte konektory pro odchozí proxy server používat pro přístup k Azure AD Application Proxy.

Další informace o tom, jak fungují konektory najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Odchozí proxy jednorázové přihlášení

Konektory mají základní komponenty operačního systému, které umožňují odchozí požadavky. Tyto součásti se automaticky pokusí vyhledat proxy server v síti pomocí Proxy Auto-Discovery WPAD (Web).

Součásti operačního systému došlo k pokusu o nalezení provádí vyhledávání DNS pro wpad.domainsuffix proxy server. Pokud vyhledávání ve službě DNS, IP adresu pro wpad.dat je potom k požadavku HTTP. Tento požadavek se změní na skript konfigurace proxy serveru ve vašem prostředí. Konektor používá tento skript k výběru odchozího proxy serveru. Ale konektor provoz nemusí stále procházejí, z důvodu další nastavení konfigurace na proxy serveru.

Můžete nakonfigurovat konektor obejít vaše místní proxy a zajistit, aby používal přímé připojení ke službám Azure. Tento přístup, doporučujeme tak dlouho, dokud zásady sítě umožňuje, protože znamená, že máte jeden menší konfiguraci udržovat.

Zakázat používání odchozího proxy serveru pro konektor, upravte soubor C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config a přidejte *system.net* část uvedenou v této ukázce kódu:

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
Pokud chcete mít jistotu, že service Connector Updater také obchází proxy serveru, proveďte podobné změny ApplicationProxyConnectorUpdaterService.exe.config souboru. Tento soubor se nachází v C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Nezapomeňte si vytvořte kopie původního souborů v případě, že budete potřebovat obnovit v souborech .config výchozí.

## <a name="use-the-outbound-proxy-server"></a>Odchozí proxy server používat

Některá prostředí vyžadují veškerý odchozí provoz a absolvovat odchozího proxy serveru, bez výjimky. V důsledku toho obcházení proxy serveru není možné.

Konektor provoz a absolvovat odchozího proxy serveru, můžete nakonfigurovat, jak je znázorněno v následujícím diagramu:

 ![Konfigurace konektoru provoz absolvovat odchozího proxy serveru do Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

V důsledku s pouze odchozí provoz, není potřeba konfigurovat příchozí přístup přes brány firewall.

>[!NOTE]
>Proxy aplikací nepodporuje ověřování na jiné servery proxy. Účty služeb konektoru/updater sítě by měl moct připojit k proxy serveru, aniž by se samy pro ověřování.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Nakonfigurujte konektor a související služby a absolvovat odchozího proxy serveru

Pokud WPAD je povoleno v prostředí a správně nakonfigurována, konektor automaticky zjistí odchozí proxy server a pokusíte se ji použít. Můžete však explicitně nakonfigurovat konektoru a absolvovat odchozího proxy serveru.

Uděláte to tak, upravte soubor C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config a přidejte *system.net* část uvedenou v této ukázce kódu. Změna *proxyserver:8080* tak, aby odrážely název vašeho místního proxy serveru nebo IP adresu a port, který naslouchá. Hodnota musí mít předponu http://, i když používáte IP adresu.

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

V dalším kroku nakonfigurujte službu Connector Updater používal proxy server tak, že podobné změny do souboru C:\Program Files\Microsoft AAD App proxy serveru konektoru Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Konfigurace proxy serveru umožňující provoz z konektor a související služby k toku

Existují čtyři aspekty ke zvážení v odchozího proxy serveru:
* Odchozí pravidla proxy
* Ověřování proxy serveru
* Porty serveru proxy
* Kontrola protokolu SSL

#### <a name="proxy-outbound-rules"></a>Odchozí pravidla proxy
Povolit přístup k následujícím adresám URL:

| zprostředkovatele identity | Jak se používá |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikace mezi konektoru a cloudovou službou Proxy aplikací |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Tyto adresy URL Azure používá k ověření certifikátů |
| login.windows.net<br>login.microsoftonline.com | Konektor používá tyto adresy URL během procesu registrace. |

Pokud vaše brána firewall nebo proxy server umožňuje přidávání na seznam povolených DNS, můžete seznam povolených připojení k \*. msappproxy.net a \*. servicebus.windows.net. Pokud ne, je potřeba povolit přístup k [rozsahy IP adres Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653). Rozsahy IP adres se aktualizují každý týden.


Pokud nemůžete povolit připojení pomocí plně kvalifikovaného názvu domény a je nutné místo toho zadat rozsahy IP adres, použijte tyto možnosti:

* Povolte konektor odchozí přístup k všechny cíle.
* Povolit konektor odchozí přístup ke všem [rozsahy IP adres datacentra Azure](https://www.microsoft.com//download/details.aspx?id=41653). Před obrovskou výzvou – s použitím seznamu rozsahů IP adres datacentra Azure je, že se každý týden aktualizuje. Je potřeba umístit proces určitým postupem zajistit, že jsou vaše pravidla přístupu k příslušným způsobem aktualizuje. Pouze pomocí některé podsady z IP adres může způsobit vaše konfigurace pro přerušení.

#### <a name="proxy-authentication"></a>Ověřování proxy serveru

Ověřování proxy serveru se momentálně nepodporuje. Aktuální doporučujeme povolit konektor anonymní přístup k internetové cíle.

#### <a name="proxy-ports"></a>Porty serveru proxy

Konektor umožňuje odchozí připojení SSL pomocí metody CONNECT. Tato metoda je v podstatě nastaví tunelové propojení prostřednictvím odchozího proxy serveru. Konfigurace proxy serveru tak, aby tunelové propojení na porty 443 a 80.

>[!NOTE]
>Při spuštění služby Service Bus přes protokol HTTPS používá port 443. Ve výchozím nastavení, ale služby Service Bus pokusí přímého připojení TCP a spadne zpět na HTTPS pouze v případě, že přímé připojení se nezdaří.

#### <a name="ssl-inspection"></a>Kontrola protokolu SSL
Nepoužívejte kontrole SSL pro provoz konektoru, protože to způsobuje problémy pro provoz konektoru. Konektor používá certifikát k ověření Proxy aplikace služby a tento certifikát může dojít ke ztrátě během kontroly protokolu SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Řešení potíží s problémy proxy serveru konektoru a problémy s připojením služby
Teď byste měli vidět veškerý provoz přes proxy server. Pokud máte problémy, by měly pomoci následující informace o odstraňování potíží.

Nejlepší způsob, jak identifikovat a řešit problémy s připojením konektoru je provést zachytávání sítě při spouštění služby konektoru. Tady jsou některé rychlé tipy pro zachytávání a filtrování trasování sítě.

Můžete použít monitorování nástroje podle vašeho výběru. Pro účely tohoto článku jsme použili Microsoft Message Analyzer. Je možné [si ho stáhnout z Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Následující příklady jsou specifické pro Message Analyzer, ale zásady můžete použít u libovolného nástroje pro analýzu.

### <a name="take-a-capture-of-connector-traffic"></a>Využijte zachytávání provozu konektoru

Počáteční s řešením problémů, proveďte následující kroky:

1. V services.msc zastavte službu Azure AD Application Proxy Connector.

   ![Služba Azure AD Application Proxy Connector v konzole services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Message Analyzer spusťte jako správce.
3. Vyberte **spustit místní trasování**.

   ![Spustit zachytávání sítě](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Spusťte službu Azure AD Application Proxy Connector.
4. Zastavte zachytávání sítě.

   ![Zastavit zachytávání sítě](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Zaškrtněte, pokud konektor provoz obchází odchozí proxy servery

Pokud jste nakonfigurovali váš konektor Proxy aplikací obejít proxy servery a připojte se přímo ke službě Proxy aplikací, který chcete zobrazit v zachytávání sítě pro neúspěšné pokusy o připojení TCP. 

Pomocí filtru Message Analyzer k identifikaci těchto pokusů. Zadejte `property.TCPSynRetransmit` pole Filtr a vyberte **použít**. 

SYN paket, který je první paket odeslaný k navázání připojení TCP. Pokud tento paket nevrací odpověď, je SYN opakování. Předchozí filtru můžete použít zobrazíte všechny požadavky SYN opakovaně. Potom můžete zkontrolovat, zda tyto požadavky SYN odpovídají veškerý provoz související s konektoru.

Pokud očekáváte, že konektor navázat přímé připojení ke službám Azure, SynRetransmit odpovědi na portu 443 jsou jako ukazatel toho, že máte problém sítě nebo brány firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Zaškrtněte, pokud provoz konektor používá odchozí proxy servery

Pokud jste nakonfigurovali provozu konektor Proxy aplikací na proxy servery, budete chtít najít připojení se nezdařilo prostřednictvím protokolu https pro váš proxy server. 

Chcete-li filtrovat v zachytávání sítě pro tyto pokusy o připojení, zadejte `(https.Request or https.Response) and tcp.port==8080` ve filtru Message Analyzer nahradíte 8080 služby port proxy serveru. Vyberte **použít** zobrazíte výsledky filtru. 

Předchozí filtr zobrazí pouze HTTPs požadavky a odpovědi do a z portu proxy serveru. Hledáte připojit požadavků, které ukazují komunikace s proxy serverem. Po úspěšném nasazení získáte odpověď HTTP OK (200).

Pokud se zobrazí další kódy odpovědí, jako je například 407 nebo 502, to znamená, že se proxy server vyžaduje ověření nebo nepovoluje přenos z nějakého důvodu. V tomto okamžiku zapojení týmu podpory proxy serveru.

## <a name="next-steps"></a>Další postup

- [Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)

- Pokud máte problémy s problémy s připojením konektor, položte svou otázku v [fórum pro Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) nebo vytvoříte lístek náš tým podpory.
