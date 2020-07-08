---
title: Přehled Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje přehled Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: d921ecc390ae9361c9b36b4738e73a499aa2e8a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361808"
---
# <a name="hybrid-runbook-worker-overview"></a>Přehled funkce Hybrid Runbook Worker

Runbooky v Azure Automation nemusí mít přístup k prostředkům v jiných cloudech nebo v místním prostředí, protože běží na cloudové platformě Azure. Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo na počítači, který je hostitelem role a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodány do jednoho nebo více přiřazených počítačů.

Tato funkce je znázorněna na následujícím obrázku:

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker lze spustit buď v operačním systému Windows nebo Linux. Závisí na [Log Analytics agenta](../azure-monitor/platform/log-analytics-agent.md) , který se hlásí do [pracovního prostoru Azure monitor Log Analytics](../azure-monitor/platform/design-logs-deployment.md). Pracovní prostor není pouze monitorující počítač pro podporovaný operační systém, ale také ke stažení součástí potřebných pro Hybrid Runbook Worker.

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

Doporučeným způsobem instalace je použití sady Runbook Azure Automation k úplnému automatizaci procesu konfigurace počítače s Windows. Pokud to není možné, můžete postupovat podle podrobných pokynů k ruční instalaci a konfiguraci role. Pro počítače se systémem Linux spustíte skript Pythonu, který nainstaluje agenta do počítače.

## <a name="network-planning"></a><a name="network-planning"></a>Plánování sítě

Aby bylo možné Hybrid Runbook Worker připojit k Azure Automation a zaregistrovat se v ní, musí mít přístup k číslu portu a adresám URL popsaným v této části. Pracovní proces musí mít také přístup k [portům a adresám URL vyžadovaným pro Log Analytics agenta](../azure-monitor/platform/agent-windows.md) pro připojení k pracovnímu prostoru Azure monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pro Hybrid Runbook Worker jsou vyžadovány následující porty a adresy URL:

* Port: pro odchozí přístup k Internetu se vyžaduje jenom TCP 443.
* Globální adresa URL:`*.azure-automation.net`
* Globální adresa URL US Gov – Virginie:`*.azure-automation.us`
* Služba agenta:`https://<workspaceId>.agentsvc.azure-automation.net`

Pokud máte účet Automation, který je definovaný pro konkrétní oblast, můžete Hybrid Runbook Worker komunikaci omezit na příslušné oblastní datové centrum. Zkontrolujte [záznamy DNS používané Azure Automation](how-to/automation-region-dns-records.md) pro požadované záznamy DNS.

### <a name="proxy-server-use"></a>Použití proxy serveru

Pokud používáte proxy server ke komunikaci mezi Azure Automation a počítači s agentem Log Analytics, ujistěte se, že jsou dostupné příslušné prostředky. Časový limit pro žádosti od Hybrid Runbook Worker a Automation Services je 30 sekund. Po třech pokusech se požadavek nezdařil.

### <a name="firewall-use"></a>Použití brány firewall

Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup. Pokud používáte bránu Log Analytics jako proxy, ujistěte se, že je nakonfigurovaná pro procesy Hybrid Runbook Worker. Viz téma [Konfigurace brány Log Analytics pro hybridní pracovní procesy automatizace](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

### <a name="service-tags"></a>Značky služeb

Azure Automation podporuje značky služby virtuální sítě Azure, počínaje značkou služby [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby **GuestAndHybridManagement** v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro službu Automation. Tato značka služby nepodporuje povolení podrobnějšího řízení tím, že omezí rozsahy IP adres na konkrétní oblast.

Značka služby pro službu Azure Automation poskytuje pouze IP adresy, které se používají v následujících scénářích:

* Triggery webhooků z vaší virtuální sítě
* Povolení komunikace mezi procesy Hybrid Runbook Worker a agenty konfigurace stavu ve vaší virtuální síti ke komunikaci se službou Automation

>[!NOTE]
>Služba tag **GuestAndHybridManagement** aktuálně nepodporuje provádění úloh sady Runbook v izolovaném prostoru Azure, a to pouze přímo na Hybrid Runbook Worker.

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management v Hybrid Runbook Worker

Pokud je povolená Azure Automation [Update Management](automation-update-management.md) , bude se každý počítač připojený k vašemu Log Analytics pracovnímu prostoru automaticky konfigurovat jako Hybrid Runbook Worker. Každý pracovní proces může podporovat Runbooky cílené na správu aktualizací.

Počítač nakonfigurovaný tímto způsobem není zaregistrovaný u žádné Hybrid Runbook Worker skupiny, které už jsou ve vašem účtu Automation definované. Počítač můžete přidat do skupiny Hybrid Runbook Worker, ale je nutné použít stejný účet pro Update Management a členství ve skupině Hybrid Runbook Worker. Tato funkce se přidala do 7.2.12024.0 verze Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Update Management adres pro Hybrid Runbook Worker

Nad standardními adresami a porty, které Hybrid Runbook Worker vyžaduje, Update Management potřebuje adresy v další tabulce. Komunikace s těmito adresami používá port 443.

|Veřejný partnerský vztah Azure  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`     | `*.ods.opinsights.azure.us`         |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Konfigurace stavu Azure Automation na Hybrid Runbook Worker

V Hybrid Runbook Worker můžete spustit [konfiguraci stavu Azure Automation](automation-dsc-overview.md) . Chcete-li spravovat konfiguraci serverů, které podporují Hybrid Runbook Worker, je nutné přidat servery jako uzly DSC. Další informace najdete v tématu [Povolení počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooky na Hybrid Runbook Worker

Můžete mít Runbooky, které spravují prostředky v místním počítači nebo se můžou spouštět na prostředky v místním prostředí, kde je nasazený Hybrid Runbook Worker. V takovém případě můžete zvolit spouštění Runbooků na hybridním pracovním procesu místo v účtu Automation. Sady Runbook spouštěné na Hybrid Runbook Worker jsou stejné ve struktuře jako ty, které spouštíte v účtu Automation. Viz [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Úlohy Hybrid Runbook Worker

Hybrid Runbook Worker úlohy se spouštějí pod účtem místní **systém** ve Windows nebo v [účtu Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) v systému Linux. Azure Automation zpracovává úlohy pro procesy Hybrid Runbook Workers trochu jinak než úlohy spuštěné v izolovaném prostoru Azure. Viz [prostředí pro spuštění sady Runbook](automation-runbook-execution.md#runbook-execution-environment).

Pokud se Hybrid Runbook Worker hostitelský počítač restartuje, všechny spuštěné úlohy Runbooku se restartují od začátku nebo z posledního kontrolního bodu pro Runbooky pracovního postupu PowerShellu. Po restartování úlohy Runbooku je více než třikrát pozastaveno.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Oprávnění sady Runbook pro Hybrid Runbook Worker

Protože mají přístup k prostředkům mimo Azure, Runbooky spouštěné v Hybrid Runbook Worker nemůžou použít ověřovací mechanismus, který používají Runbooky ověřování pro prostředky Azure. Sada Runbook buď poskytuje vlastní ověřování pro místní prostředky, nebo konfiguruje ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Můžete také zadat účet Spustit jako, který bude poskytovat kontext uživatele pro všechny sady Runbook.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).