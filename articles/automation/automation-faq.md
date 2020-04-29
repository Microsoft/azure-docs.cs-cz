---
title: Nejčastější dotazy k Azure Automation | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405967"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation nejčastějších dotazech

Toto je seznam nejčastějších dotazů k Azure Automation. Pokud máte další dotazy týkající se jeho schopností, navštivte diskuzní fórum a publikujte své dotazy. V případě častého dotazu přidáme Tento článek do tohoto článku, aby ho bylo možné rychle a snadno najít.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Řešení Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Můžu zabránit neočekávaným upgradům na úrovni operačního systému?

U některých variant systému Linux, například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může způsobit Update Management spuštění, ve kterém se číslo verze operačního systému změní. Vzhledem k tomu, že Update Management používá stejné metody k aktualizaci balíčků, které správce používá místně na počítači se systémem Linux, je toto chování záměrné.

Abyste se vyhnuli aktualizace verze operačního systému pomocí Update Management nasazení, použijte funkci **vyloučení** .

V Red Hat Enterprise Linux název balíčku, který se má vyloučit `redhat-release-server.x86_64`, je.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Proč nejsou důležité nebo se nepoužívají aktualizace zabezpečení?

Když nasadíte aktualizace na počítač se systémem Linux, můžete vybrat možnost klasifikace aktualizací. Tato možnost filtruje aktualizace, které splňují zadaná kritéria. Tento filtr se použije lokálně na počítači při nasazení aktualizace.

Vzhledem k tomu, že Update Management provádí obohacení aktualizace v cloudu, můžete označit některé aktualizace v Update Management, protože mají dopad na zabezpečení, a to i v případě, že místní počítač tyto informace nemá. Pokud na počítači se systémem Linux použijete důležité aktualizace, může dojít k aktualizacím, které nejsou označeny jako bezpečnostní dopad na daný počítač, a proto nejsou použity. Update Management ale přesto může tento počítač ohlásit jako nevyhovující, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje na verzích RTM CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace, aby bylo zajištěno, že budou aktualizace aplikovány. V případě SUSE mohou při výběru pouze **dalších** aktualizací, které jsou součástí klasifikace, dojít k instalaci některých dalších aktualizací zabezpečení, pokud jsou nejprve požadovány aktualizace zabezpečení související s zypperu (Správce balíčků) nebo její závislosti. Toto chování je omezení zypperu. V některých případech může být nutné znovu spustit nasazení aktualizace a pak ověřit nasazení prostřednictvím protokolu aktualizace.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Můžu v klientech Azure nasazovat aktualizace?

Pokud máte počítače, které vyžadují opravy v jiném tenantovi Azure pro Update Management, musíte k jejich naplánování použít následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) s `ForUpdateConfiguration` parametrem zadaným. Můžete použít rutinu [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) a předat do `NonAzureComputer` parametru počítače v druhém tenantovi. Následující příklad ukazuje, jak to provést.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Další kroky

Pokud tady na vaši otázku neodpovíte, můžete získat další otázky a odpovědi v následujících zdrojích.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Fórum pro názory](https://feedback.azure.com/forums/905242-update-management)
