---
title: "Práce s existujícími místními proxy servery a Azure AD | Microsoft Docs"
description: "Popisuje, jak pracovat s existující místní proxy servery."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 85b45a828dbb06325b6a1c9453117849f48a27c8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Práce s existující místní proxy servery

Tento článek vysvětluje, jak konfigurovat konektory Proxy aplikace služby Azure Active Directory (Azure AD) pro práci s odchozí proxy servery. Je určený pro zákazníky s sítě prostředích, která mají existující proxy.

Můžeme začít hledáním v těchto scénářích hlavní nasazení:
* Nakonfigurujte konektory pro vynechat vaše místní odchozí proxy.
* Nakonfigurujte konektory používat odchozího proxy serveru pro přístup k Azure AD Application Proxy.

Další informace o fungování konektory najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-understand-connectors.md).

## <a name="bypass-outbound-proxies"></a>Odchozí proxy jednorázové přihlášení

Konektory mít základní součásti operačního systému, které odchozí požadavky. Tyto součásti se automaticky pokusí vyhledat proxy server v síti pomocí Proxy Auto-Discovery WPAD (Web).

Součásti operačního systému se pokusí vyhledat proxy server pomocí vyhledávání DNS pro wpad.domainsuffix. Pokud vyhledávání řeší ve službě DNS, je IP adresa pro wpad.dat potom k požadavku HTTP. Tento požadavek se změní na skript konfigurace proxy serveru ve vašem prostředí. Konektor používá tento skript k vyberte odchozí proxy server. Ale provoz konektor nemusí stále projít, z důvodu nastavení konfigurace na proxy serveru.

Můžete nakonfigurovat konektor Nepoužívat proxy místní zajistit, že používá přímé připojení ke službám Azure. Doporučujeme tuto metodu, dokud zásady sítě umožňuje, protože znamená, že máte jeden menší konfiguraci k údržbě.

Zakázat používání odchozího proxy serveru pro konektor, upravte soubor C:\Program Files\Microsoft AAD aplikace Proxy Connector\ApplicationProxyConnectorService.exe.config a přidat *system.net* části uvedené v této ukázce kódu:

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
Zajistit, že službu Connector Updater také obchází proxy server, změňte podobné ApplicationProxyConnectorUpdaterService.exe.config souboru. Tento soubor se nachází v C:\Program Files\Microsoft AAD aplikace Proxy Connector Updater.

Ujistěte se, aby byly kopie aplikace původních souborů, v případě, že potřebujete obnovit výchozí soubory .config.

## <a name="use-the-outbound-proxy-server"></a>Použít odchozí proxy server

Některé prostředí vyžaduje všechny odchozí přenosy projít odchozího proxy serveru, bez výjimky. V důsledku toho obcházení proxy serveru není možné.

Konektor provoz projít odchozího proxy serveru, můžete nakonfigurovat, jak je znázorněno v následujícím diagramu:

 ![Konfigurace konektoru provoz projít odchozího proxy serveru, na proxy aplikace služby Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

V důsledku s pouze odchozí přenosy, není nutné konfigurovat příchozí přístup přes vaší brány firewall.

>[!NOTE]
>Proxy aplikací nepodporuje ověřování na jiné servery proxy. Účty služby konektoru/updater sítě by mohli připojit k proxy serveru bez požádána o ověřování.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurace konektoru a související služby projít odchozího proxy serveru

Pokud WPAD je povolena v prostředí a správně nakonfigurován, konektor automaticky vyhledá odchozí proxy serveru a pokus o ho použít. Můžete však explicitně nakonfigurovat konektor projít odchozího proxy serveru.

Uděláte to tak, upravte soubor C:\Program Files\Microsoft AAD aplikace Proxy Connector\ApplicationProxyConnectorService.exe.config a přidat *system.net* části uvedené v této ukázce kódu. Změna *proxyserver:8080* tak, aby odrážela název místní proxy serveru nebo IP adresu a port, který naslouchá na.

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

V dalším kroku nakonfigurujte službu Connector Updater na používání proxy serveru tak, že podobné změny do souboru C:\Program Files\Microsoft AAD aplikace proxy serveru konektoru Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Konfigurace proxy serveru, který chcete povolit přenosy z konektoru a související služby k procházet skrz

Existují čtyři aspekty, které je třeba zvážit v odchozího proxy serveru:
* Odchozí pravidla proxy
* Ověřování proxy serverem
* Porty proxy
* Kontrolu SSL

#### <a name="proxy-outbound-rules"></a>Odchozí pravidla proxy
Povolit přístup k vytvoření následujících koncových bodů pro přístup k službě konektor:

* *.msappproxy.net
* *.servicebus.windows.net

Pro počáteční registraci povolte přístup k vytvoření následujících koncových bodů:

* login.windows.net
* login.microsoftonline.com

Pokud nemůžete povolit připojení ve plně kvalifikovaný název domény a je nutné místo toho zadat rozsahy IP adres, použijte tyto možnosti:

* Povolí odchozí přístup konektoru na všechna místa určení.
* Povolit konektor odchozí přístup ke všem [rozsahy IP adres Azure datacenter](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Problém s použitím seznamu rozsahů IP adres Azure datacenter je, že je aktualizovaný týdně. Musíte uvést proces k zajištění, že jsou vaše pravidla přístupu k příslušným způsobem aktualizuje. Část IP adresy jenom pomocí může způsobit vaše konfigurace rozdělit.

#### <a name="proxy-authentication"></a>Ověřování proxy serverem

Ověřování proxy není aktuálně podporován. Naše doporučení aktuální je umožnit konektor anonymní přístup k Internetu cíle.

#### <a name="proxy-ports"></a>Porty proxy

Konektor umožňuje odchozí připojení založené na protokolu SSL pomocí metody připojení. Tato metoda je v podstatě nastavuje tunelové propojení prostřednictvím odchozího proxy serveru. Konfigurace proxy serveru tak, aby tunelového propojení pro porty 443 a 80.

>[!NOTE]
>Spuštění služby Service Bus přes protokol HTTPS používá port 443. Ve výchozím nastavení, ale Service Bus pokusí přímé připojení TCP a spadne zpět na HTTPS pouze v případě, že přímé připojení se nezdaří.

#### <a name="ssl-inspection"></a>Kontrolu SSL
Pro provoz konektor, nepoužívejte kontrolu SSL, protože způsobuje problémy týkající se provozu konektor. Konektor používá certifikát k ověření Proxy aplikace služby a tento certifikát může dojít ke ztrátě během kontroly protokolu SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Řešení potíží s konektor proxy problémy a potíže s připojením služby
Nyní byste měli vidět veškerý provoz prostřednictvím proxy serveru. Pokud máte potíže, by měly pomoci následující informace o odstraňování potíží.

Nejlepší způsob, jak identifikovat a řešit problémy s připojením k konektor je převést zachycení dat ze sítě při spouštění služby konektoru. Tady jsou některé rychlé tipy pro zachytávání a filtrování trasování sítě.

Můžete použít nástroj monitorování podle svého výběru. Pro účely tohoto článku použili jsme Microsoft Message Analyzer. Můžete [ji stáhnout z Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Následující příklady jsou specifické pro Message Analyzer, ale zásady lze použít pro nástroj pro analýzu.

### <a name="take-a-capture-of-connector-traffic"></a>Proveďte zachycení provozu konektoru

Pro počáteční řešení potíží, proveďte následující kroky:

1. Ze souboru services.msc zastavte službu konektoru Proxy aplikace služby Azure AD.

   ![Služba Azure AD konektor Proxy aplikace v services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. Message Analyzer spusťte jako správce.
3. Vyberte **spustit místní trasování**.

   ![Spustit zachycení dat ze sítě](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. Spusťte službu konektoru Proxy aplikace služby Azure AD.
4. Zastavte zachycení dat ze sítě.

   ![Zastavit zachycení dat ze sítě](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Zkontrolujte, pokud konektor provoz obchází odchozí proxy

Pokud jste nakonfigurovali vaší konektor Proxy aplikace na obcházení proxy serverů a připojte se přímo ke službě Proxy aplikace, který chcete zobrazit v zachycení sítě pro neúspěšné pokusy o připojení protokolu TCP. 

Filtr Message Analyzer použijte k identifikaci tyto pokusy. Zadejte `property.TCPSynRetransmit` v pole filtru a vyberte **použít**. 

Paket SYN je první paket odeslaný k navázání připojení TCP. Pokud tomuto paketu nevrací odpověď, je reattempted SYN. Předchozí filtrem můžete zobrazit všechny požadavky SYN opakovaně. Potom můžete zkontrolovat, zda tyto požadavky SYN odpovídají přenosy dat souvisejících s konektoru.

Pokud očekáváte, konektor navázat přímé připojení ke službám Azure, SynRetransmit odpovědi na portu 443 jsou to znamenat, že máte síť nebo brána firewall problému.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Zkontrolujte, jestli provoz konektor používá odchozí proxy

Pokud jste nakonfigurovali provozu konektoru Proxy aplikace projít, proxy servery, který chcete zobrazit pro protokol https se nezdařilo připojení k proxy. 

Chcete-li filtrovat zachycení dat ze sítě pro tyto pokusy o připojení, zadejte `(https.Request or https.Response) and tcp.port==8080` ve filtru Message Analyzer, nahraďte 8080 vaší služby port proxy serveru. Vyberte **použít** zobrazíte výsledky filtru. 

Předchozí filtr zobrazuje pouze požadavky HTTPs a odpovědi z port proxy serveru. Hledáte CONNECT požadavků, které ukazují komunikace se serverem proxy. Po úspěšné zobrazí se odpověď HTTP OK (200).

Pokud se zobrazí další kódy odpovědí, jako je například 407 nebo 502, která znamená, že proxy server je vyžadování ověřování nebo neumožňuje provoz z jiného důvodu. V tomto okamžiku zaujmout váš tým podpory proxy serveru.

## <a name="next-steps"></a>Další postup

- [Pochopení konektory proxy aplikace služby Azure AD](application-proxy-understand-connectors.md)

- Pokud máte problémy s problémy s připojením k konektor, položte svou otázku v [fórum Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) nebo vytvořit lístek s náš tým podpory.
