---
title: Připojení počítačů s použitím brány Log Analytics | Dokumentace Microsoftu
description: Připojení vašich zařízení a počítače monitorované Operations Manageru pomocí Log Analytics brána k odesílání dat do služby Azure Automation a služba Log Analytics, když nemají přístup k Internetu.
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
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: a497662ac7a885b53e69bb8c86a646045bd2eef7
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314666"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway"></a>Připojit počítače bez připojení k Internetu pomocí brány Log Analytics

>[!NOTE]
>Jak Microsoft Operations Management Suite (OMS) přejde do Microsoft Azure Monitor, se mění terminologie. Tento článek odkazuje na bránu OMS jako brána Azure Log Analytics. 
>

Tento článek popisuje, jak nakonfigurovat komunikaci s Azure Automation a Log Analytics pomocí Log Analytics gateway, pokud počítače, které jsou připojeny přímo nebo které jsou monitorovány nástrojem Operations Manager mají bez připojení k Internetu. 

Brána Log Analytics je dopředné proxy server HTTP, který podporuje HTTP tunelování, pomocí příkazu HTTP připojení. Tuto bránu můžete shromažďovat data a odeslat do služby Azure Automation a Log Analytics jménem počítačů, které nejsou připojené k Internetu.  

Log Analytics gateway podporuje:

* Vytváření sestav až čtyři stejné Log Analytics pracovní prostor agenty, které jsou za to, které jsou nakonfigurovány s Azure Automation Hybrid Runbook Worker.  
* Počítače Windows, na kterých Microsoft Monitoring Agent je přímo připojeno k pracovnímu prostoru Log Analytics.
* Linux počítače, na kterých agenta Log Analytics pro Linux je přímo připojeno k pracovnímu prostoru Log Analytics.  
* System Center Operations Manager 2012 SP1 UR7, Operations Manager 2012 R2 s UR3 nebo skupinu pro správu v Operations Manageru 2016 nebo novější, který je integrovaný s Log Analytics.  

Některé zásady zabezpečení IT neumožňují připojení k Internetu pro počítače v síti. Tyto počítače nepřipojené může být bodu terminálů zařízení nebo podporu IT služeb, například serverů. Pro připojení k Azure Automation nebo Log Analytics, správu a monitorování těchto zařízení, konfigurovat, aby mohla komunikovat přímo s bránou Log Analytics. Log Analytics gateway můžou získat informace o konfiguraci a předávání dat jejich jménem. Pokud počítačích nakonfigurovaných pomocí agenta Log Analytics k přímému připojení k pracovnímu prostoru Log Analytics, počítače místo toho komunikovat s Log Analytics gateway.  

Log Analytics gateway přenáší data z agentů do služby přímo. Nelze analyzovat jakákoliv data při přenosu.

Když skupiny pro správu Operations Manageru je integrovaný s Log Analytics, servery pro správu je nakonfigurovat pro připojení k bráně Log Analytics pro příjem informací o konfiguraci a posílání shromážděných dat, v závislosti na řešení, které jste povolili .  Agenti nástroje Operations Manager odeslat data na server pro správu. Například agentů může odesílat výstrahy nástroje Operations Manager, údaje o posouzení konfigurace, data prostoru instancí a data o kapacitě. Další velkého objemu dat, jako jsou protokoly, údaje o výkonu a událostech zabezpečení, Internetové informační služby (IIS) se pošle přímo brány Log Analytics. 

Pokud jeden nebo více serverů brány Operations Manager k monitorování nedůvěryhodného systémů v hraniční síti nebo v izolované síti nasadíte, tyto servery nemůže komunikovat s Log Analytics gateway.  Servery nástroje Operations Manager brány může hlásit pouze k serveru pro správu.  Když skupinu pro správu Operations Manageru je nakonfigurován pro komunikaci s bránou Log Analytics, informace o konfiguraci proxy serveru je automaticky distribuován do každý počítač spravovaný agentem, který je nakonfigurovaný ke shromažďování dat i pro Log Analytics Pokud nastavení je prázdné.    

Pro zajištění vysoké dostupnosti pro přímo připojená nebo skupin pro správu operací, které komunikují se službou Log Analytics prostřednictvím brány, použijte Vyrovnávání zatížení sítě (NLB) k přesměrování a distribuovat provoz napříč několika servery brány. Tímto způsobem, pokud jeden server brány ocitne mimo provoz, provoz přesměruje do jiného uzlu k dispozici.  

Počítač, na kterém běží brána Log Analytics vyžaduje, aby agent Windows Log Analytics k identifikaci koncové body služby, které brána potřebuje ke komunikaci s. Agent také musí směrovat brány na zprávy do stejné pracovních prostorů, které agentů nebo skupiny pro správu nástroje Operations Manager za bránou nakonfigurovanou. Tato konfigurace umožňuje bránu a agenta pro komunikaci s jejich přiřazené pracovního prostoru.

Brána lze s více adresami až čtyři pracovními prostory. Toto je celkový počet pracovních prostorů, které podporuje Windows agenta.  

Každý agent musí mít síťové připojení k bráně, tak, aby agenti automaticky můžou přenášet data do a z brány. Bránu neinstalujte na řadič domény.

Následující diagram znázorňuje dat odesílaných ze přímí agenti, přes bránu, aby službě Azure Automation a Log Analytics. Konfigurace proxy serveru agenta musí shodovat s portem, který má nakonfigurovanou bránu Log Analytics.  

![Diagram přímý agent komunikace se službami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat ze skupiny pro správu Operations Manageru k Log Analytics.   

![Diagram komunikaci Operations Manageru s Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Nastavení systému

Počítače určené ke spuštění Log Analytics brány musí mít následující konfiguraci:

* Windows 10, Windows 8.1 nebo Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 nebo Windows Server 2008
* Microsoft .NET Framework 4.5
* Minimálně 4 jádra procesoru a 8 GB paměti 
* A [agenta Log Analytics pro Windows](agent-windows.md) , který je nakonfigurovaný k ukládání dat do stejného pracovního prostoru jako agenti, které komunikují prostřednictvím brány

### <a name="language-availability"></a>Dostupnost jazyka

Brána Log Analytics je dostupná v těchto jazycích:

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
Log Analytics gateway podporuje pouze zabezpečení TLS (Transport Layer) 1.0, 1.1 a 1.2.  Nepodporuje vrstvy SSL (Secure Sockets).  Zajistit zabezpečení dat při přenosu do služby Log Analytics nakonfigurovat bránu, aby používal minimálně TLS 1.2. Starší verze protokolu TLS nebo SSL jsou ohrožené. I když nechají aktuálně zpětnou kompatibilitu, vyhněte se jejich použití.  

Další informace najdete v tématu [odesílání dat pomocí protokolu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Podporovaný počet připojení agenta
V následující tabulce jsou uvedeny přibližně kolik agentů může komunikovat se serverem brány. Podpora je založena na agenty, kteří odesílají 200 KB dat každých 6 sekund. Objem dat pro každého agenta testování, je přibližně 2.7 GB za den.

|brána |Nepodporuje agenty (přibližné)|  
|--------|----------------------------------|  
|Procesor: Intel Xeon E5-2660 v3 procesoru \@ 2,6 GHz, 2 jádra<br> Paměť: 4 GB<br> Šířka pásma sítě: 1 Gb/s| 600|  
|Procesor: Intel Xeon E5-2660 v3 procesoru \@ 2,6 GHz, 4 jádra<br> Paměť: 8 GB<br> Šířka pásma sítě: 1 Gb/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Stáhněte si bránu Log Analytics

Získat nejnovější verzi instalačního souboru Log Analytics gateway buď z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) nebo na webu Azure portal.

Získat brány Log Analytics z portálu Azure portal, postupujte takto:

1. Procházet seznam služeb a pak vyberte **Log Analytics**. 
1. Vyberte pracovní prostor.
1. V okně pracovního prostoru v části **Obecné**vyberte **rychlý Start**. 
1. V části **vyberte zdroj dat pro připojení k pracovnímu prostoru**vyberte **počítače**.
1. V **přímý Agent** okně vyberte **stáhnout Log Analytics gateway**.
 
   ![Snímek obrazovky kroků, stáhněte si bránu Log Analytics](./media/gateway/download-gateway.png)

nebo 

1. V okně pracovního prostoru v části **nastavení**vyberte **upřesňující nastavení**.
1. Přejděte na **připojené zdroje** > **servery Windows** a vyberte **stáhnout Log Analytics gateway**.

## <a name="install-the-log-analytics-gateway"></a>Instalace brány Log Analytics

Pokud chcete nainstalovat bránu, postupujte takto.  (Pokud jste nainstalovali předchozí verzi volá předávání Log Analytics, bude upgradována na tuto verzi.)

1. Z cílové složky, dvakrát klikněte na panel **Log Analytics gateway.msi**.
1. Na **úvodní** stránce vyberte **Další**.

   ![Snímek obrazovky s úvodní stránka Průvodce instalací brány](./media/gateway/gateway-wizard01.png)

1. Na **licenční smlouvy** stránce **souhlasím s podmínkami licenční smlouvy** svůj souhlas s licenčními podmínkami pro Software společnosti Microsoft, a potom vyberte **Další**.
1. Na **portu a proxy adresy** stránky:

   a. Zadejte číslo portu TCP pro použití brány. Instalační program používal toto číslo portu v bráně Windows Firewall nakonfigurovat pravidlo pro příchozí spojení.  Výchozí hodnota je 8080.
      Platný rozsah číslo portu je 1 až 65535. Pokud vstup do tohoto rozsahu nespadá, zobrazí se chybová zpráva.

   b. Pokud server, kde je nainstalovaná brána potřebuje komunikovat přes proxy server, zadejte adresu proxy, kde je potřeba brána připojení. Zadejte například `http://myorgname.corp.contoso.com:80`.  Pokud toto pole ponecháte prázdné, brána se pokusí připojit přímo k Internetu.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo.

   c. Vyberte **Další**.

   ![Snímek obrazovky s konfigurací pro proxy server brány](./media/gateway/gateway-wizard02.png)

1. Pokud nemáte povolená služba Microsoft Update, zobrazí se stránka Microsoft Update a můžete ji povolit. Proveďte výběr a potom vyberte **Další**. V opačném případě pokračujte k dalšímu kroku.
1. Na **cílovou složku** stránky, ponechte výchozí složky C:\Program Files\OMS brány nebo zadejte umístění, kam chcete nainstalovat bránu. Pak vyberte **Další**.
1. Na **připraveno k instalaci** stránce **nainstalovat**. Pokud nástroj Řízení uživatelských účtů požádá o oprávnění k instalaci, vyberte **Ano**.
1. Po dokončení instalačního programu vyberte **Dokončit**. Pokud chcete ověřit, zda je spuštěna služba, otevřete modul snap-in services.msc a ověřte, že **bránu OMS** se zobrazí v seznamu služeb a jestli má stav **systémem**.

   ![Snímek obrazovky z místní služby, zda je spuštěna brána OMS](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Konfigurace služby Vyrovnávání zatížení sítě 
Bránu pro zajištění vysoké dostupnosti pomocí služby Vyrovnávání zatížení sítě (NLB) společnosti Microsoft můžete nakonfigurovat [načíst vyrovnávání sítě (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), nebo nástroje pro vyrovnávání zatížení na základě hardwaru. Nástroje pro vyrovnávání zatížení spravuje provozu přesměrování mezi jeho uzly požadované připojení z agentů Log Analytics nebo serverů pro správu Operations Manageru. Pokud jeden server brány ocitne mimo provoz, provoz přesměrován do dalších uzlů.

### <a name="microsoft-network-load-balancing"></a>Vyrovnávání zatížení sítě Microsoft
Zjistěte, jak navrhnout a nasadit cluster programu pro vyrovnávání zatížení sítě systému Windows Server 2016, najdete v článku [Vyrovnávání zatížení sítě](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Následující kroky popisují, jak konfigurovat cluster vyrovnávání zatížení sítě společnosti Microsoft.  

1. Přihlaste do Windows serveru, který je členem clusteru programu NLB s účtem správce.  
2. Ve Správci serveru otevřete Správce vyrovnávání zatížení sítě, klikněte na tlačítko **nástroje**a potom klikněte na tlačítko **Správce vyrovnávání zatížení sítě**.
3. Pro připojení k serveru služby Brána Log Analytics pomocí Microsoft Monitoring Agent nainstalován, klikněte pravým tlačítkem na IP adresu clusteru a potom klikněte na tlačítko **přidat hostitele do clusteru**. 

    ![Zatížení vyrovnávání správce – přidat hostitele do clusteru](./media/gateway/nlb02.png)
 
4. Zadejte IP adresu serveru brány, kterou chcete připojit. 

    ![Zatížení vyrovnávání správce – přidat hostitele do clusteru: Připojení](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
Zjistěte, jak navrhnout a nasadit služby Azure Load Balancer, najdete v článku [co je Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Pokud chcete nasadit nástroj pro vyrovnávání zatížení základní, postupujte podle kroků uvedených v tomto [rychlý Start](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) s výjimkou kroků uvedených v části **vytvořit back-end serverů**.   

> [!NOTE]
> Konfigurace pomocí nástroje pro vyrovnávání zatížení Azure **základní SKU**, vyžaduje, aby patřit dostupnosti virtuálních počítačů Azure. Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure](../../virtual-machines/windows/manage-availability.md). Přidat existující virtuální počítače do skupiny dostupnosti, najdete v tématu [nastavit Azure Resource Manageru dostupnosti virtuálních počítačů](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po vytvoření nástroje pro vyrovnávání zatížení back-endový fond je potřeba vytvořit, který distribuuje provoz na jeden nebo více serverů brány. Postupujte podle kroků popsaných v části článku rychlý Start [vytvoření prostředků nástroje pro vyrovnávání zatížení](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Při konfiguraci sondy stavu musí být nakonfigurovaný na použití portu TCP serveru brány. Sonda stavu dynamicky přidá nebo odebere z oběhu nástroje pro vyrovnávání zatížení na základě jejich reakce na kontroly stavu serverů brány. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurace agenta Log Analytics a skupinu pro správu Operations Manageru
V této části uvidíte postup konfigurace přímo připojených agentů Log Analytics, skupiny pro správu Operations Manageru nebo Azure Automation Hybrid Runbook Worker ve službě Log Analytics gateway ke komunikaci s Azure Automation a Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurace agenta Log Analytics do samostatné
Při konfiguraci agenta Log Analytics, nahraďte hodnotu server proxy IP adresu serveru brány Log Analytics a jeho číslo portu. Pokud jste nasadili více serverů brány za nástrojem pro vyrovnávání zatížení, konfigurace proxy serveru agenta Log Analytics je virtuální IP adresa nástroje pro vyrovnávání zatížení.  

>[!NOTE]
>Chcete-li nainstalovat agenta Log Analytics mají brány a Windows, které se připojují přímo ke službě Log Analytics, přečtěte si téma [počítače Windows se připojit ke službě Log Analytics v Azure](agent-windows.md). Připojení počítačů s Linuxem najdete v tématu [konfigurace agenta Log Analytics pro počítače s Linuxem v hybridním prostředí](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Po instalaci agenta na server brány, nakonfigurujte ji hlásit do pracovního prostoru nebo prostoru agentů, kteří komunikují s bránou. Pokud agenta Log Analytics Windows není nainstalována na bráně, se zapíše událost 300 do protokolu událostí bránu OMS, označující, že musí být nainstalovaný agent. Pokud je agent nainstalován, ale není nakonfigurovaný k ukládání dat do stejného pracovního prostoru jako agenti, kteří komunikují přes něj, 105 se zapíše událost do stejného protokolu, která udává, že agent na bráně je potřeba nakonfigurovat k ukládání dat do stejného pracovního prostoru jako agenti této ko mmunicate s bránou.

Po dokončení konfigurace restartujte službu brány OMS změny se projeví. Jinak odmítne brána agenty, kteří se pokusí o komunikovat s Log Analytics a oznámí 105 události v protokolu událostí bránu OMS. Také k tomu dochází při přidání nebo odebrání pracovního prostoru z konfigurace agenta na server brány.   

Informace týkající se služby Automation Hybrid Runbook Worker, naleznete v tématu [automatizaci prostředků v datovém centru nebo v cloudu s využitím procesu Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurace nástroje Operations Manager, kde všechny agenty používat stejný server proxy
Konfigurace proxy serveru nástroje Operations Manager se automaticky využije na všechny agenty nástroje Operations Manager, i když je toto nastavení prázdné.  

Pro podporu nástroje Operations Manager použít bránu OMS, musíte mít:

* Microsoft Monitoring Agent (verze 8.0.10900.0 nebo novější) nainstalován na serveru služby Brána OMS a nakonfigurovanou stejnou pracovních prostorů Log Analytics, že vaši skupinu pro správu je nakonfigurovaný k ukládání dat do.
* Připojení k Internetu. Alternativně bránu OMS, musíte být připojeni k proxy serveru, který je připojený k Internetu.

> [!NOTE]
> Pokud chcete zadat žádná hodnota pro bránu, prázdné hodnoty se nasdílejí do všech agentů.
>

Pokud registraci skupiny pro správu nástroje Operations Manager s pracovním prostorem Log Analytics pro první nezobrazí možnost zadat konfiguraci proxy serveru pro skupinu pro správu v konzole Operations console. Tato možnost je dostupná jenom v případě, že skupina pro správu byl zaregistrován u služby.  

Pokud chcete nakonfigurovat integrace, aktualizujte konfiguraci proxy serveru systému pomocí nástroje Netsh v systému, ve kterém spouštíte konzolu Operations console a na všech serverech pro správu ve skupině pro správu. Postupujte následovně:

1. Otevřete příkazový řádek se zvýšenými oprávněními:

   a. Vyberte **Start** a zadejte **cmd**.  

   b. Klikněte pravým tlačítkem na **příkazového řádku** a vyberte **spustit jako správce**.  

1. Zadejte následující příkaz:

   `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s Log Analytics, odeberte změnu spuštěním `netsh winhttp reset proxy`. Potom použijte v konzoli Operations console **konfigurovat proxy server** můžete zadat server brány nástroje Log Analytics. 

1. V konzole nástroje Operations Manager v části **Operations Management Suite**vyberte **připojení**a pak vyberte **konfigurovat Proxy Server**.

   ![Snímek obrazovky nástroje Operations Manager, zobrazen výběr konfigurovat Proxy Server](./media/gateway/scom01.png)

1. Vyberte **použít proxy server pro přístup k Operations Management Suite** a poté zadejte IP adresu serveru brány pro Log Analytics nebo virtuální IP adresu nástroje pro vyrovnávání zatížení. Dejte pozor, abyste začínat předponou `http://`.

   ![Snímek obrazovky nástroje Operations Manager, zobrazuje adresu serveru proxy](./media/gateway/scom02.png)

1. Vyberte **Finish** (Dokončit). Vaši skupinu pro správu Operations Manageru je nyní nakonfigurováno pro komunikaci prostřednictvím serveru brány ke službě Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurace nástroje Operations Manager, kde konkrétní agenty používat proxy server
Pro rozsáhlá nebo složitá prostředí může být vhodné jenom konkrétním serverům (nebo skupiny) používat server brány nástroje Log Analytics.  Pro tyto servery nemůžete aktualizovat agenta nástroje Operations Manager přímo vzhledem k tomu, že tato hodnota je přepsána globální hodnoty pro skupinu pro správu.  Místo toho přepište pravidlo použít tyto hodnoty.  

> [!NOTE] 
> Pokud chcete povolit pro více serverů brány Log Analytics ve vašem prostředí, použijte tento postup konfigurace.  Může například vyžadovat konkrétní servery brány Log Analytics určit na základě místní.
>

Ke konfiguraci určitých serverů nebo skupin pro použití serveru brány Log Analytics: 

1. Otevřete konzolu nástroje Operations Manager a vyberte **Authoring** pracovního prostoru.  
1. V pracovním prostoru vytváření obsahu, vyberte **pravidla**. 
1. Na panelu nástrojů Operations Manager, vyberte **oboru** tlačítko. Pokud toto tlačítko není k dispozici, ujistěte se, že jste zvolili v objektu, nikoli složku, **monitorování** podokně. **Obor objektů sady Management Pack** dialogové okno zobrazí seznam běžných cílové třídy, skupiny nebo objekty. 
1. V **vyhledejte** zadejte **služba Health Service** a vyberte ho ze seznamu. Vyberte **OK**.  
1. Vyhledejte **pravidla nastavení proxy serveru služby Advisor**. 
1. Na panelu nástrojů Operations Manager, vyberte **přepíše** a přejděte na **přepsat Rule\For konkrétní objekt třídy: Služba Health Service** a vyberte objekt v seznamu.  Nebo vytvořte vlastní skupinu, která obsahuje objekt služba stavu serverů, které chcete použít toto přepsání na. Použijte vlastní skupiny přepsání.
1. V **potlačit vlastnosti** dialogu okně přidejte značku zaškrtnutí v **přepsat** vedle sloupce **WebProxyAddress** parametr.  V **hodnota přepsání** pole, zadejte adresu URL serveru brány pro Log Analytics. Dejte pozor, abyste začínat předponou `http://`.  

    >[!NOTE]
    > Není nutné k povolení pravidla. To je již spravován automaticky pomocí přepsání v sadě management pack Microsoft System Center Advisor zabezpečení odkaz přepsat Microsoft System Center Advisor monitorování skupiny serverů, který cílí.
    > 

1. Vyberte sadu management pack z **vyberte cílovou sadu management pack** seznamu, nebo vytvořte nový nezapečetěný management pack tak, že vyberete **nový**. 
1. Jakmile budete hotovi, vyberte **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurace pro Automation Hybrid Runbook Worker
Pokud máte služby Automation Hybrid Runbook Worker ve vašem prostředí, následujícím postupem pro manuální, dočasných řešení v oblasti konfigurace bránu OMS, která podporují zaměstnanců.

Chcete-li postupovat podle kroků v této části, je potřeba vědět oblast Azure, ve které se nachází na účtu Automation. K vyhledání tohoto umístění:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte službu Azure Automation.
1. Vyberte příslušný účet Azure Automation.
1. Zobrazit jeho oblast v rámci **umístění**.

   ![Snímek obrazovky umístění účtu Automation na webu Azure Portal](./media/gateway/location.png)

Pomocí následující tabulky Identifikujte adresu URL pro každé umístění.

**Adresy URL služby dat získaných za běhu úlohy**

| **Umístění** | **Adresa URL** |
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

| **Umístění** | **Adresa URL** |
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

Pokud váš počítač je registrovaný jako Hybrid Runbook Worker automaticky, pomocí řešení Update Management můžete spravovat opravy. Postupujte následovně:

1. Adresy URL služby dat získaných za běhu úlohy přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics pomocí následující rutiny Powershellu: `Restart-Service OMSGatewayService`

Pokud je počítač připojen k službě Azure Automation pomocí rutiny registrace procesu Hybrid Runbook Worker, postupujte podle těchto kroků:

1. Adresa URL pro registraci agenta služby přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adresy URL služby dat získaných za běhu úlohy přidáte do seznamu Povolené hostitele ve službě Log Analytics gateway. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu brány Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutin prostředí PowerShell
K dokončení úlohy se aktualizovat nastavení konfigurace brány Log Analytics můžete použít rutiny. Před použitím rutin nezapomeňte na následující:

1. Instalace brány Log Analytics (Instalační služby systému Windows).
1. Otevřete okno konzole Powershellu.
1. Importujte modul zadáním tohoto příkazu: `Import-Module OMSGateway`
1. Pokud k žádné chybě došlo v předchozím kroku, modul se úspěšně naimportoval a můžou používat rutiny. Enter `Get-Module OMSGateway`
1. Po použití rutiny provést změny, restartujte službu brány OMS.

Chybu v kroku 3 znamená, že nebyl importován v modulu. Prostředí PowerShell nemůže najít modul může dojít k chybě. V modulu najdete v instalační cestě brána OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci této služby |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Key (vyžadováno) <br> Hodnota |Změny konfigurace služby |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu proxy serveru (nadřazeného) relay |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo |Nastaví adresu (a přihlašovací údaje) (nadřazeného) proxy relay |1. Nastavte relay proxy a přihlašovacích údajů:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavení předávání přes proxy server, která nepotřebuje ověřování: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Vymažte nastavení proxy serveru relay:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povolené hostitele (jenom pro místně nakonfigurovanému hostiteli povolené, staženy automaticky povoleno hostitelů) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených aplikací |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených aplikací |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Přidá certifikát klienta může na seznamu povolených |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Předmět (povinné) |Odebere ze seznamu povolených aplikací předmětu certifikátu klienta |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené klienta předměty certifikátu (jenom pro místně nakonfigurované povolených oborů, není automaticky stažena povolené témata) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží
Shromažďovat události zapsané podle bránu, měli byste mít nainstalovaného agenta Log Analytics.

![Snímek obrazovky seznamu Prohlížeč událostí v protokolu brány Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Událost výchozí brány log Analytics ID a popisy

V následující tabulce jsou uvedeny ID událostí a popisy pro Log Analytics brány protokolu událostí.

| **ID** | **Popis** |
| --- | --- |
| 400 |Chyba jakékoli aplikace, který nemá žádnou konkrétní vlastnost ID. |
| 401 |Chybná konfigurace. Například listenPort = "text" namísto celého čísla. |
| 402 |Výjimka při analýze zprávy metody handshake TLS. |
| 403 |Došlo k chybě sítě. Například nelze připojit k cílovému serveru. |
| 100 |Obecné informace. |
| 101 |Služba byla spuštěna. |
| 102 |Služba byla zastavena. |
| 103 |Příkaz připojení HTTP byla přijata od klienta. |
| 104 |Není připojení protokolu HTTP příkaz. |
| 105 |Cílový server se nenachází v seznamu povolených nebo cílový port není zabezpečený (443). <br> <br> Ujistěte se, že na serveru brány OMS agenta MMA a agenty komunikující s bránou OMS jsou připojené do stejného pracovního prostoru Log Analytics. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN = brány. <br><br> Ujistěte se, že používáte bránu OMS verze 1.0.395.0 nebo vyšší. Také se ujistěte, že na serveru brány OMS agenta MMA a agenty komunikaci s bránou OMS jsou připojené do stejného pracovního prostoru Log Analytics. |
| 106 |Nepodporovaná verze protokolu TLS/SSL.<br><br> Brána Log Analytics podporuje jenom TLS 1.0, TLS 1.1 a 1.2. Nepodporuje SSL.|
| 107 |Relace TLS byla ověřena. |

### <a name="performance-counters-to-collect"></a>Čítače výkonu ke shromažďování

V následující tabulce jsou uvedeny čítačů výkonu k dispozici pro bránu Log Analytics. Můžete přidat čítače sledování výkonu.

| **Název** | **Popis** |
| --- | --- |
| Připojení klienta k log Analytics Gateway/aktivní |Počet aktivních klientů připojení protokolu TCP) |
| Počet brány/chyb log Analytics |Počet chyb |
| Log Analytics brány nebo připojení klienta |Počet připojených klientů |
| Počet brány nebo zamítnutí log Analytics |Počet zamítnutí kvůli chybě ověřování TLS |

![Snímek obrazovky služby Log Analytics rozhraní brány, zobrazuje čítače výkonu](./media/gateway/counters.png)

## <a name="assistance"></a>Pomoc
Když jste přihlášení k webu Azure portal, můžete získat pomoc se sadou gateway Log Analytics nebo jiné služby Azure nebo funkce.
Potřebujete pomoc, vyberte ikonu otazníku v pravém horním rohu portálu a vyberte **nová žádost o podporu**. Dokončete nový formulář žádosti o podporu.

![Snímek obrazovky s novou žádost o podporu](./media/gateway/support.png)

## <a name="next-steps"></a>Další postup
[Přidat zdroje dat](../../azure-monitor/platform/agent-data-sources.md) pro shromažďování dat z připojených zdrojů a ukládání dat ve vašem pracovním prostoru Log Analytics.