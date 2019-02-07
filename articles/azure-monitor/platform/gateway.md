---
title: Připojení počítačů s využitím Log Analytics gateway | Dokumentace Microsoftu
description: Připojení vašich zařízení a počítače monitorované Operations Manageru s Log Analytics brána k odesílání dat do služby Azure Automation a služba Log Analytics, když nemají přístup k Internetu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: e1c225eb77a76015520690916db0399487ffe9e7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822608"
---
# <a name="connect-computers-without-internet-access-using-the-log-analytics-gateway"></a>Připojit počítače bez připojení k Internetu pomocí brány Log Analytics

>[!NOTE]
>Brána OMS se jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru, uvedené jako brána Log Analytics. 
>

Tento dokument popisuje, jak nakonfigurovat komunikaci s Azure Automation a Log Analytics pomocí Log Analytics gateway při přímé připojení nebo Operations Manager monitoruje počítače nemají přístup k Internetu.  Bránu Log Analytics, která je dopředné proxy server HTTP, který podporuje tunelování pomocí příkazu HTTP připojení HTTP, můžete shromažďovat data a odeslat do služby Azure Automation a Log Analytics jejich jménem.  

Log Analytics gateway podporuje:

* Vytváření sestav až čtyři stejné Log Analytics pracovní prostory agenti jeho jsou nakonfigurováni pomocí  
* Azure Automation Hybrid Runbook Worker  
* Počítače s Windows pomocí agenta Microsoft Monitoring Agent přímo připojené k pracovnímu prostoru Log Analytics
* Počítačů s Linuxem pomocí agenta Log Analytics pro Linux přímo připojený k pracovnímu prostoru Log Analytics  
* System Center Operations Manager 2012 SP1 s UR7, Operations Manager 2012 R2 UR3, Operations Manager 2016 nebo novější skupiny pro správu integrované se službou Log Analytics.  

Pokud zásady zabezpečení IT neumožňují počítačů ve vaší síti pro připojení k Internetu, jako je například bod terminálů zařízení nebo serverů podporující IT služby, ale budete muset připojit je ke službě Azure Automation nebo Log Analytics, spravovat a monitorovat jejich , se dají konfigurovat přímo komunikovat s Log Analytics brány na konfigurace a předávání dat jejich jménem.  Pokud tyto počítače jsou nakonfigurované pomocí agenta Log Analytics k přímému připojení k pracovnímu prostoru Log Analytics, všechny počítače se místo toho komunikovat s Log Analytics gateway.  Brána přenáší data z agentů do služby přímo, nebudou analyzované žádné přenášená data.

Když skupinu pro správu Operations Manageru je integrovaný s Log Analytics, dá se servery pro správu pro připojení k bráně Log Analytics pro příjem informací o konfiguraci a posílání shromážděných dat v závislosti na řešení, které jste povolili.  Agenti nástroje Operations Manager některá data, jako jsou výstrahy nástroje Operations Manager, posouzení konfigurace, prostoru instancí a data o kapacitě odeslat na server pro správu. Další velkého objemu dat, jako jsou protokoly služby IIS, výkonu a události zabezpečení odesílány přímo ke službě Log Analytics gateway.  Pokud máte nasazený v hraniční sítě nebo jiné izolované sítě pro monitorování systémů nedůvěryhodné nejméně jeden server brány Operations Manager nemůže komunikovat s Log Analytics gateway.  Servery nástroje Operations Manager brány může jenom nahlásit to k serveru pro správu.  Když skupinu pro správu Operations Manageru je nakonfigurován pro komunikaci s bránou Log Analytics, informace o konfiguraci proxy serveru je automaticky distribuován do každý počítač spravovaný agentem, který je nakonfigurovaný ke shromažďování dat i pro Log Analytics Pokud nastavení je prázdné.    

Pro zajištění vysoké dostupnosti pro přímé připojení nebo operace skupinami pro správu komunikovat s Log Analytics prostřednictvím brány, můžete použít vyrovnávání zatížení sítě k přesměrování a distribuuje provoz mezi více serverů brány.  Pokud jeden server brány ocitne mimo provoz, provoz přesměruje do jiného uzlu k dispozici.  

Agenta Log Analytics Windows se vyžaduje na počítači se systémem brány Log Analytics v pořadí a nejenom identifikovat, které jsou potřebné ke komunikaci s koncovými body služby, ale také sestavu do stejného pracovní prostory, které agentů nebo Operations Manager Skupina pro správu za bránou se nakonfigurují se. To je nezbytné pro bránu, aby se mohly komunikovat s jejich přiřazené pracovního prostoru. Může být brána s více adresami k až čtyři pracovním prostorům, je to celkový počet pracovních prostorů, které podporuje Windows agenta.  

Každý agent musí mít síťové připojení k bráně, tak, aby agenti automaticky můžou přenášet data do a z něj. Instalace brány na řadiči domény se nedoporučuje.

Následující diagram znázorňuje tok dat z přímí agenti Azure Automation a Log Analytics pomocí serveru brány. Agenty musí mít jejich konfiguraci proxy serveru, které odpovídají stejný port, který má nakonfigurovanou bránu Log Analytics.  

![přímý agent komunikaci s diagram služby](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat ze skupiny pro správu Operations Manageru k Log Analytics.   

![Operations Manager komunikace s diagramem Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Požadavky

Při určování počítač pro spuštění brány Log Analytics, musí splňovat následující:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2,  Windows Server 2008
* Rozhraní .net framework 4.5
* Minimálně 4 jádra procesoru a 8 GB paměti 
* [Agenta log Analytics pro Windows](agent-windows.md) je nainstalovaný a nakonfigurovaný k ukládání dat do stejného pracovního prostoru jako agenti komunikovat prostřednictvím brány.  

### <a name="language-availability"></a>Dostupnost jazyka

Brána Log Analytics je dostupná v následujících jazycích:

- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Holandština
- Angličtina
- Francouzština
- Němčina
- Maďarština
- italština
- Japonština
- Korejština
- polština
- Portugalština (Brazílie)
- Portugalština (Portugalsko)
- ruština
- Španělština (mezinárodní)

### <a name="supported-encryption-protocols"></a>Šifrování podporovaných protokolů
Log Analytics brána podporuje pouze zabezpečení TLS (Transport Layer) 1.0, 1.1 a 1.2.  Nepodporuje vrstvy SSL (Secure Sockets).  – Pomáhat zajistit zabezpečení dat při přenosu do služby Log Analytics, důrazně doporučujeme, abyste ke konfiguraci k bráně pro použití alespoň zabezpečení TLS (Transport Layer) 1.2. Starší verze z protokolu TLS/Secure Sockets Layer (SSL) bylo zjištěno ohrožen a stále aktuálně fungují povolit zpětnou kompatibilitu, ale jsou **ale nedoporučený krok**.  Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Podporovaný počet připojení agenta
V následující tabulce najdete podporovaný počet agentů komunikaci se serverem brány.  Tato podpora je založen na agentech nahrávání přibližně 200 KB dat každých 6 sekund. Objem dat podle agenta testování je přibližně 2.7 GB za den.

|brána |Asi počet agentů podporována|  
|--------|----------------------------------|  
|- CPU: Intel XEON 2660 CPU E5 v3 \@ 2,6 GHz, 2 jádra<br> -Paměti: 4 GB<br> – Šířka pásma sítě: 1 Gb/s| 600|  
|- CPU: Intel XEON 2660 CPU E5 v3 \@ 2,6 GHz, 4 jádra<br> -Paměti: 8 GB<br> – Šířka pásma sítě: 1 Gb/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Stáhněte si bránu Log Analytics

Existují dva způsoby, jak získat nejnovější verzi instalačního souboru brány Log Analytics.

1. Stahování [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

1. Stáhněte z webu Azure portal.  Po přihlášení k webu Azure portal:  

   1. Procházet seznam služeb a pak vyberte **Log Analytics**.  
   1. Vyberte pracovní prostor.
   1. V okně pracovního prostoru v části **Obecné**, klikněte na tlačítko **rychlý Start**.
   1. V části **vyberte zdroj dat pro připojení k pracovnímu prostoru**, klikněte na tlačítko **počítače**.
   1. V **přímý Agent** okna, klikněte na tlačítko **stáhnout Log Analytics gateway**.<br><br> ![Stáhněte si bránu Log Analytics](./media/gateway/download-gateway.png)

nebo 

   1. V okně pracovního prostoru v části **nastavení**, klikněte na tlačítko **upřesňující nastavení**.
   1. Přejděte do **připojené zdroje** > **servery Windows** a klikněte na tlačítko **stáhnout Log Analytics gateway**.

## <a name="install-the-log-analytics-gateway"></a>Instalace brány Log Analytics

Pokud chcete nainstalovat bránu, postupujte následovně.  Pokud jste nainstalovali předchozí verzi, dříve se označovaly jako *předávání Log Analytics*, se upgraduje na tuto verzi.  

1. Z cílové složky, dvakrát klikněte na panel **Log Analytics gateway.msi**.
1. Na **úvodní** stránce klikněte na **Další**.<br><br> ![Průvodce instalací brány](./media/gateway/gateway-wizard01.png)<br> 
1. Na **licenční smlouvy** stránce **souhlasím s podmínkami licenční smlouvy** svůj souhlas se smlouvou EULA a potom klikněte na **Další**.
1. Na **portu a proxy adresy** stránky:
   1. Zadejte číslo portu TCP pro použití brány. Instalační program nakonfiguruje příchozí pravidlo s tímto číslem portu v bráně Windows firewall.  Výchozí hodnota je 8080.
      Platný rozsah číslo portu je 1-65535. Pokud vstup do tohoto rozsahu nespadá, zobrazí se chybová zpráva.
   1. Pokud server, kde je nainstalovaná brána potřebuje komunikovat prostřednictvím proxy serveru, volitelně zadejte adresu proxy serveru, kde je potřeba brána připojení. Například, `http://myorgname.corp.contoso.com:80`.  Pokud je pole prázdné, brány se pokusí připojit přímo k Internetu.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo.<br><br> ![Konfigurace proxy serveru brány Průvodce](./media/gateway/gateway-wizard02.png)<br>   
   1. Klikněte na **Další**.
1. Pokud nemáte povolenu službu Microsoft Update, zobrazí se stránka Microsoft Update, kde můžete vybrat, aby je. Proveďte výběr a potom klikněte na tlačítko **Další**. V opačném případě pokračujte k dalšímu kroku.
1. Na **cílovou složku** stránky, ponechte výchozí složky C:\Program Files\OMS brány nebo zadejte umístění, kam chcete nainstalovat bránu a potom klikněte na tlačítko **Další**.
1. Na **připraveno k instalaci** klikněte na **nainstalovat**. Řízení uživatelských účtů se může zobrazit žádost o oprávnění k instalaci. Pokud ano, klikněte na tlačítko **Ano**.
1. Po dokončení instalace klikněte na tlačítko **Dokončit**. Můžete ověřit, že je služba spuštěná tak, že otevřete services.msc a ověřte, že **bránu OMS** se zobrazí v seznamu služeb a jeho stav je **systémem**.<br><br> ![Služby – Brána Log Analytics](./media/gateway/gateway-service.png)  


## <a name="configure-network-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení sítě 
Můžete nakonfigurovat bránu pro zajištění vysoké dostupnosti pomocí služby Vyrovnávání zatížení sítě (NLB) buď Microsoft sítě vyrovnávání zatížení (NLB) nebo nástroje pro vyrovnávání zatížení na základě hardwaru.  Nástroje pro vyrovnávání zatížení spravuje provozu přesměrování mezi jeho uzly požadované připojení z agentů Log Analytics nebo serverů pro správu Operations Manageru. Pokud jeden server brány ocitne mimo provoz, provoz přesměrován do dalších uzlů.

Zjistěte, jak navrhnout a nasadit cluster programu pro vyrovnávání zatížení sítě systému Windows Server 2016, najdete v článku [Vyrovnávání zatížení sítě](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Následující kroky popisují, jak konfigurovat cluster vyrovnávání zatížení sítě společnosti Microsoft.  

1. Přihlaste do Windows serveru, který je členem clusteru programu NLB s účtem správce.  
1. Ve Správci serveru otevřete Správce vyrovnávání zatížení sítě, klikněte na tlačítko **nástroje**a potom klikněte na tlačítko **Správce vyrovnávání zatížení sítě**.
1. Pro připojení k serveru brány Log Analytics pomocí Microsoft Monitoring Agent nainstalován, klikněte pravým tlačítkem na IP adresu clusteru a potom klikněte na tlačítko **přidat hostitele do clusteru**.<br><br> ![Zatížení vyrovnávání správce – přidat hostitele do clusteru](./media/gateway/nlb02.png)<br> 
1. Zadejte IP adresu serveru brány, kterou chcete připojit.<br><br> ![Zatížení vyrovnávání správce – přidat hostitele do clusteru: Připojení](./media/gateway/nlb03.png) 
    
## <a name="configure-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurace agenta Log Analytics a skupinu pro správu Operations Manageru
Následující část obsahuje pokyny ke konfiguraci přímo připojených agentů Log Analytics, skupiny pro správu Operations Manageru nebo Azure Automation Hybrid Runbook Worker ve službě Log Analytics gateway ke komunikaci s Azure Automation nebo protokolu Analytics.  

### <a name="configure-standalone-log-analytics-agent"></a>Konfigurace samostatného agenta Log Analytics
Požadavky a pokyny k instalaci agenta Log Analytics na bránu a v počítačích Windows přímého připojení k Log Analytics najdete v tématu [počítače Windows se připojit ke službě Log Analytics](agent-windows.md) nebo počítačů pro Linux najdete v článku [ Připojení počítačů s Linuxem k Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md). Místo zadání proxy serveru při konfiguraci agenta, nahraďte tuto hodnotu IP adresu serveru brány Log Analytics a jeho číslo portu. Pokud jste nasadili více serverů brány za nástroj pro vyrovnávání zatížení sítě, je konfigurace proxy serveru agenta Log Analytics virtuální IP adresy služby NLB.  

Po instalaci agenta na server brány, můžete nakonfigurovat na hlásit do pracovního prostoru nebo pracovním prostorům agentů s bránou. Pokud agenta Log Analytics Windows není nainstalována na bráně, k se zapíše událost 300 **protokol brány OMS** protokolu událostí udávající agenta musí být nainstalovaný. Pokud je agent nainstalován, ale není nakonfigurovaný k ukládání dat do stejného pracovního prostoru jako agenti komunikaci přes něj, se zapíše událost 105 do stejného protokolu událostí, s informacemi o tom, že agent na bráně je potřeba nakonfigurovat k ukládání dat do stejného pracovního prostoru jako agenti s t he brány.

Po dokončení konfigurace budete muset restartovat **bránu OMS** služby, aby se změny projevily. V opačném případě brány odmítnou pokouší komunikovat s Log Analytics a sestav událost ID 105 v agentů **protokol brány OMS** protokolu událostí. To platí i při přidání nebo odebrání pracovního prostoru z konfigurace agenta na server brány.   

Informace týkající se služby Automation Hybrid Runbook Worker, naleznete v tématu [nasazení funkce Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurace nástroje Operations Manager – všechny agenty používat stejný server proxy
Chcete-li přidat server brány nástroje Operations Manager nakonfigurujete.  Konfigurace proxy serveru nástroje Operations Manager se automaticky využije na všech agentů odesílajících sestavy do nástroje Operations Manager, i když je toto nastavení prázdné.  

Použití brány pro podporu nástroje Operations Manager, musíte mít:

* Microsoft Monitoring Agent (verze agenta – **8.0.10900.0** nebo novější) na serveru služby Brána nainstalovaná a nakonfigurovaná s stejný pracovní prostory Log Analytics, že vaši skupinu pro správu je nakonfigurovaný k ukládání dat do.
* Brány musí mít připojení k Internetu nebo připojení k proxy serveru, která provádí.

> [!NOTE]
> Pokud nezadáte hodnotu pro bránu, prázdné hodnoty se nasdílejí do všech agentů.
> 

Pokud je prvním s pracovním prostorem Log Analytics je registrace skupiny pro správu nástroje Operations Manager, můžete zadat konfiguraci proxy serveru pro skupinu pro správu není k dispozici v konzoli Operations console.  Tato možnost bude dostupná až potom, co bude skupina pro správu ve službě úspěšně zaregistrovaná.  Vaše spuštění konzoly Operations console z ke konfiguraci integrace a všechny servery pro správu ve skupině pro správu aktualizujte konfiguraci proxy serveru systému pomocí nástroje Netsh v systému.  

1. Otevřete příkazový řádek se zvýšenými oprávněními.

    a. Přejděte na **Start** a typ **cmd**.  
    b. Klikněte pravým tlačítkem na **příkazového řádku** a vyberte **spustit jako správce**.  

1. Zadejte následující příkaz a stiskněte **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s Log Analytics, můžete odebrat spuštěním změnu `netsh winhttp reset proxy` a pak použít **konfigurovat proxy server** možnost v konzoli Operations console k určení serveru brány Log Analytics. 

1. Otevřete konzolu nástroje Operations Manager a v části **Operations Management Suite**, klikněte na tlačítko **připojení** a potom klikněte na tlačítko **konfigurovat Proxy Server**.<br><br> ![Nástroj Operations Manager – nakonfigurujte Proxy Server](./media/gateway/scom01.png)<br> 
1. Vyberte **použít proxy server pro přístup k Operations Management Suite** a zadejte IP adresu serveru brány pro Log Analytics nebo virtuální IP adresy Vyrovnávání zatížení sítě. Ujistěte se, že začínáte s `http://` předponu.<br><br> ![Nástroj Operations Manager – adresa proxy serveru](./media/gateway/scom02.png)<br> 
1. Klikněte na **Dokončit**. Vaši skupinu pro správu Operations Manageru je nyní nakonfigurováno pro komunikaci prostřednictvím serveru brány ke službě Log Analytics.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurace nástroje Operations Manager – konkrétní agentů použít proxy server
Pro rozsáhlá nebo složitá prostředí můžete chtít konkrétních serverů (nebo skupiny) používat server brány, Log Analytics.  Pro tyto servery nelze aktualizovat agenta nástroje Operations Manager přímo, protože tato hodnota je přepsána globální hodnoty pro skupinu pro správu.  Místo toho musíte přepsat pravidlo použít tyto hodnoty.  

> [!NOTE] 
> Tento stejný postup konfigurace slouží k povolení použití více serverů brány Log Analytics ve vašem prostředí.  Může například vyžadovat konkrétní servery brány Log Analytics určit na základě jednotlivých oblastech.
>  

1. Otevřete konzolu nástroje Operations Manager a vyberte **Authoring** pracovního prostoru.  
1. V pracovním prostoru vytváření obsahu, vyberte **pravidla** a klikněte na tlačítko **oboru** tlačítko na panelu nástrojů Operations Manager. Pokud toto tlačítko není k dispozici, zkontrolujte, že máte objektu, nikoli složku, v podokně monitorování vybrán. **Obor objektů sady Management Pack** dialogové okno zobrazí seznam běžných cílové třídy, skupiny nebo objekty. 
1. Typ **služba Health Service** v **vyhledejte** pole a vyberte ho ze seznamu.  Klikněte na **OK**.  
1. Vyhledejte pravidlo **pravidla nastavení proxy serveru služby Advisor** a v panelu nástrojů konzoly Operations console, klikněte na tlačítko **přepíše** a přejděte na **přepsat Rule\For konkrétní objekt třídy: Služba Health Service** a vybrat konkrétní objekt ze seznamu.  Volitelně můžete vytvořit vlastní skupiny obsahující objekt služba stavu serverů, které chcete použít toto přepsání na a pak použít přepsání do této skupiny.
1. V **potlačit vlastnosti** dialogové okno, kliknutím umístěte značku zaškrtnutí v **přepsat** vedle sloupce **WebProxyAddress** parametru.  V **hodnota přepsání** pole, zadejte adresu URL serveru brány Log Analytics zajistit začínat `http://` předponu.  

    >[!NOTE]
    > Není potřeba povolit pravidlo, protože to je již spravován automaticky pomocí přepsání obsažené v sadě management pack Microsoft System Center Advisor zabezpečení odkaz přepsat cílení na Microsoft System Center Advisor monitorování skupiny serverů.
    >   

1. Vyberte sadu management pack z **vyberte cílovou sadu management pack** seznamu nebo vytvořte nový nezapečetěný management pack kliknutím **nový**. 
1. Když změny dokončíte, klikněte na tlačítko **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurace pro automation hybrid Worker
Pokud máte služby Automation Hybrid Runbook Worker ve vašem prostředí, následující kroky obsahují ruční, dočasných řešení v oblasti konfigurace brány pro jejich podporu.

V následujících krocích je potřeba vědět oblast Azure, ve které se nachází na účtu Automation. Vyhledejte umístění:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte službu Azure Automation.
1. Vyberte příslušný účet Azure Automation.
1. Zobrazit jeho oblast v rámci **umístění**.<br><br> ![Azure portal – umístění účtu Automation](./media/gateway/location.png)  

Pomocí následující tabulky Identifikujte adresu URL pro každé umístění:

**Adresy URL služby úlohu runtime dat.**

| **location** | **Adresa URL** |
| --- | --- |
| Středoseverní USA |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net |
| Středojižní USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net |
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japonsko |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrálie |ase-jobruntimedata-prod-su1.azure-automation.net |

**Adresy URL služby agenta**

| **location** | **Adresa URL** |
| --- | --- |
| Středoseverní USA |ncus-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-agentservice-prod-1.azure-automation.net |
| Středojižní USA |scus-agentservice-prod-1.azure-automation.net |
| Východní USA 2 |eus2-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-agentservice-prod-1.azure-automation.net |
| Střed Indie |cid-agentservice-prod-1.azure-automation.net |
| Japonsko |jpe-agentservice-prod-1.azure-automation.net |
| Austrálie |ase-agentservice-prod-1.azure-automation.net |

Pokud váš počítač je registrovaný jako Hybrid Runbook Worker automaticky pro použití dílčích oprav pomocí řešení Update Management, postupujte podle těchto kroků:

1. Adresy URL služby dat získaných za běhu úlohy přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics pomocí následující rutiny Powershellu: `Restart-Service OMSGatewayService`

Pokud váš počítač zprovozněný do Azure Automation pomocí rutiny registrace procesu Hybrid Runbook Worker, postupujte podle těchto kroků:

1. Adresa URL pro registraci agenta služby přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adresy URL služby dat získaných za běhu úlohy přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutin prostředí PowerShell
Rutiny vám můžou pomoct dokončit úkoly, které jsou potřeba k aktualizaci nastavení konfigurace Log Analytics gateway. Předtím, než je použijete, nezapomeňte na následující:

1. Instalace brány Log Analytics (MSI).
1. Otevřete okno konzole Powershellu.
1. Importujte modul, zadejte tento příkaz: `Import-Module OMSGateway`
1. Pokud k žádné chybě došlo v předchozím kroku, modul se úspěšně naimportoval a můžou používat rutiny. Typ `Get-Module OMSGateway`
1. Když provedete změny pomocí rutin, zkontrolujte službu brány restartovat.

Modul nebyl importován, pokud dojde k chybě v kroku 3. Po nelze nalézt modul prostředí PowerShell, může dojít k chybě. Najdete ho v cestě instalace brány: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci této služby |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Key (vyžadováno) <br> Hodnota |Změny konfigurace služby |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu proxy serveru (nadřazeného) relay |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo |Nastaví adresu (a přihlašovací údaje) (nadřazeného) proxy relay |1. Nastavte relay proxy a přihlašovacích údajů:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavení předávání přes proxy server, která nepotřebuje ověřování: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Vymažte nastavení proxy serveru relay:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povolené hostitele (pouze místně nakonfigurované povolené hostitele, nezahrnuje povolené hostitelé automaticky stažené) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených aplikací |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených aplikací |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Přidá certifikát klienta může na seznamu povolených |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Odebere ze seznamu povolených aplikací předmětu certifikátu klienta |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené klienta předměty certifikátu (pouze místně nakonfigurované povolené subjekty, nezahrnuje automaticky staženy povolené témata) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží
Shromažďovat události zapsané podle brány, musíte také mít nainstalovaného agenta Log Analytics.<br><br> ![Prohlížeč událostí – brána protokolu Log Analytics](./media/gateway/event-viewer.png)

**ID události brány log Analytics a popisy**

V následující tabulce jsou uvedeny ID událostí a popisy pro Log Analytics gateway události protokolu.

| **ID** | **Popis** |
| --- | --- |
| 400 |Chyba jakékoli aplikace, která nemá žádné specifické ID |
| 401 |Chybná konfigurace. Příklad: listenPort = "text" namísto celého čísla |
| 402 |Výjimka při analýze zprávy metody handshake TLS |
| 403 |Došlo k chybě sítě. Příklad: Nelze se připojit k cílovému serveru |
| 100 |Obecné informace |
| 101 |Služba byla spuštěna |
| 102 |Služba byla zastavena |
| 103 |Přijetí příkazu k připojení HTTP z klienta |
| 104 |Není připojení protokolu HTTP příkaz |
| 105 |Cílový server se nenachází v seznamu povolených nebo cílový port není zabezpečený port (443) <br> <br> Ujistěte se, že agenta MMA na serveru brány a agenty komunikaci s bránou připojeni do stejného pracovního prostoru Log Analytics. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN = brány <br><br> Ujistěte se, že: <br>    <br> &#149;Při použití brány se číslo verze 1.0.395.0 nebo vyšší. <br> &#149;Agenta MMA na serveru brány a agenty komunikaci s bránou jsou připojené do stejného pracovního prostoru Log Analytics. |
| 106 |Brána Log Analytics podporuje jenom TLS 1.0, TLS 1.1 a 1.2.  Nepodporuje SSL. Log Analytics gateway pro všechny nepodporované verze protokolu TLS/SSL, vygeneruje událost ID 106.|
| 107 |Relace TLS byla ověřena. |

**Čítače výkonu ke shromažďování**

V následující tabulce jsou uvedeny čítačů výkonu k dispozici pro bránu Log Analytics. Můžete přidat čítače sledování výkonu.

| **Název** | **Popis** |
| --- | --- |
| Připojení klienta k log Analytics Gateway/aktivní |Počet aktivních klientů připojení protokolu TCP) |
| Počet brány/chyb log Analytics |Počet chyb |
| Log Analytics brány nebo připojení klienta |Počet připojených klientů |
| Počet brány nebo zamítnutí log Analytics |Počet zamítnutí kvůli chybě ověřování TLS |

![Čítače výkonu brány analýzy protokolů](./media/gateway/counters.png)

## <a name="get-assistance"></a>Získat pomoc
Když jste přihlášeni k webu Azure portal, můžete vytvořit žádost o pomoc s brány Log Analytics nebo jiné služby Azure nebo součástí služby.
Požádat o pomoc, klikněte na symbol otazníku v pravém horním rohu portálu a potom klikněte na **nová žádost o podporu**. Dokončete nový formulář žádosti o podporu.

![Nová žádost o podporu](./media/gateway/support.png)

## <a name="next-steps"></a>Další postup
[Přidat zdroje dat](../../azure-monitor/platform/agent-data-sources.md) shromažďovat data z vašeho připojené zdroje a uloží je v pracovním prostoru Log Analytics.
