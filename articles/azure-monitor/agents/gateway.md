---
title: Připojení počítačů pomocí Log Analytics brány | Microsoft Docs
description: Připojení zařízení a Operations Manager monitorovaných počítačů pomocí brány Log Analytics k odesílání dat do služby Azure Automation a Log Analytics, když nemají přístup k Internetu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: d83ee4b53d4ccda9cb2af21fa0cb4c91822ec7c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732016"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Připojení počítačů bez přístupu k Internetu pomocí Log Analytics brány v Azure Monitor

>[!NOTE]
>Při přechodu Microsoft Operations Management Suite (OMS) na monitorování Microsoft Azure se terminologie mění. Tento článek se týká brány OMS jako brány Azure Log Analytics. 
>

Tento článek popisuje, jak nakonfigurovat komunikaci s Azure Automation a Azure Monitor pomocí brány Log Analytics, když jsou počítače, které jsou přímo připojené nebo monitorované Operations Manager, nemají přístup k Internetu. 

Log Analytics brána je předávací proxy HTTP, který podporuje tunelování HTTP pomocí příkazu HTTP CONNECT. Tato brána odesílá data do Azure Automation a pracovní prostor Log Analytics v Azure Monitor jménem počítačů, které se nemohou přímo připojit k Internetu. 

Brána Log Analytics podporuje:

* Probíhá hlášení až do stejných Log Analytics pracovních prostorů nakonfigurovaných u každého agenta za ním a nakonfigurovaných pomocí Azure Automation procesů Hybrid Runbook Worker.  
* Počítače se systémem Windows, na kterých je Microsoft Monitoring Agent přímo připojen k pracovnímu prostoru Log Analytics v Azure Monitor.
* Počítače se systémem Linux, ve kterých je agent Log Analytics pro Linux přímo připojen k pracovnímu prostoru Log Analytics v Azure Monitor.  
* System Center Operations Manager 2012 SP1 s UR7, Operations Manager 2012 R2 s UR3 nebo skupinu pro správu v Operations Manager 2016 nebo novější, která je integrovaná s Log Analytics.  

Některé zásady zabezpečení IT neumožňují připojení k Internetu pro síťové počítače. Tyto nepřipojené počítače by mohly být například zařízení v prodeji (POS) nebo servery podporující IT služby. Pokud chcete tato zařízení připojit k Azure Automation nebo pracovnímu prostoru Log Analytics, abyste je mohli spravovat a monitorovat, můžete je nakonfigurovat tak, aby komunikovaly přímo s Log Analytics bránou. Brána Log Analytics může dostávat informace o konfiguraci a přeposlání dat jejich jménem. Pokud jsou počítače nakonfigurované s agentem Log Analytics pro přímé připojení k pracovnímu prostoru Log Analytics, počítače místo toho komunikují s bránou Log Analytics.  

Brána Log Analytics přenáší data z agentů přímo do služby. Neanalyzuje žádné přenášená data a brána neukládá data do mezipaměti, když ztratí připojení ke službě. Pokud brána nemůže komunikovat se službou, Agent pokračuje v běhu a zařadí shromážděná data do fronty na disk monitorovaného počítače. Po obnovení připojení agent pošle data uložená v mezipaměti, která jsou shromážděna do Azure Monitor.

Pokud je skupina pro správu Operations Manager integrovaná s Log Analytics, servery pro správu je možné nakonfigurovat tak, aby se připojovaly k Log Analytics bráně a přijímaly informace o konfiguraci a odesílali shromážděná data v závislosti na řešení, které jste povolili.  Agenti Operations Manager odesílají data do management server. Například agenti mohou odesílat výstrahy Operations Manager, data vyhodnocení konfigurace, data prostoru instancí a data o kapacitě. Další data s vysokým objemem, jako jsou protokoly Internetová informační služba (IIS), data o výkonu a události zabezpečení, se odesílají přímo do Log Analytics brány. 

Pokud jsou pro monitorování nedůvěryhodných systémů v hraniční síti nebo izolované síti nasazené aspoň jeden server brány Operations Manager, nemůžou tyto servery komunikovat s bránou Log Analytics.  Servery Operations Manager brány mohou hlásit pouze management server.  Pokud je skupina pro správu Operations Manager nakonfigurovaná tak, aby komunikovala s Log Analytics bránou, informace o konfiguraci proxy serveru se automaticky distribuují do každého počítače spravovaného agentem, který je nakonfigurovaný na shromažďování dat protokolu pro Azure Monitor, a to i v případě, že je nastavení prázdné.

Aby byla zajištěna vysoká dostupnost pro přímo propojené nebo skupiny pro správu operací, které komunikují s Log Analytics pracovním prostorem prostřednictvím brány, použijte k přesměrování a distribuci provozu napříč více servery brány službu Vyrovnávání zatížení sítě (NLB). To znamená, že pokud jeden server brány přestane být dostupný, přenos se přesměruje do jiného dostupného uzlu.  

Počítač, který spouští bránu Log Analytics, vyžaduje Log Analytics agenta Windows k identifikaci koncových bodů služby, se kterými musí brána komunikovat. Agent taky musí směrovat bránu tak, aby se nahlásil do stejných pracovních prostorů, s kterými se nakonfigurují agenti nebo skupina pro správu Operations Manager za bránou. Tato konfigurace umožňuje, aby brána a Agent komunikovaly s přiřazeným pracovním prostorem.

Brána může mít více než až čtyři pracovní prostory. Toto je celkový počet pracovních prostorů, které Agent Windows podporuje.  

Každý agent musí mít síťové připojení k bráně, aby agenti mohli automaticky přenášet data do a z brány. Vyhněte se instalaci brány na řadič domény. Počítače se systémem Linux, které jsou za serverem brány, nemohou použít metodu [instalace skriptu obálky](../agents/agent-linux.md#install-the-agent-using-wrapper-script) k instalaci agenta Log Analytics pro Linux. Agenta je nutné stáhnout ručně, zkopírovat do počítače a nainstalovat ručně, protože brána podporuje pouze komunikaci se službami Azure zmíněnými výše.

Následující diagram znázorňuje tok dat z přímých agentů přes bránu, aby se Azure Automation a Log Analytics. Konfigurace proxy agenta se musí shodovat s portem, se kterým je Log Analytics brána nakonfigurovaná.  

![Diagram komunikace mezi přímým agentem a službami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Následující diagram znázorňuje tok dat z Operations Manager skupiny pro správu do Log Analytics.   

![Diagram Operations Manager komunikace s Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Nastavení systému

Počítače určené ke spuštění brány Log Analytics musí mít následující konfiguraci:

* Windows 10, Windows 8.1 nebo Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 nebo Windows Server 2008
* Microsoft .NET Framework 4.5
* Alespoň 4 jádro procesoru a 8 GB paměti 
* [Agent Log Analytics pro Windows](../agents/agent-windows.md) , který je nakonfigurovaný tak, aby nahlásil do stejného pracovního prostoru jako agenti, kteří komunikují přes bránu

### <a name="language-availability"></a>Dostupnost jazyka

Brána Log Analytics je dostupná v těchto jazycích:

- Čínština (zjednodušená)
- Čínština (tradiční)
- Čeština
- Nizozemština
- Angličtina
- Francouzština
- Němčina
- Maďarština
- Italština
- Japonština
- Korejština
- Polština
- Portugalština (Brazílie)
- portugalština (Portugalsko)
- Ruština
- Španělština (mezinárodní)

### <a name="supported-encryption-protocols"></a>Podporované šifrovací protokoly

Brána Log Analytics podporuje pouze TLS (Transport Layer Security) 1,0, 1,1 a 1,2.  Nepodporuje SSL (Secure Sockets Layer) (SSL).  Aby se zajistila bezpečnost dat při přenosu do Log Analytics, nakonfigurujte bránu tak, aby používala aspoň protokol TLS 1,2. Jsou ohrožené starší verze TLS nebo SSL. I když v současné době umožňují zpětnou kompatibilitu, nepoužívejte je.  

Další informace najdete v [zabezpečeném posílání dat pomocí TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Podporovaný počet připojení agenta

Následující tabulka uvádí, kolik agentů může komunikovat se serverem brány. Podpora je založená na agentech, kteří každých 6 sekund odesílají data o 200 KB. U každého testovaného agenta je objem dat přibližně 2,7 GB za den.

|brána |Podporovaná agenti (přibližná)|  
|--------|----------------------------------|  
|Procesor: procesor Intel Xeon E5-2660 V3 \@ 2,6 GHz 2 jádra<br> Paměť: 4 GB<br> Šířka pásma sítě: 1 GB/s| 600|  
|Procesor: procesor Intel Xeon E5-2660 V3 \@ 2,6 GHz 4 jádra<br> Paměť: 8 GB<br> Šířka pásma sítě: 1 GB/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Stažení Log Analytics brány

Nejnovější verzi instalačního souboru brány Log Analytics Gateway získáte buď z webu Microsoft Download Center ([odkaz ke stažení](https://go.microsoft.com/fwlink/?linkid=837444)), nebo Azure Portal.

Pokud chcete bránu Log Analytics z Azure Portal získat, postupujte takto:

1. Procházejte seznamem služeb a vyberte **Log Analytics**. 
1. Vyberte pracovní prostor.
1. V okně pracovního prostoru v části **Obecné** vyberte **rychlé zprovoznění**. 
1. V části **Zvolit zdroj dat pro připojení k pracovnímu prostoru** vyberte **počítače**.
1. V okně **přímý agent** vyberte **Stáhnout Log Analytics bránu**.
 
   ![Snímek obrazovky s postupem, jak stáhnout bránu Log Analytics](./media/gateway/download-gateway.png)

nebo 

1. V okně pracovního prostoru v části **Nastavení** vyberte **Upřesnit nastavení**.
1. Přejít na **připojené zdroje**  >  **Windows servery** a vyberte **Stáhnout Log Analytics bránu**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instalace brány Log Analytics pomocí Průvodce instalací

Chcete-li nainstalovat bránu pomocí Průvodce instalací, postupujte podle těchto kroků. 

1. Z cílové složky poklikejte na **Log Analytics gateway.msi**.
1. Na **úvodní** stránce vyberte **Další**.

   ![Snímek obrazovky úvodní stránky v Průvodci instalací brány](./media/gateway/gateway-wizard01.png)

1. Na stránce **Licenční smlouva** vyberte možnost Souhlasím s **podmínkami licenční smlouvy** , abyste mohli vyjádřit souhlas s licenčními podmínkami pro software společnosti Microsoft, a pak vyberte možnost **Další**.
1. Na stránce **port a adresa proxy serveru** :

   a. Zadejte číslo portu TCP, který se má použít pro bránu. Instalační program používá toto číslo portu ke konfiguraci příchozího pravidla v bráně Windows Firewall.  Výchozí hodnota je 8080.
      Platný rozsah čísla portu je 1 až 65535. Pokud vstup do tohoto rozsahu nespadá, zobrazí se chybová zpráva.

   b. Pokud server, na kterém je brána nainstalovaná, potřebuje komunikovat prostřednictvím proxy serveru, zadejte adresu proxy serveru, kam se musí brána připojit. Zadejte například `http://myorgname.corp.contoso.com:80`.  Pokud toto pole necháte prázdné, brána se pokusí připojit přímo k Internetu.  Pokud vaše proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo.

   c. Vyberte **Další**.

   ![Snímek obrazovky s konfigurací proxy serveru brány](./media/gateway/gateway-wizard02.png)

1. Pokud nemáte Microsoft Update povolený, zobrazí se stránka Microsoft Update a můžete ji vybrat, abyste ji mohli povolit. Proveďte výběr a pak vyberte **Další**. V opačném případě pokračujte k dalšímu kroku.
1. Na stránce **cílová složka** ponechte výchozí složku C:\Program Files\OMS Gateway nebo zadejte umístění, kam chcete bránu nainstalovat. Pak vyberte **Další**.
1. Na stránce **připraveno k instalaci** vyberte **instalovat**. Pokud řízení uživatelských účtů požaduje oprávnění k instalaci, vyberte **Ano**.
1. Po dokončení instalace vyberte **Dokončit**. Pokud chcete ověřit, jestli je služba spuštěná, otevřete modul snap-in Services. msc a ověřte, že se v seznamu služeb zobrazuje **Brána OMS** a že její stav je **spuštěný**.

   ![Snímek obrazovky s místními službami, na kterém je vidět, že je spuštěná brána OMS](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instalace brány Log Analytics pomocí příkazového řádku

Staženým souborem pro bránu je balíček Instalační služba systému Windows, který podporuje tichou instalaci z příkazového řádku nebo jiné automatizované metody. Pokud neznáte standardní možnosti příkazového řádku pro Instalační služba systému Windows, přečtěte si téma [Možnosti příkazového řádku](/windows/desktop/msi/command-line-options).
 
V následující tabulce jsou vysvětlené parametry podporované instalačním programem.

|Parametry| Poznámky|
|----------|------| 
|ČÍSLO_PORTU | Číslo portu TCP, na kterém má brána naslouchat |
|SOUBORY | IP adresa proxy server |
|INSTALLDIR | Plně kvalifikovaná cesta pro určení instalačního adresáře souborů softwaru brány |
|USERNAME | ID uživatele, které se má ověřit pomocí proxy server |
|ZADÁNO | Heslo ID uživatele, které se má ověřit pomocí proxy |
|LicenseAccepted | Zadejte hodnotu **1** , pokud chcete ověřit, že souhlasíte s licenční smlouvou. |
|HASAUTH | Zadejte hodnotu **1** , pokud jsou zadány parametry uživatelského jména a hesla. |
|HASPROXY | Zadejte hodnotu **1** při zadání IP adresy pro parametr **proxy** . |

Pokud chcete bránu nainstalovat tiše a nakonfigurovat ji pomocí konkrétní adresy proxy, čísla portu, zadejte následující příkaz:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Použití možnosti příkazového řádku/qn skrývá instalaci,/QB zobrazuje instalační program během bezobslužné instalace.  

Pokud potřebujete zadat pověření pro ověření u proxy serveru, zadejte následující příkaz:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Po dokončení instalace můžete potvrdit přijetí nastavení (s výjimkou uživatelského jména a hesla) pomocí následujících rutin PowerShellu:

- **Get-OMSGatewayConfig** – vrátí port TCP, na kterém je brána nakonfigurované pro naslouchání.
- **Get-OMSGatewayRelayProxy** – vrátí IP adresu proxy server, se kterou jste nakonfigurovali pro komunikaci.

## <a name="configure-network-load-balancing"></a>Konfigurace vyrovnávání zatížení sítě

Bránu pro vysokou dostupnost můžete nakonfigurovat pomocí služby Vyrovnávání zatížení sítě (NLB) pomocí služby Vyrovnávání zatížení [sítě (NLB)](/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)nebo hardwarových nástrojů pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení spravuje provoz pomocí přesměrování požadovaných připojení od Log Analyticsch agentů nebo serverů pro správu Operations Manager napříč svými uzly. Pokud dojde k výpadku jednoho serveru brány, přenos se přesměruje na jiné uzly.

### <a name="microsoft-network-load-balancing"></a>Vyrovnávání zatížení sítě Microsoftu

Informace o tom, jak navrhnout a nasadit cluster programu pro vyrovnávání zatížení sítě Windows Server 2016, najdete v tématu [Vyrovnávání zatížení sítě](/windows-server/networking/technologies/network-load-balancing). Následující postup popisuje, jak nakonfigurovat cluster programu pro vyrovnávání zatížení sítě společnosti Microsoft.  

1. Přihlaste se k systému Windows Server, který je členem clusteru programu NLB, s účtem správce.  
2. V Správce serveru otevřete Správce vyrovnávání zatížení sítě, klikněte na **nástroje** a potom klikněte na **Správce vyrovnávání zatížení sítě**.
3. Pokud chcete připojit Server Log Analytics brány s nainstalovaným Microsoft Monitoring Agent, klikněte pravým tlačítkem na IP adresu clusteru a pak klikněte na **Přidat hostitele do clusteru**. 

    ![Správce vyrovnávání zatížení sítě – přidání hostitele do clusteru](./media/gateway/nlb02.png)
 
4. Zadejte IP adresu serveru brány, který chcete připojit. 

    ![Správce vyrovnávání zatížení sítě – přidat hostitele do clusteru: připojit](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Informace o tom, jak navrhnout a nasadit Azure Load Balancer, najdete v tématu [co je Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Pokud chcete nasadit základní nástroj pro vyrovnávání zatížení, postupujte podle kroků uvedených v tomto [rychlém](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) startu s výjimkou kroků uvedených v části **Vytvoření back-endové serverů**.   

> [!NOTE]
> Konfigurace Azure Load Balancer pomocí **základní skladové** položky (SKU) vyžaduje, aby virtuální počítače Azure patřily do skupiny dostupnosti. Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů s Windows v Azure](../../virtual-machines/manage-availability.md). Chcete-li přidat existující virtuální počítače do skupiny dostupnosti, přečtěte si téma [nastavení sady dostupnosti virtuálního počítače Azure Resource Manager](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po vytvoření nástroje pro vyrovnávání zatížení je třeba vytvořit back-end fond, který distribuuje provoz na jeden nebo více serverů brány. Postupujte podle kroků popsaných v části článek rychlý Start [vytvoření prostředků pro nástroj pro vyrovnávání zatížení](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Při konfiguraci sondy stavu by měla být nakonfigurovaná tak, aby používala port TCP serveru brány. Sonda stavu dynamicky přidává nebo odebírá servery brány z rotace nástroje pro vyrovnávání zatížení na základě jejich odpovědí na kontroly stavu. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurace agenta Log Analytics a skupiny pro správu Operations Manager

V této části se dozvíte, jak nakonfigurovat přímo připojené Log Analytics agenty, skupinu pro správu Operations Manager nebo Azure Automation procesy Hybrid Runbook Worker s Log Analytics bránou ke komunikaci s Azure Automation nebo Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurace samostatného agenta Log Analytics

Při konfiguraci agenta Log Analytics nahraďte hodnotu proxy server IP adresou serveru brány Log Analytics a číslem portu. Pokud jste nasadili více serverů brány za nástroj pro vyrovnávání zatížení, je konfigurace proxy agenta Log Analytics virtuální IP adresou nástroje pro vyrovnávání zatížení.  

>[!NOTE]
>Chcete-li nainstalovat agenta Log Analytics na počítačích brány a Windows, které se přímo připojují k Log Analytics, přečtěte si téma [připojení počítačů s Windows ke službě Log Analytics v Azure](../agents/agent-windows.md). Informace o připojení počítačů se systémem Linux naleznete v tématu [připojení počítačů se systémem Linux k Azure monitor](../agents/agent-linux.md). 
>

Po instalaci agenta na server brány ho nakonfigurujte tak, aby se nahlásil do pracovního prostoru nebo agentů pracovních prostorů, které komunikují s bránou. Pokud v bráně není nainstalovaný Log Analytics Windows Agent, do protokolu událostí brány OMS se zapíše událost 300, která indikuje, že je potřeba nainstalovat agenta. Pokud je agent nainstalovaný, ale není nakonfigurovaný tak, aby nahlásil do stejného pracovního prostoru jako agenti, kteří ho komunikují, zapíše se do stejného protokolu událost 105, která značí, že agent v bráně musí být nakonfigurovaný tak, aby nahlásil zprávy do stejného pracovního prostoru jako agenti, kteří komunikují s bránou.

Po dokončení konfigurace restartujte službu **brány OMS** , aby se změny projevily. V opačném případě brána odmítne agenty, kteří se pokusí o komunikaci s Log Analytics, a ohlásí událost 105 v protokolu událostí brány OMS. K tomu dojde také v případě, že přidáte nebo odeberete pracovní prostor z konfigurace agenta na serveru brány.

Informace týkající se Hybrid Runbook Worker pro automatizaci najdete v tématu [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Nakonfigurujte Operations Manager, kde všichni agenti používají stejný proxy server

Konfigurace proxy serveru Operations Manager se automaticky použije pro všechny agenty, kteří nahlásí Operations Manager, a to i v případě, že je nastavení prázdné.  

Pokud chcete použít bránu OMS k podpoře Operations Manager, musíte mít:

* Microsoft Monitoring Agent (verze 8.0.10900.0 nebo novější) nainstalované na serveru brány OMS a nakonfigurované se stejnými Log Analytics pracovními prostory, na které je nakonfigurovaná vaše skupina pro správu.
* Připojení k Internetu. Případně musí být brána OMS připojená k proxy server, která je připojená k Internetu.

> [!NOTE]
> Pokud pro bránu nezadáte žádnou hodnotu, budou prázdné hodnoty vloženy všem agentům.
>

Pokud je vaše skupina pro správu Operations Manager v pracovním prostoru Log Analytics registrována poprvé, neuvidíte možnost zadat konfiguraci proxy serveru pro skupinu pro správu v konzole Operations Console. Tato možnost je k dispozici pouze v případě, že je skupina pro správu zaregistrována ve službě.  

Pokud chcete nakonfigurovat integraci, aktualizujte konfiguraci proxy serveru pomocí příkazu Netsh v systému, na kterém je spuštěná konzola Operations Console, a na všech serverech pro správu ve skupině pro správu. Postupujte takto:

1. Otevřete příkazový řádek se zvýšenými oprávněními:

   a. Vyberte **Start** a zadejte **cmd**.  

   b. Klikněte pravým tlačítkem myši na **příkazový řádek** a vyberte **Spustit jako správce**.  

1. Zadejte následující příkaz:

   `netsh winhttp set proxy <proxy>:<port>`

Po dokončení integrace s Log Analytics odeberte změnu spuštěním `netsh winhttp reset proxy` . V konzoli Operations Console pak pomocí možnosti **konfigurovat proxy server** určete server brány Log Analytics. 

1. V konzole Operations Manager v části **Operations Management Suite** vyberte **připojení** a pak vyberte **konfigurovat proxy server**.

   ![Snímek obrazovky Operations Manager zobrazující výběr konfigurace proxy serveru](./media/gateway/scom01.png)

1. Vyberte **použít proxy server pro přístup k Operations Management Suite** a pak zadejte IP adresu serveru Log Analytics brány nebo virtuální IP adresy nástroje pro vyrovnávání zatížení. Buďte opatrní a začněte s předponou `http://` .

   ![Snímek obrazovky Operations Manager se zobrazením proxy server adresy](./media/gateway/scom02.png)

1. Vyberte **Dokončit**. Vaše skupina pro správu Operations Manager je teď nakonfigurovaná tak, aby komunikovala se serverem brány ke službě Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurace Operations Manager, kde konkrétní agenti používají proxy server

U rozsáhlých nebo složitých prostředí můžete pro použití serveru Log Analytics brány použít jenom konkrétní servery (nebo skupiny).  U těchto serverů nelze aktualizovat agenta Operations Manager přímo, protože tato hodnota je přepsána globální hodnotou pro skupinu pro správu.  Místo toho přepište pravidlo používané pro vložení těchto hodnot.  

> [!NOTE] 
> Tuto metodu konfigurace použijte, pokud chcete ve svém prostředí přidělit více serverů Log Analytics brány.  Můžete například vyžadovat, aby byly specifické servery Log Analytics brány specifikovány na regionální úrovni.
>

Konfigurace určitých serverů nebo skupin pro použití serveru Log Analytics brány: 

1. Otevřete konzolu Operations Manager a vyberte pracovní prostor **vytváření obsahu** .  
1. V pracovním prostoru vytváření obsahu vyberte **pravidla**. 
1. Na panelu nástrojů Operations Manager vyberte tlačítko **Rozsah** . Pokud toto tlačítko není k dispozici, ujistěte se, že jste v podokně **monitorování** vybrali objekt, nikoli složku. V dialogovém okně **Rozsah sady Management Pack objekty** se zobrazí seznam společných cílových tříd, skupin nebo objektů. 
1. Do pole **Hledat** zadejte **Health Service** a vyberte ho ze seznamu. Vyberte **OK**.  
1. Vyhledejte **pravidlo nastavení proxy serveru Advisor**. 
1. Na panelu nástrojů Operations Manager vyberte možnost **Overrides** a pak nastavte možnost **přepsat Rule\For specifický objekt třídy: Health Service** a vybrat objekt ze seznamu.  Nebo vytvořte vlastní skupinu, která obsahuje objekt služby stavu serverů, na které chcete toto přepsání použít. Pak použijte přepsání na vlastní skupinu.
1. V dialogovém okně **přepsat vlastnosti** zaškrtněte políčko ve sloupci **přepsat** vedle parametru **WebProxyAddress** .  Do pole **přepsat hodnotu** zadejte adresu URL serveru Log Analytics brány. Buďte opatrní a začněte s předponou `http://` .  

    >[!NOTE]
    > Toto pravidlo není nutné povolit. Je už spravovaný automaticky s přepsáním v Management Pack přepsání zabezpečeného odkazu služby Microsoft System Center Advisor cílí na skupinu monitorovacích serverů služby Microsoft System Center Advisor.
    > 

1. Vyberte Management Pack ze seznamu **vybrat cíl Management Pack** nebo vytvořte novou nezapečetěnou Management Pack výběrem možnosti **Nový**. 
1. Jakmile budete hotovi, vyberte **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurace pro hybridní pracovní procesy Runbooku služby Automation

Pokud máte ve svém prostředí hybridní pracovní procesy Runbooku, postupujte podle těchto kroků a nakonfigurujte bránu tak, aby podporovala pracovní procesy.

Informace o adrese URL pro jednotlivé oblasti najdete v části [Konfigurace sítě](../../automation/automation-hybrid-runbook-worker.md#network-planning) v dokumentaci pro automatizaci.

Pokud je počítač zaregistrován jako Hybrid Runbook Worker automaticky, například pokud je řešení Update Management povoleno pro jeden nebo více virtuálních počítačů, postupujte podle následujících kroků:

1. Přidejte adresy URL běhových dat úlohy do seznamu povolených hostitelů v bráně Log Analytics. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu Log Analytics brány pomocí následující rutiny PowerShellu: `Restart-Service OMSGatewayService`

Pokud je počítač připojený k Azure Automation pomocí rutiny registrace Hybrid Runbook Worker, postupujte podle těchto kroků:

1. Do seznamu povolených hostitelů v bráně Log Analytics přidejte adresu URL pro registraci služby Agent. Příklad: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Přidejte adresy URL běhových dat úlohy do seznamu povolených hostitelů v bráně Log Analytics. Příklad: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Restartujte službu Log Analytics brány.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny PowerShellu

Pomocí rutin můžete dokončit úlohy aktualizace nastavení konfigurace Log Analytics brány. Než začnete používat rutiny, nezapomeňte:

1. Nainstalujte bránu Log Analytics (Microsoft Instalační služba systému Windows).
1. Otevřete okno konzoly PowerShellu.
1. Importujte modul zadáním tohoto příkazu: `Import-Module OMSGateway`
1. Pokud v předchozím kroku nedošlo k žádné chybě, modul byl úspěšně importován a lze použít rutiny. Zadejte `Get-Module OMSGateway`.
1. Po provedení změn pomocí rutin restartujte službu brány OMS.

Chyba v kroku 3 znamená, že modul nebyl naimportován. K této chybě může dojít, když PowerShell nemůže najít modul. Modul najdete v instalační cestě brány OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Rutina** | **Parametry** | **Popis** | **Příklad** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klíč |Získá konfiguraci služby. |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klíč (povinné) <br> Hodnota |Změní konfiguraci služby. |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Získá adresu proxy předávacího (nadřazeného) serveru. |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresa<br> Uživatelské jméno<br> Heslo (zabezpečený řetězec) |Nastaví adresu (a přihlašovací údaje) proxy předávacího (nadřazeného) serveru. |1. Nastavte proxy server přenosu a přihlašovací údaje:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Nastavte přenosového proxy serveru, který nepotřebuje ověřování: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Vymažte nastavení předávacího proxy serveru:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Získá aktuálně povoleného hostitele (jenom místně konfigurovaný povolený hostitel, ale nestahují automaticky povolené hostitele). |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hostitel (povinné) |Přidá hostitele do seznamu povolených. |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hostitel (povinné) |Odebere hostitele ze seznamu povolených. |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Subject (povinné) |Přidá klientský certifikát předmět do seznamu povolených. |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Subject (povinné) |Odebere klientský certifikát ze seznamu povolených. |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Získá aktuálně povolené předměty klientského certifikátu (jenom místně konfigurovaná povolená témata, ale nestahují automaticky povolené subjekty). |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Řešení potíží

Pokud chcete shromažďovat události zaznamenané bránou, měli byste mít nainstalovaného agenta Log Analytics.

![Snímek obrazovky se seznamem Prohlížeč událostí v protokolu Log Analytics brány](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID a popisy událostí Log Analytics brány

Následující tabulka ukazuje ID událostí a popisy pro události protokolu Log Analytics brány.

| **ID** | **Popis** |
| --- | --- |
| 400 |Jakákoli chyba aplikace, která nemá žádné konkrétní ID. |
| 401 |Nesprávná konfigurace Například listenPort = "text" místo Integer. |
| 402 |Při analýze zpráv TLS handshake došlo k výjimce. |
| 403 |Chyba sítě. Například se nemůže připojit k cílovému serveru. |
| 100 |Obecné informace. |
| 101 |Služba byla spuštěna. |
| 102 |Služba byla zastavena. |
| 103 |Z klienta byl přijat příkaz HTTP CONNECT. |
| 104 |Nejedná se o příkaz HTTP CONNECT. |
| 105 |Cílový server není v seznamu povolených nebo cílový port není zabezpečený (443). <br> <br> Zajistěte, aby byl agent MMA na serveru brány OMS a agentech, kteří komunikují s bránou OMS, připojený ke stejnému pracovnímu prostoru Log Analytics. |
| 105 |Chyba TcpConnection – neplatný klientský certifikát: CN = Gateway. <br><br> Ujistěte se, že používáte bránu OMS verze 1.0.395.0 nebo novější. Také zajistěte, aby byl agent MMA na serveru brány OMS a agentech komunikujících s bránou OMS připojen ke stejnému pracovnímu prostoru Log Analytics. |
| 106 |Nepodporovaná verze protokolu TLS/SSL.<br><br> Brána Log Analytics podporuje pouze TLS 1,0, TLS 1,1 a 1,2. Nepodporuje protokol SSL.|
| 107 |Relace TLS byla ověřena. |

### <a name="performance-counters-to-collect"></a>Čítače výkonu ke shromáždění

V následující tabulce jsou uvedené čítače výkonu dostupné pro bránu Log Analytics. Čítače můžete přidat pomocí nástroje sledování výkonu.

| **Název** | **Popis** |
| --- | --- |
| Log Analytics brána/aktivní připojení klienta |Počet aktivních připojení klientů sítě (TCP) |
| Log Analytics brána/počet chyb |Počet chyb |
| Log Analytics brána/připojeného klienta |Počet připojených klientů |
| Log Analytics brána/počet zamítnutí |Počet zamítnutí z důvodu chyby ověřování TLS |

![Snímek obrazovky s rozhraním Log Analytics brány, které zobrazuje čítače výkonu](./media/gateway/counters.png)

## <a name="assistance"></a>Asistence

Když jste se přihlásili k Azure Portal, můžete získat pomoc s bránou Log Analytics nebo jinou službou nebo funkcí Azure.
Pokud chcete získat nápovědu, vyberte ikonu otazníku v pravém horním rohu portálu a vyberte **nový požadavek na podporu**. Potom dokončete formulář nové žádosti o podporu.

![Snímek obrazovky s novou žádostí o podporu](./media/gateway/support.png)

## <a name="next-steps"></a>Další kroky

[Přidejte zdroje dat](./../agents/agent-data-sources.md) pro shromažďování dat z připojených zdrojů a data uložte do svého pracovního prostoru Log Analytics.