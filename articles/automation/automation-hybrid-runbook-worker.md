---
title: Přehled Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje přehled Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: f555d603756159dd108345260d71233c23c01ce5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715711"
---
# <a name="hybrid-runbook-worker-overview"></a>Přehled funkce Hybrid Runbook Worker

Runbooky v Azure Automation nemusí mít přístup k prostředkům v jiných cloudech nebo v místním prostředí, protože běží na cloudové platformě Azure. Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodány jednomu nebo více přiřazeným počítačům.

Tato funkce je znázorněna na následujícím obrázku:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker může spustit operační systém Windows nebo Linux. Pro účely monitorování vyžaduje použití Azure Monitor a agenta Log Analytics pro podporovaný operační systém. Další informace najdete v tématu [Azure monitor](automation-runbook-execution.md#azure-monitor).

Každý Hybrid Runbook Worker je členem skupiny Hybrid Runbook Worker, kterou zadáte při instalaci agenta. Skupina může obsahovat jediného agenta, ale můžete do skupiny nainstalovat více agentů pro zajištění vysoké dostupnosti. Každý počítač může hostovat jeden hybridní pracovní proces, který je podřízen jednomu účtu Automation. 

Při spuštění sady Runbook na Hybrid Runbook Worker zadáte skupinu, ve které je spuštěna. Každý pracovní proces ve skupině se Azure Automation dotazuje, zda jsou k dispozici nějaké úlohy. Pokud je úloha k dispozici, první pracovní proces, který úlohu získá, ji vezme. Doba zpracování fronty úloh závisí na hardwarovém profilu a zatížení hybridního pracovního procesu. Nemůžete zadat konkrétního pracovního procesu. 

Místo [izolovaného prostoru (sandboxu) Azure](automation-runbook-execution.md#runbook-execution-environment) použijte Hybrid Runbook Worker, protože nemá mnoho [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) izolovaného prostoru (sandboxu) na místě na disku, paměti nebo síťových soketech. Omezení hybridního pracovního procesu se vztahují pouze na vlastní prostředky pracovního procesu. 

> [!NOTE]
> Hybridní pracovní procesy Runbooku nejsou omezené na základě [reálného](automation-runbook-execution.md#fair-share) časového limitu sdílení, který mají Azure Sandbox. 

## <a name="hybrid-runbook-worker-installation"></a>Instalace Hybrid Runbook Worker

Proces instalace Hybrid Runbook Worker závisí na operačním systému. Následující tabulka definuje typy nasazení.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [Automatizovaná](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Doporučeným způsobem instalace je použití sady Runbook Azure Automation k úplnému automatizaci procesu konfigurace počítače se systémem Windows. Druhou metodou je postupovat podle podrobných pokynů k ruční instalaci a konfiguraci role. Pro počítače se systémem Linux spustíte skript Pythonu, který nainstaluje agenta do počítače.

## <a name="network-planning"></a><a name="network-planning"></a>Plánování sítě

Aby bylo možné Hybrid Runbook Worker připojit k Azure Automation a zaregistrovat se v ní, musí mít přístup k číslu portu a adresám URL popsaným v této části. Pracovní proces musí mít také přístup k [portům a adresám URL vyžadovaným pro Log Analytics agenta](../azure-monitor/platform/agent-windows.md) pro připojení k pracovnímu prostoru Azure monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pro Hybrid Runbook Worker jsou vyžadovány následující porty a adresy URL:

* Port: pro odchozí přístup k Internetu se vyžaduje jenom TCP 443.
* Globální adresa URL: *. azure-automation.net
* Globální adresa URL US Gov – Virginie: *.azure-automation.us
* Služba agenta: https:// \< ID pracovního prostoru \> . agentsvc.Azure-Automation.NET

Doporučujeme používat adresy uvedené při definování [výjimek](automation-runbook-execution.md#exceptions). Pro IP adresy můžete stáhnout [Microsoft Azure rozsahy IP adres datacentra](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Tento soubor se aktualizuje týdně a má aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres.

### <a name="dns-records-per-region"></a>Záznamy DNS na oblast

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete Hybrid Runbook Worker komunikaci omezit na příslušné oblastní datové centrum. Následující tabulka uvádí záznam DNS pro každou oblast.

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Austrálie – střed |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Austrálie – východ |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indie – střed |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA – východ 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA – středojih |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA – středozápad | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA – západ 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Seznam IP adres oblastí, nikoli názvy oblastí, si můžete stáhnout ze služby Stažení [softwaru ve službě](https://www.microsoft.com/download/details.aspx?id=41653) stažení softwaru. Aktualizovaný soubor IP adresy je zveřejněný týdně. 

V souboru IP adresy jsou uvedené rozsahy IP adres, které se používají v datových centrech Microsoft Azure. Zahrnuje výpočetní prostředí, SQL a rozsahy úložiště a odráží aktuálně nasazené rozsahy a všechny nadcházející změny rozsahu IP adres. Nové rozsahy, které se zobrazí v souboru, se v datových centrech nepoužijí aspoň na jeden týden.

Je vhodné stáhnout nový soubor s IP adresou každý týden. Pak aktualizujte svůj web tak, aby správně identifikoval služby běžící v Azure. 

> [!NOTE]
> Pokud používáte Azure ExpressRoute, pamatujte na to, že soubor IP adresy se používá k aktualizaci inzerce protokolu BGP (Border Gateway Protocol) v Azure Space v prvním týdnu v měsíci.

### <a name="proxy-server-use"></a>Použití proxy serveru

Pokud používáte proxy server ke komunikaci mezi Azure Automation a agentem Log Analytics, ujistěte se, že jsou dostupné příslušné prostředky. Časový limit pro žádosti od Hybrid Runbook Worker a Automation Services je 30 sekund. Po třech pokusech se požadavek nezdařil. 

### <a name="firewall-use"></a>Použití brány firewall

Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup. Pokud používáte bránu Log Analytics jako proxy, ujistěte se, že je nakonfigurovaná pro procesy Hybrid Runbook Worker. Viz téma [Konfigurace brány Log Analytics pro hybridní pracovní procesy automatizace](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management v Hybrid Runbook Worker

Pokud je povolená Azure Automation [Update Management](automation-update-management.md) , všechny počítače připojené k vašemu Log Analytics pracovnímu prostoru se automaticky nakonfigurují jako Hybrid Runbook Worker. Každý pracovní proces může podporovat Runbooky cílené na správu aktualizací. 

Počítač nakonfigurovaný tímto způsobem není zaregistrovaný u žádné Hybrid Runbook Worker skupiny, které jsou už ve vašem účtu Automation definované. Počítač můžete přidat do skupiny Hybrid Runbook Worker, ale je nutné použít stejný účet pro Update Management a členství ve skupině Hybrid Runbook Worker. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Update Management adres pro Hybrid Runbook Worker

Nad standardními adresami a porty, které Hybrid Runbook Worker vyžaduje, Update Management potřebuje adresy v další tabulce. Komunikace s těmito adresami používá port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Konfigurace stavu Azure Automation na Hybrid Runbook Worker

V Hybrid Runbook Worker můžete spustit [konfiguraci stavu Azure Automation](automation-dsc-overview.md) . Chcete-li spravovat konfiguraci serverů, které podporují Hybrid Runbook Worker, je nutné přidat servery jako uzly DSC. Další informace najdete v tématu [Povolení počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooky na Hybrid Runbook Worker

Můžete mít Runbooky, které spravují prostředky v místním počítači, nebo je spouštět na prostředky v místním prostředí, kde je nasazená Hybrid Runbook Worker. V takovém případě můžete zvolit spouštění Runbooků na hybridním pracovním procesu místo v účtu Automation. Sady Runbook spouštěné na Hybrid Runbook Worker jsou stejné ve struktuře jako ty, které spouštíte v účtu Automation. Viz [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Úlohy Hybrid Runbook Worker

Hybrid Runbook Worker úlohy se spouštějí pod účtem místní **systém** ve Windows nebo v [účtu Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) v systému Linux. Azure Automation zpracovává úlohy pro procesy Hybrid Runbook Workers trochu jinak než úlohy spuštěné v izolovaném prostoru Azure. Viz [prostředí pro spuštění sady Runbook](automation-runbook-execution.md#runbook-execution-environment).

Pokud se Hybrid Runbook Worker hostitelský počítač restartuje, všechny spuštěné úlohy Runbooku se restartují od začátku nebo z posledního kontrolního bodu pro Runbooky pracovního postupu PowerShellu. Po restartování úlohy Runbooku je více než třikrát pozastaveno.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Oprávnění sady Runbook pro Hybrid Runbook Worker

Protože mají přístup k prostředkům mimo Azure, Runbooky spouštěné v Hybrid Runbook Worker nemůžou použít ověřovací mechanismus, který používají Runbooky ověřování pro prostředky Azure. Sada Runbook buď poskytuje vlastní ověřování pro místní prostředky, nebo konfiguruje ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Můžete také zadat účet Spustit jako, který bude poskytovat kontext uživatele pro všechny sady Runbook.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).