---
title: Přehled agenta Azure Monitor
description: Přehled agenta Azure Monitor (AMA), který shromažďuje data monitorování z hostovaného operačního systému virtuálních počítačů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ea2fae483da495bce9551899b9646868251f0454
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030823"
---
# <a name="azure-monitor-agent-overview-preview"></a>Přehled agenta Azure Monitor (Preview)
Agent Azure Monitor (AMA) shromažďuje data monitorování z hostovaného operačního systému virtuálních počítačů a doručuje je do Azure Monitor. V tomto článku najdete přehled agenta Azure Monitor, včetně postupu jeho instalace a konfigurace shromažďování dat.

## <a name="relationship-to-other-agents"></a>Vztah k jiným agentům
Agent Azure Monitor nahrazuje následující agenty, které jsou aktuálně používány nástrojem Azure Monitor ke shromažďování dat hostů z virtuálních počítačů:

- [Agent Log Analytics](log-analytics-agent.md) – odesílá data do pracovního prostoru Log Analytics a podporuje řešení Azure monitor pro virtuální počítače a monitorování.
- [Diagnostické rozšíření](diagnostics-extension-overview.md) – odesílá data do Azure monitor metrik (jenom Windows), Azure Event Hubs a Azure Storage.
- [Agent telegraf](collect-custom-metrics-linux-telegraf.md) – odesílá data do Azure monitor metrik (jenom Linux).

Kromě toho, že se tato funkce konsoliduje do jednoho agenta, přináší agent Azure Monitor v rámci stávajících agentů následující výhody:

- Rozsah monitorování. Centrálně konfigurujte kolekci pro různé sady dat z různých sad virtuálních počítačů.  
- Linux více domovských stránek: odesílat data z virtuálních počítačů se systémem Linux do několika pracovních prostorů.
- Filtrování událostí systému Windows: pomocí dotazů XPATH můžete filtrovat, které události systému Windows jsou shromažďovány.
- Vylepšená správa rozšíření: Agent Azure Monitor používá novou metodu pro zpracování rozšiřitelnosti, která je lépe transparentní a ovladatelné než sady Management Pack a moduly plug-in Linux v aktuálních agentech Log Analytics.

### <a name="changes-in-data-collection"></a>Změny v kolekci dat
Metody pro definování shromažďování dat pro stávající agenty se liší od sebe a každá z nich má problémy řešené agentem Azure Monitor.

- Agent Log Analytics získá svoji konfiguraci z pracovního prostoru Log Analytics. To je snadné – centrálně konfigurujte, ale obtížně definujte nezávislé definice pro různé virtuální počítače. Může odesílat data pouze do pracovního prostoru Log Analytics.

- Diagnostické rozšíření má konfiguraci pro každý virtuální počítač. Díky tomu je snadné definovat nezávislé definice pro různé virtuální počítače, ale obtížné centrálně spravovat. Data může posílat jenom Azure Monitor metriky, Azure Event Hubs nebo Azure Storage. Pro agenty Linux se k posílání dat Azure Monitorch metrik vyžaduje agent Open Source telegraf.

Agent Azure Monitor používá [pravidla shromažďování dat (DCR)](data-collection-rule-overview.md) ke konfiguraci dat, která se mají shromažďovat od každého agenta. Pravidla shromažďování dat umožňují spravovatelnost nastavení kolekce ve velkém měřítku a zároveň přitom povolují jedinečné a vymezené konfigurace pro podmnožiny počítačů. Jsou nezávislé na pracovním prostoru a nezávisle na virtuálním počítači, což umožňuje jejich definování a jejich použití v různých počítačích a prostředích. Viz téma [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Aktuální omezení
Během veřejné verze Preview agenta Azure Monitor platí následující omezení:

- Agent Azure Monitor nepodporuje řešení a přehledy, jako je Azure Monitor pro virtuální počítače a Azure Security Center. Jediným scénářem, který se aktuálně podporuje, je shromažďování dat pomocí pravidel shromažďování dat, která nakonfigurujete. 
- Pravidla shromažďování dat musí být vytvořena ve stejné oblasti jako kterýkoli Log Analytics pracovní prostor, který se používá jako cíl.
- V tuto chvíli se podporují jenom virtuální počítače Azure. Místní virtuální počítače, sady škálování virtuálních počítačů, ARC pro servery, služba Azure Kubernetes a další typy výpočetních prostředků se aktuálně nepodporují.
- Virtuální počítač musí mít přístup k následujícím koncovým bodům HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Koexistence s ostatními agenty
Agent Azure Monitor může společně s existujícími agenty fungovat, abyste mohli nadále používat stávající funkce během hodnocení nebo migrace. To je důležité hlavně z důvodu omezení ve verzi Public Preview v části Podpora stávajících řešení. Měli byste být opatrní při shromažďování duplicitních dat, protože by to mohlo zkosit výsledky dotazu a mít za následek další poplatky za přijímání a uchovávání dat.

Azure Monitor pro virtuální počítače například používá agenta Log Analytics k posílání údajů o výkonu do Log Analyticsho pracovního prostoru. Můžete také nakonfigurovat pracovní prostor pro shromažďování událostí systému Windows a událostí protokolu syslog od agentů. Pokud nainstalujete agenta Azure Monitor a vytvoříte pravidlo shromažďování dat pro tyto stejné události a data o výkonu, bude výsledkem duplicitní data.


## <a name="costs"></a>Náklady
Pro agenta Azure Monitor se neúčtují žádné náklady, ale za ingestování dat vám můžou být účtovány poplatky. Podrobnosti o Log Analytics Shromažďování a uchovávání dat a zákaznických metrikách najdete v článku o [cenách Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Zdroje dat a cíle
V následující tabulce jsou uvedeny typy dat, které můžete v současnosti shromažďovat s agentem Azure Monitor pomocí pravidel shromažďování dat a kam můžete tato data odeslat. Podívejte se na téma [co je monitorované pomocí Azure monitor?](../monitor-reference.md) v seznamu přehledů, řešení a dalších řešení, která používají agenta Azure monitor ke shromažďování dalších typů dat.


Agent Azure Monitor odesílá data do Azure Monitor metrik nebo Log Analytics pracovní prostor podporující protokoly Azure Monitor.

| Zdroj dat | Cíle | Popis |
|:---|:---|:---|
| Výkon        | Azure Monitor metriky<br>Pracovní prostor služby Log Analytics | Číselné hodnoty, které měří výkon různých aspektů operačního systému a zatížení. |
| Protokoly událostí systému Windows | Pracovní prostor služby Log Analytics | Informace odesílané systému protokolování událostí systému Windows. |
| Syslog             | Pracovní prostor služby Log Analytics | Informace odesílané systému protokolování událostí pro Linux. |


## <a name="supported-operating-systems"></a>Podporované operační systémy
Agent Azure Monitor aktuálně podporuje následující operační systémy.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7
  - SLES 11, 12, 15
  - Ubuntu 14,04 LTS, 16,04 LTS, 18,04 LTS

> [!IMPORTANT]
> <sup>1</sup> Pro tyto distribuce, aby odesílaly data syslog, je nutné službu rsyslog restartovat jednou po instalaci agenta.


## <a name="security"></a>Zabezpečení
Agent Azure Monitor nepotřebuje žádné klíče, ale místo toho vyžaduje [spravovanou identitu přiřazenou systémem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Před nasazením agenta musíte mít na každém virtuálním počítači povolenou spravovanou identitu přiřazenou systémem.

## <a name="networking"></a>Sítě
Agent Azure Monitor podporuje značky služeb Azure (vyžadují se značky AzureMonitor a AzureResourceManager), ale ještě nepracuje s Azure Monitormi obory privátních odkazů nebo přímými proxy servery.

## <a name="install-the-azure-monitor-agent"></a>Instalace agenta Azure Monitor
Agent Azure Monitor se implementuje jako [rozšíření virtuálního počítače Azure](../../virtual-machines/extensions/overview.md) s podrobnostmi v následující tabulce. 

| Vlastnost | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 1.5 |

Nainstalujte agenta Azure Monitor pomocí kterékoli z metod, jak nainstalovat agenty virtuálních počítačů, včetně těchto postupů: PowerShell nebo CLI. Případně můžete agenta nainstalovat a nakonfigurovat shromažďování dat na virtuálních počítačích v předplatném Azure pomocí portálu s postupem popsaným v části [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Další kroky

- [Vytvořte pravidlo shromažďování dat](data-collection-rule-azure-monitor-agent.md) pro shromažďování dat z agenta a jeho odeslání na Azure monitor.
