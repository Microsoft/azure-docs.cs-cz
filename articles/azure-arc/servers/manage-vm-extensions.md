---
title: Správa rozšíření virtuálních počítačů pomocí serverů s podporou ARC Azure
description: Servery s podporou ARC Azure můžou spravovat nasazení rozšíření virtuálních počítačů, která poskytují konfiguraci po nasazení a úlohy automatizace s virtuálními počítači mimo Azure.
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: b39149eb7ac572ac3bd50bb6303f28d2340f387d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580862"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Správa rozšíření virtuálních počítačů pomocí serverů s podporou Azure Arc

Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. Pokud třeba virtuální počítač vyžaduje instalaci softwaru, antivirovou ochranu nebo spuštění skriptu, můžete použít rozšíření virtuálního počítače.

Servery s podporou ARC Azure umožňují nasadit rozšíření virtuálních počítačů Azure na virtuální počítače, které nejsou v Azure a Linux, a zjednoduší správu hybridního počítače prostřednictvím jejich životního cyklu. Rozšíření virtuálních počítačů je možné spravovat pomocí následujících metod na hybridních počítačích nebo serverech spravovaných servery s podporou ARC:

- [Azure Portal](manage-vm-extensions-portal.md)
- Rozhraní příkazového [řádku Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Šablony Azure správce prostředků](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Klíčové výhody

Podpora rozšíření virtuálních počítačů serverů s podporou ARC Azure nabízí následující klíčové výhody:

- [Konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md) slouží k centrálnímu ukládání konfigurací a údržbě požadovaného stavu hybridních připojených počítačů povolených prostřednictvím rozšíření virtuálního počítače DSC.

- Shromažďovat data protokolu pro analýzu s [protokoly v Azure monitor](../../azure-monitor/logs/data-platform-logs.md) povolené prostřednictvím rozšíření virtuálního počítače agenta Log Analytics. To je užitečné při komplexní analýze napříč daty z různých druhů zdrojů.

- Díky [Azure monitor pro virtuální počítače](../../azure-monitor/vm/vminsights-overview.md)analyzuje výkon virtuálních počítačů s Windows a Linux a monitoruje jejich procesy a závislosti na dalších prostředcích a externích procesech. Toho dosáhnete tím, že povolíte rozšíření Log Analytics agenta a rozšíření virtuálního počítače agenta závislostí.

- Stahovat a spouštět skripty na hybridních připojených počítačích pomocí rozšíření vlastních skriptů. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy.

- Automatické aktualizace certifikátů uložených v [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Dostupnost

Funkce rozšíření virtuálních počítačů jsou k dispozici pouze v seznamu [podporovaných oblastí](overview.md#supported-regions). Ujistěte se, že jste počítač připojili do jedné z těchto oblastí.

## <a name="extensions"></a>Rozšíření

V této verzi podporujeme následující rozšíření virtuálních počítačů na počítačích s Windows a Linux.

Další informace o balíčku agenta připojeného počítače Azure a podrobnostech o komponentě agenta rozšíření najdete v tématu [Přehled agenta](agent-overview.md#agent-component-details).

### <a name="windows-extensions"></a>Rozšíření Windows

|Linka |Publisher |Typ |Další informace |
|----------|----------|-----|-----------------------|
|Integrovaný skener ohrožení zabezpečení v Azure Defenderu |Qualys |WindowsAgent.AzureSecurityCenter |[Integrované řešení posouzení ohrožení zabezpečení v Azure Defenderu pro Azure a hybridní počítače](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozšíření vlastních skriptů |Microsoft.Compute | CustomScriptExtension |[Rozšíření vlastních skriptů pro Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|PowerShell DSC |Microsoft. PowerShell |DSC |[Rozšíření Windows PowerShell DSC](../../virtual-machines/extensions/dsc-windows.md)|
|Agent Log Analytics |Microsoft. EnterpriseCloud. Monitoring |MicrosoftMonitoringAgent |[Log Analytics rozšíření virtuálního počítače pro Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor pro virtuální počítače (přehledy) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentWindows | [Rozšíření pro virtuální počítače s agentem závislosti pro Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault synchronizace certifikátů | Microsoft. Azure. Key. trezor |KeyVaultForWindows | [Key Vault rozšíření virtuálního počítače pro Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agent Azure Monitoru |Microsoft. Azure. monitor |AzureMonitorWindowsAgent |[Instalace agenta Azure Monitor (Preview)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Rozšíření pro Linux

|Linka |Publisher |Typ |Další informace |
|----------|----------|-----|-----------------------|
|Integrovaný skener ohrožení zabezpečení v Azure Defenderu |Qualys |LinuxAgent.AzureSecurityCenter |[Integrované řešení posouzení ohrožení zabezpečení v Azure Defenderu pro Azure a hybridní počítače](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Rozšíření vlastních skriptů |Microsoft. Azure. Extensions |CustomScript |[Rozšíření vlastních skriptů pro Linux verze 2](../../virtual-machines/extensions/custom-script-linux.md) |
|PowerShell DSC |Microsoft. OSTCExtensions |DSCForLinux |[Rozšíření PowerShell DSC pro Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent Log Analytics |Microsoft. EnterpriseCloud. Monitoring |OmsAgentForLinux |[Rozšíření virtuálního počítače s Log Analytics pro Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor pro virtuální počítače (přehledy) |Microsoft. Azure. Monitoring. DependencyAgent |DependencyAgentLinux |[Rozšíření pro virtuální počítače s agentem závislosti pro Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault synchronizace certifikátů | Microsoft. Azure. Key. trezor |KeyVaultForLinux | [Key Vault rozšíření virtuálního počítače pro Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agent Azure Monitoru |Microsoft. Azure. monitor |AzureMonitorLinuxAgent |[Instalace agenta Azure Monitor (Preview)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na následujících poskytovatelích prostředků Azure v rámci vašeho předplatného:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Pokud ještě nejsou zaregistrované, postupujte podle kroků v části [registrace poskytovatelů prostředků Azure](agent-overview.md#register-azure-resource-providers).

Nezapomeňte si projít dokumentaci pro každé rozšíření virtuálních počítačů, na které odkazuje předchozí tabulka, a zjistěte, jestli obsahuje nějaké požadavky na síť nebo systém. To vám může pomáhat při potížích s připojením ke službě Azure nebo funkci, která spoléhá na toto rozšíření virtuálního počítače.

### <a name="log-analytics-vm-extension"></a>Rozšíření virtuálního počítače Log Analytics

Rozšíření virtuálního počítače agenta Log Analytics pro Linux vyžaduje Python 2. x na cílovém počítači. 

### <a name="azure-key-vault-vm-extension-preview"></a>Rozšíření virtuálního počítače Azure Key Vault (Preview)

Rozšíření virtuálního počítače Key Vault (Preview) nepodporuje následující operační systémy Linux:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Nasazení rozšíření virtuálního počítače Key Vault (Preview) se podporuje jenom pomocí:

- Azure CLI
- Azure PowerShell
- Šablona Azure Resource Manageru

Než rozšíření nasadíte, musíte provést následující:

1. [Vytvořte trezor a certifikát](../../key-vault/certificates/quick-create-portal.md) (podepsaný držitelem nebo importovat).

2. Udělte serveru s povolenou službou Azure ARC přístup k tajnému kódu certifikátu. Pokud používáte [verzi Preview služby RBAC](../../key-vault/general/rbac-guide.md), vyhledejte název prostředku ARC Azure a přiřaďte mu roli **Key Vault tajných klíčů uživatele (Preview)** . Pokud používáte [zásady přístupu Key Vault](../../key-vault/general/assign-access-policy-portal.md), přiřaďte k identitě přiřazené k systému prostředku ARC Azure oprávnění **získat** tajný klíč.

### <a name="connected-machine-agent"></a>Agent připojeného počítače

Ověřte, že počítač odpovídá [podporovaným verzím](agent-overview.md#supported-operating-systems) operačního systému Windows a Linux pro agenta připojeného počítače Azure.

Minimální verze agenta připojeného počítače, který je podporovaný touto funkcí v systému Windows a Linux, je verze 1,0.

Pokud chcete upgradovat počítač na požadovanou verzi agenta, přečtěte si téma [Upgrade agenta](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Další kroky

Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí [Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), ze [Azure Portal](manage-vm-extensions-portal.md)nebo [šablon Azure Resource Manager](manage-vm-extensions-template.md).
