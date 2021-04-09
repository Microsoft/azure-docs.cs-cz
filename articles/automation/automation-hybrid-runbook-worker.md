---
title: Přehled Azure Automation Hybrid Runbook Worker
description: Tento článek poskytuje přehled Hybrid Runbook Worker, které můžete použít ke spouštění Runbooků na počítačích v místním datovém centru nebo poskytovateli cloudu.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581268"
---
# <a name="hybrid-runbook-worker-overview"></a>Přehled funkce Hybrid Runbook Worker

Runbooky v Azure Automation nemusí mít přístup k prostředkům v jiných cloudech nebo v místním prostředí, protože běží na cloudové platformě Azure. Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo na počítači, který je hostitelem role a k prostředkům v prostředí za účelem správy těchto místních prostředků. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodány do jednoho nebo více přiřazených počítačů.

## <a name="runbook-worker-types"></a>Typy Runbook Worker

Existují dva typy pracovních procesů Runbooku – systém a uživatel. Rozdíly mezi nimi jsou popsány v následující tabulce.

|Typ | Description |
|-----|-------------|
|**Systém** |Nástroj podporuje sadu skrytých runbooků používaných funkcí Update Management, které jsou určeny k instalaci aktualizací určených uživatelem na počítačích se systémem Windows a Linux.<br> Tento typ Hybrid Runbook Worker není členem skupiny Hybrid Runbook Worker, a proto nespouští Runbooky, které cílí na skupinu Runbook Worker. |
|**Uživatel** |Podporuje uživatelsky definované Runbooky, které mají být spouštěny přímo na počítači s Windows a Linux, které jsou členy jedné nebo více skupin Runbook Worker. |

Hybrid Runbook Worker lze spustit buď v operačním systému Windows nebo Linux, a tato role spoléhá na [Log Analytics agenta](../azure-monitor/agents/log-analytics-agent.md) sestav do Azure monitor [Log Analytics pracovního prostoru](../azure-monitor/logs/design-logs-deployment.md). Pracovní prostor není pouze monitorující počítač pro podporovaný operační systém, ale také ke stažení součástí potřebných k instalaci Hybrid Runbook Worker.

Pokud je povolená Azure Automation [Update Management](./update-management/overview.md) , je počítač připojený k vašemu Log Analytics pracovnímu prostoru automaticky nakonfigurovaný jako Hybrid Runbook Worker systému. Postup při konfiguraci Hybrid Runbook Worker jako uživatel Windows najdete v tématu nasazení [Hybrid Runbook Worker systému Windows](automation-windows-hrw-install.md) a pro Linux najdete v tématu [nasazení Hybrid Runbook Worker pro Linux](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Jak to funguje?

![Přehled funkce Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Každý uživatel Hybrid Runbook Worker je členem skupiny Hybrid Runbook Worker, kterou zadáte při instalaci pracovního procesu. Skupina může obsahovat jeden pracovní proces, ale k zajištění vysoké dostupnosti můžete do skupiny zahrnout více pracovních procesů. Každý počítač může hostovat jeden Hybrid Runbook Worker hlášení na jeden účet Automation; hybridní pracovní proces nemůžete zaregistrovat v několika účtech Automation. Hybridní pracovní proces může naslouchat jenom úlohám z jednoho účtu Automation. Pro počítače, které hostují systém Hybrid Runbook Worker spravované pomocí Update Management, je lze přidat do skupiny Hybrid Runbook Worker. Je ale nutné použít stejný účet Automation pro Update Management a členství v Hybrid Runbook Worker skupině.

Když spouštíte sadu Runbook na uživatelském Hybrid Runbook Worker, zadáte skupinu, na které je spuštěná. Každý pracovní proces ve skupině se Azure Automation dotazuje, zda jsou k dispozici nějaké úlohy. Pokud je úloha k dispozici, první pracovní proces, který úlohu získá, ji vezme. Doba zpracování fronty úloh závisí na hardwarovém profilu a zatížení hybridního pracovního procesu. Nemůžete zadat konkrétního pracovního procesu. Hybridní pracovní proces funguje na mechanismu cyklického dotazování (každých 30 sekund) a řídí se pořadím prvního přihlašování. V závislosti na tom, kdy byla úloha vložena, podle toho, jak Služba Automation otestuje pracovní proces, vybírá úlohu. Jeden hybridní pracovní proces může obecně vybírat čtyři úlohy na jeden test otestuje (to znamená každých 30 sekund). Pokud je rychlost doručování úloh vyšší než čtyři za 30 sekund, pak existuje vysoká možnost, že se v této úloze vybrala jiná hybridní pracovní proces ve skupině Hybrid Runbook Worker.

Hybrid Runbook Worker nemá mnoho [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) prostředků [izolovaného prostoru (sandbox) Azure](automation-runbook-execution.md#runbook-execution-environment) pro místo na disku, paměť nebo síťové sokety. Omezení hybridního pracovního procesu se vztahují pouze na vlastní prostředky pracovního procesu a nejsou omezené na základě [reálného](automation-runbook-execution.md#fair-share) časového limitu sdílení, který mají izolované pracovní prostory Azure.

Pokud chcete řídit distribuci runbooků na hybridních pracovních procesech Runbooku a při spuštění úloh nebo jak se spouštějí úlohy, můžete hybridního pracovního procesu zaregistrovat do různých skupin Hybrid Runbook Worker v rámci svého účtu Automation. Zacílíte úlohy na konkrétní skupinu nebo skupiny, aby bylo možné podporovat vaše ujednání o provádění.

## <a name="hybrid-runbook-worker-installation"></a>Instalace Hybrid Runbook Worker

Proces instalace uživatele Hybrid Runbook Worker závisí na operačním systému. Následující tabulka definuje typy nasazení.

|Operační systém  |Typy nasazení  |
|---------|---------|
|Windows     | [Automatizovaná](automation-windows-hrw-install.md#automated-deployment)<br>[Ruční](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Ruční](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Doporučenou metodou instalace pro počítač s Windows je použití sady Runbook Azure Automation k úplnému automatizaci procesu konfigurace. Pokud to není možné, můžete postupovat podle podrobných pokynů k ruční instalaci a konfiguraci role. Pro počítače se systémem Linux spustíte skript Pythonu, který nainstaluje agenta do počítače.

## <a name="network-planning"></a><a name="network-planning"></a>Plánování sítě

Podrobné informace o portech, adresách URL a dalších podrobných informacích o sítích požadovaných pro Hybrid Runbook Worker najdete v [Azure Automation konfiguraci sítě](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) .

### <a name="proxy-server-use"></a>Použití proxy serveru

Pokud používáte proxy server ke komunikaci mezi Azure Automation a počítači s agentem Log Analytics, ujistěte se, že jsou dostupné příslušné prostředky. Časový limit pro žádosti od Hybrid Runbook Worker a Automation Services je 30 sekund. Po třech pokusech se požadavek nezdařil.

### <a name="firewall-use"></a>Použití brány firewall

Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup. Pokud používáte bránu Log Analytics jako proxy, ujistěte se, že je nakonfigurovaná pro procesy Hybrid Runbook Worker. Viz téma [Konfigurace brány Log Analytics pro procesy Hybrid Runbook Worker pro automatizaci](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Značky služeb

Azure Automation podporuje značky služby virtuální sítě Azure, počínaje značkou služby [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby **GuestAndHybridManagement**  v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro službu Automation. Tato značka služby nepodporuje povolení podrobnějšího řízení tím, že omezí rozsahy IP adres na konkrétní oblast.

Značka služby pro službu Azure Automation poskytuje pouze IP adresy, které se používají v následujících scénářích:

* Triggery webhooků z vaší virtuální sítě
* Povolení komunikace mezi procesy Hybrid Runbook Worker a agenty konfigurace stavu ve vaší virtuální síti ke komunikaci se službou Automation

>[!NOTE]
>Služba tag **GuestAndHybridManagement** aktuálně nepodporuje provádění úloh sady Runbook v izolovaném prostoru Azure, a to pouze přímo na Hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Podpora pro úroveň dopadu 5 (IL5)

Azure Automation Hybrid Runbook Worker lze v Azure Government použít pro podporu úloh úrovně dopadu 5 v obou následujících dvou konfiguracích:

* [Izolovaný virtuální počítač](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Při nasazení vybírají pro tento počítač celý fyzický hostitel, který poskytuje potřebnou úroveň izolace potřebnou k podpoře úloh IL5.

* [Vyhrazené hostitele Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), které poskytují fyzické servery, které mohou hostovat jeden nebo více virtuálních počítačů vyhrazených pro jedno předplatné Azure.

>[!NOTE]
>Výpočetní izolaci prostřednictvím role Hybrid Runbook Worker je k dispozici pro cloudy Azure komerčních i státních úřadů USA.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Update Management adres pro Hybrid Runbook Worker

Kromě standardních adres a portů vyžadovaných pro Hybrid Runbook Worker má Update Management další požadavky na konfiguraci sítě popsané v části [Plánování sítě](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Konfigurace stavu Azure Automation na Hybrid Runbook Worker

V Hybrid Runbook Worker můžete spustit [konfiguraci stavu Azure Automation](automation-dsc-overview.md) . Chcete-li spravovat konfiguraci serverů, které podporují Hybrid Runbook Worker, je nutné přidat servery jako uzly DSC. Další informace najdete v tématu [Povolení počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Omezení služby Runbook Worker

Maximální počet Hybrid Worker skupin na účet Automation je 4000 a platí pro jak pro uživatele, tak pro & pro hybridní pracovní procesy. Pokud máte více než 4 000 počítačů ke správě, doporučujeme vytvořit další účet Automation.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooky na Hybrid Runbook Worker

Můžete mít sady Runbook, které spravují prostředky v místním počítači nebo mohou být spuštěny na prostředky v místním prostředí, kde je nasazen uživatel Hybrid Runbook Worker. V takovém případě můžete zvolit spouštění Runbooků na hybridním pracovním procesu místo v účtu Automation. Sady Runbook spouštěné na Hybrid Runbook Worker jsou stejné ve struktuře jako ty, které spouštíte v účtu Automation. Viz [spuštění runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Úlohy Hybrid Runbook Worker

Hybrid Runbook Worker úlohy se spouštějí pod účtem místní **systém** ve Windows nebo v [účtu Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) v systému Linux. Azure Automation zpracovává úlohy pro procesy Hybrid Runbook Worker jinak než úlohy spuštěné v izolovaném prostoru Azure. Viz [prostředí pro spuštění sady Runbook](automation-runbook-execution.md#runbook-execution-environment).

Pokud se Hybrid Runbook Worker hostitelský počítač restartuje, všechny spuštěné úlohy Runbooku se restartují od začátku nebo z posledního kontrolního bodu pro Runbooky pracovního postupu PowerShellu. Po restartování úlohy Runbooku je více než třikrát pozastaveno.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Oprávnění sady Runbook pro Hybrid Runbook Worker

Protože mají přístup k prostředkům mimo Azure, sady Runbook spuštěné v uživatelském Hybrid Runbook Worker nemůžou používat mechanismus ověřování, který používají Runbooky ověřující prostředky Azure. Sada Runbook buď poskytuje vlastní ověřování pro místní prostředky, nebo konfiguruje ověřování pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Můžete také zadat účet Spustit jako, který bude poskytovat kontext uživatele pro všechny sady Runbook.

## <a name="view-system-hybrid-runbook-workers"></a>Zobrazit systémové procesy Runbooku pro Hybrid

Po povolení funkce Update Management v počítačích se systémem Windows nebo Linux můžete v Azure Portal inventarizaci seznamu skupiny System Hybrid Runbook Worker. Na portálu můžete zobrazit až 2 000 pracovních procesů tak, že v levém podokně pro vybraný účet Automation vyberete **skupinu hybridní pracovní procesy systému** karta ze **skupiny hybridní pracovní** procesy.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Stránka skupiny hybridních pracovních procesů systému účtů služby Automation" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Pokud máte více než 2 000 hybridních pracovních procesů a získáte seznam všech těchto možností, můžete spustit následující skript prostředí PowerShell:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Informace o tom, jak řešit potíže s procesy Hybrid Runbook Worker, najdete v tématu [řešení potíží s Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
