---
title: Přehled agenta Analýzy protokolů
description: Toto téma vám pomůže pochopit, jak shromažďovat data a monitorovat počítače hostované v Azure, v místním nebo jiném cloudovém prostředí pomocí Analýzy protokolů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 1ca03cde57a9496054d0860fbb70bd286caabe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533245"
---
# <a name="log-analytics-agent-overview"></a>Přehled agenta Analýzy protokolů
Agent Azure Log Analytics byl vyvinut pro komplexní správu napříč virtuálními počítači v libovolném cloudu, místních počítačích a v počítačích monitorovaných [system center operations managerem](https://docs.microsoft.com/system-center/scom/). Agenti Windows a Linuxu odesílají shromážděná data z různých zdrojů do pracovního prostoru Log Analytics ve službě Azure Monitor, stejně jako všechny jedinečné protokoly nebo metriky definované v řešení monitorování. Agent Log Analytics také podporuje přehledy a další služby v Azure Monitoru, jako je [Azure Monitor pro virtuální počítače](../insights/vminsights-enable-overview.md), Azure Security [Center](/azure/security-center/)a [Azure Automation](../../automation/automation-intro.md).

Tento článek obsahuje podrobný přehled požadavků na agenta, systém a síť a různé metody nasazení.

> [!NOTE]
> Může se také zobrazit agent analýzy protokolů označovaný jako agent Monitorování společnosti Microsoft (MMA) nebo OMS Linux.

> [!NOTE]
> Rozšíření Azure Diagnostics je jedním z agentů, kteří jsou k dispozici pro shromažďování dat monitorování z hostovaného operačního systému výpočetních prostředků. Viz [Přehled agentů Azure Monitoru](agents-overview.md) pro popis různých agentů a pokyny k výběru příslušných agentů pro vaše požadavky.

## <a name="comparison-to-azure-diagnostics-extension"></a>Porovnání s rozšířením diagnostiky Azure
[Rozšíření diagnostiky Azure](diagnostics-extension-overview.md) ve službě Azure Monitor se taky dá použít ke shromažďování dat monitorování z hostovaného operačního systému virtuálních počítačů Azure. Můžete se rozhodnout použít jeden nebo oba v závislosti na vašich požadavcích. Podrobný přehled [agentů Azure Monitoru](agents-overview.md) najdete v článku Podrobný porovnání agentů Azure Monitoru. 

Hlavní rozdíly, které je třeba zvážit, jsou:

- Rozšíření Diagnostika Azure lze použít pouze s virtuálními počítači Azure. Agent log analytics lze použít s virtuálními počítači v Azure, jiných cloudů a místní.
- Rozšíření Diagnostika Azure odesílá data do Azure Storage, [metriky Azure Monitor](data-platform-metrics.md) (jenom windows) a centra událostí. Agent Log Analytics shromažďuje data do [protokolů monitorování Azure](data-platform-logs.md).
- Agent Log Analytics je vyžadován pro [řešení](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md)a další služby, jako je Azure Security [Center](/azure/security-center/).

## <a name="costs"></a>Náklady
Neexistuje žádné náklady pro agenta Log Analytics, ale může vám účtovat poplatky za pomohou za pozůstalé dat. Podívejte se [na spravovat využití a náklady pomocí protokolů monitorování Azure,](manage-cost-storage.md) kde najdete podrobné informace o cenách za data shromážděná v pracovním prostoru Log Analytics.

## <a name="data-collected"></a>Shromažďovaná data
V následující tabulce jsou uvedeny typy dat, které můžete nakonfigurovat pracovní prostor Log Analytics pro shromažďování od všech připojených agentů. Podívejte se [na co monitor Azure Monitor?](../monitor-reference.md) seznam přehledů, řešení a dalších řešení, která používají agenta Log Analytics ke shromažďování dalších druhů dat.

| Zdroj dat | Popis |
| --- | --- |
| [Protokoly událostí systému Windows](data-sources-windows-events.md) | Informace odeslané do systému protokolování událostí systému Windows. |
| [Syslog](data-sources-syslog.md)                     | Informace odeslané do systému protokolování událostí Linuxu. |
| [Výkon](data-sources-performance-counters.md)  | Číselné hodnoty měření výkonu různých aspektů operačního systému a úloh. |
| [Protokoly iis](data-sources-iis-logs.md)                 | Informace o použití webů služby IIS spuštěných v hostovaném operačním systému. |
| [Vlastní protokoly](data-sources-custom-logs.md)           | Události z textových souborů na počítačích se systémem Windows i Linux. |

## <a name="data-destinations"></a>Cíle dat
Agent Log Analytics odesílá data do pracovního prostoru Analýzy protokolů v Azure Monitoru. Agentwindows může být vícepřímá pro odesílání dat do více pracovních prostorů a skupin pro správu nástroje System Center Operations Manager. Agent Linuxu může odesílat pouze do jednoho cíle.

## <a name="other-services"></a>Další služby
Agent pro Linux a Windows není jenom pro připojení k Azure Monitoru, ale také podporuje Azure Automation pro hostování role hybridního runbooku a dalších služeb, jako je [sledování změn](../../automation/change-tracking.md), [správa aktualizací](../../automation/automation-update-management.md)a Azure [Security Center](../../security-center/security-center-intro.md). Další informace o roli hybridního pracovníka runbooku najdete v [tématu Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalace a konfigurace

Při použití agentů Log Analytics ke shromažďování dat, musíte pochopit následující, abyste mohli naplánovat nasazení agenta:

* Chcete-li shromažďovat data od agentů systému Windows, můžete [nakonfigurovat každého agenta tak, aby se hlásil do jednoho nebo více pracovních prostorů](agent-windows.md), a to i v případě, že je vykazován skupině pro správu nástroje System Center Operations Manager. Agent systému Windows může hlásit až čtyři pracovní prostory.
* Agent Linuxu nepodporuje vícenásobné navádění a může se hlásit pouze do jednoho pracovního prostoru.
* Agent systému Windows podporuje [standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), zatímco agent Linuxu jej nepodporuje.  

Pokud používáte nástroj System Center Operations Manager 2012 R2 nebo novější:

* Každá skupina správy nástroje Operations Manager lze [připojit pouze k jednomu pracovnímu prostoru](om-agents.md).
* Počítače s Linuxem, které se hlásí skupině pro správu, musí být nakonfigurovány tak, aby se hlásily přímo do pracovního prostoru Analýzy protokolů. Pokud vaše počítače s Linuxem již vykazují hlášení přímo do pracovního prostoru a chcete je sledovat pomocí nástroje Operations Manager, [nahlaste](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)je podle pokynů skupině pro správu nástroje Operations Manager .
* Agenta systému Windows pro analýzu protokolů můžete nainstalovat do počítače se systémem Windows a nechat jej nahlásit jak nástroji Operations Manager, integrovaným s pracovním prostorem, tak do jiného pracovního prostoru.


Existuje několik způsobů, jak nainstalovat agenta Log Analytics a připojit počítač k Azure Monitoru v závislosti na vašich požadavcích. V následující tabulce jsou uvedeny jednotlivé metody k určení, která funguje nejlépe ve vaší organizaci.

|Zdroj | Metoda | Popis|
|-------|-------------|-------------|
|Virtuální počítač Azure| [Ručně z webu Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Zadejte virtuální chod, které chcete nasadit z pracovního prostoru Log Analytics. |
| | Rozšíření virtuálního počítače Log Analytics pro [Windows](../../virtual-machines/extensions/oms-windows.md) nebo [Linux](../../virtual-machines/extensions/oms-linux.md) pomocí Azure CLI nebo pomocí šablony Azure Resource Manager | Rozšíření nainstaluje agenta Log Analytics na virtuální počítače Azure a zaregistruje je do existujícího pracovního prostoru Azure Monitor. |
| | [Azure Monitor pro virtuální počítače](../insights/vminsights-enable-overview.md) | Když povolíte monitorování pomocí Azure Monitor u virtuálních počítačů, nainstaluje rozšíření a agenta Log Analytics. |
| | [Automatické zřizování Centra zabezpečení Azure](../../security-center/security-center-enable-data-collection.md) | Azure Security Center můžete zřídit agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všechny nové, které se vytvoří, pokud povolíte sledovat chyby zabezpečení a hrozby. Pokud je povoleno, bude zřízen jakýkoli nový nebo existující virtuální virtuální město bez nainstalovaného agenta. |
| Hybridní počítač s Windows| [Ruční instalace](agent-windows.md) | Nainstalujte agenta Microsoft Monitoring z příkazového řádku. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatizujte instalaci pomocí Azure Automation DSC. |
| | [Šablona Správce prostředků s Azure Stackem](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Pokud jste ve svém datovém centru nasadili Microsoft Azure Stack, použijte šablonu Azure Resource Manageru.| 
| Hybridní počítač s Linuxem| [Ruční instalace](../../azure-monitor/learn/quick-collect-linux-computer.md)|Nainstalujte agenta pro Linux volajícího obálkový skript hostovaný na GitHubu. | 
| System Center Operations Manager|[Integrace operations manageru s analýzou protokolů](../../azure-monitor/platform/om-agents.md) | Nakonfigurujte integraci mezi protokoly Operations Manager a Azure Monitor a převezte shromážděná data z počítačů se systémem Windows, které se hlásí do skupiny pro správu.|  


## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows

Pro agenta windows jsou oficiálně podporovány následující verze operačního systému Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, verze 1709 a 1803
* Windows 7 SP1, Windows 8 Enterprise a Pro a Windows 10 Enterprise a Pro

>[!NOTE]
>Zatímco agent Log Analytics pro Systém Windows byl navržen tak, aby podporoval scénáře monitorování serveru, uvědomujeme si, že můžete spustit klienta systému Windows pro podporu úloh nakonfigurovaných a optimalizovaných pro operační systém serveru. Agent podporuje klienta systému Windows, ale naše řešení monitorování se nezaměřují na scénáře monitorování klientů, pokud není výslovně uvedeno.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Tato část obsahuje podrobnosti o podporovaných distribucích Linuxu.

Počínaje verzemi vydanými po srpnu 2018 provádíme následující změny našeho modelu podpory:  

* Podporovány jsou pouze verze serveru, nikoli klient.  
* Zaměřte se na podporu některého z [distribucí schválených Azure Linuxem](../../virtual-machines/linux/endorsed-distros.md). Všimněte si, že může být určité zpoždění mezi nové distro/verze je Azure Linux schválila a je podporována pro agenta Log Analytics Linux.
* Všechny dílčí verze jsou podporovány pro každou hlavní uvedenou verzi.
* Verze, které prošly datem ukončení podpory výrobce, nejsou podporovány.  
* Nové verze AMI nejsou podporovány.  
* Podporovány jsou pouze verze, ve kterých je ve výchozím nastavení spuštěn a ssl 1.x.

>[!NOTE]
>Pokud používáte distro nebo verzi, která není aktuálně podporována a není zarovnána s naším modelem podpory, doporučujeme vyklopit toto úložiště s potvrzením, že podpora společnosti Microsoft nebude poskytovat pomoc s verzemi rozložených agentů.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) a 7 (x64)  
* Oracle Linux 6 a 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) a 7 (x64)
* Debian GNU/Linux 8 a 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) a 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) a 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 je podporována pouze na platformách x86_x64 (64 bit) a OpenSSL starší než 1.x není podporován a na žádné platformě není podporován.
>

### <a name="agent-prerequisites"></a>Požadavky agenta

V následující tabulce jsou zvýrazněny balíčky potřebné pro podporované distribuce Linuxu, na kterých bude agent nainstalován.

|Požadovaný balíček |Popis |Minimální verze |
|-----------------|------------|----------------|
|Glibc |    Knihovna GNU C | 2.5-12 
|Openssl    | Knihovny OpenSSL | 1.0.x nebo 1.1.x |
|Curl | webový klient cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog nebo syslog-ng jsou nutné ke shromažďování zpráv syslog. Výchozí daemon syslogu ve verzi 5 verze Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporován pro shromažďování událostí syslogu. Chcete-li shromažďovat data syslogu z této verze těchto distribucí, měl by být nainstalován a nakonfigurován daemon rsyslog, který nahradí sysklog.

## <a name="tls-12-protocol"></a>Protokol TLS 1.2

Chcete-li zajistit zabezpečení dat při přenosu do protokolů Azure Monitor, důrazně doporučujeme nakonfigurovat agenta tak, aby používal alespoň zabezpečení transportní vrstvy (TLS) 1.2. Starší verze vrstvy TLS/SSockets Layer (SSL) byly shledány zranitelnými a zatímco v současné době pracují na povolení zpětné kompatibility, **nejsou doporučeny**.  Další informace naleznete v recenzi [Odesílání dat bezpečně pomocí TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Požadavek podpory podepisování kódu SHA-2 pro systém Windows
Agent systému Windows začne používat výhradně sha-2 podepisování května 18, 2020. Tato změna bude mít dopad na zákazníky, kteří používají agenta Analýzy protokolů ve starším operačním systému jako součást jakékoli služby Azure (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). Změna nevyžaduje žádnou akci zákazníka, pokud nepoužíváte agenta ve starší verzi operačního systému (Windows 7, Windows Server 2008 R2 a Windows Server 2008). Zákazníci spuštění na starší verzi operačního systému musí před 18.

1. Nainstalujte nejnovější aktualizaci Service Pack pro váš operační systém. Požadované verze aktualizace Service Pack jsou:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instalace aktualizací sha-2 podepisování systému Windows pro váš operační systém, jak je popsáno v [požadavku podpory podepisování kódu SHA-2 22 pro Windows a WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Aktualizace na nejnovější verzi agenta systému Windows (verze 10.20.18029).
4. Doporučujeme nakonfigurovat agenta tak, aby [používal Protokol TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Síťové požadavky
Agent pro Linux a Windows komunikuje odchozí služby Azure Monitor přes port TCP 443 a pokud se počítač připojí přes bránu firewall nebo proxy server ke komunikaci přes Internet, zkontrolujte níže uvedené požadavky, abyste pochopili konfiguraci sítě Požadované. Pokud vaše zásady zabezpečení IT neumožňují počítačům v síti připojení k Internetu, můžete nastavit [bránu Log Analytics](gateway.md) a potom nakonfigurovat agenta pro připojení přes bránu k protokolům Azure Monitor. Agent pak může přijímat informace o konfiguraci a odesílat shromažďovaná data v závislosti na pravidlech shromažďování dat a řešeních monitorování, která jste povolili v pracovním prostoru.

![Diagram komunikace agenta analýzy protokolů](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Požadavky na síťovou bránu firewall
Níže uvedené informace uvádějí informace o konfiguraci serveru proxy a brány firewall, které jsou vyžadovány pro komunikaci s protokoly Azure Monitoru pro agenta Linuxu a Windows.  

|Prostředek agenta|Porty |Směr |Obejít kontrolu protokolu HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Přístav 443 |Odchozí|Ano |  
|*.oms.opinsights.azure.com |Přístav 443 |Odchozí|Ano |  
|*.blob.core.windows.net |Přístav 443 |Odchozí|Ano |  

Informace o bráně firewall požadované pro Azure Government najdete v [tématu Správa Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Pokud plánujete použít pracovník hybridní sady Runbook Azure Automation pro připojení a registraci ke službě Automation a k použití runbooků nebo řešení pro správu ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v [části Konfigurace sítě pro pracovníka hybridní sady Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent Windows a Linux podporuje komunikaci prostřednictvím proxy serveru nebo brány Log Analytics do Azure Monitoru pomocí protokolu HTTPS.  Anonymní i základní ověřování (uživatelské jméno/heslo) jsou podporovány.  U agenta systému Windows připojeného přímo ke službě je konfigurace serveru proxy určena během instalace nebo [po nasazení](agent-manage.md#update-proxy-settings) z Ovládacích panelů nebo pomocí prostředí PowerShell.  

U agenta Linuxu je proxy server určen během instalace nebo [po instalaci](agent-manage.md#update-proxy-settings) úpravou konfiguračního souboru proxy.conf.  Hodnota konfigurace proxy agenta Linuxu má následující syntaxi:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Pokud váš proxy server nevyžaduje ověření, agent Linuxu stále vyžaduje zadání pseudo uživatele/hesla. Může se jedná o libovolné uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protocol (Protokol) | HTTPS |
|uživatel | Volitelné uživatelské jméno pro ověřování proxy serveru |
|heslo | Volitelné heslo pro ověřování proxy serveru |
|proxyhost | Adresa nebo hlavní název souboru přihlašovacích prostředků brány proxy serveru/analýzy protokolů |
|port | Volitelné číslo portu pro bránu proxy serveru/log analytics |

Příklad: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Pokud v hesle\@používáte speciální znaky, například " ", zobrazí se chyba připojení proxy, protože hodnota je analyzována nesprávně.  Chcete-li tento problém vyřešit, zakódujte heslo v adrese URL pomocí nástroje, jako je [urldecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Další kroky

* Zkontrolujte [zdroje dat,](agent-data-sources.md) abyste porozuměli dostupným zdrojům dat pro shromažďování dat ze systému Windows nebo Linux. 
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení. 
* Další informace o [řešenímonitorování,](../insights/solutions.md) které přidávají funkce do Azure Monitoru a také shromažďují data do pracovního prostoru Log Analytics.

