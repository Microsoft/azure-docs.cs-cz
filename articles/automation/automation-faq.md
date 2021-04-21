---
title: Nejčastější dotazy k Azure Automation | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834223"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation nejčastějších dotazech

Toto je seznam nejčastějších dotazů k Azure Automation. Pokud máte další dotazy týkající se jeho schopností, navštivte diskuzní fórum a publikujte své dotazy. V případě častého dotazu přidáme Tento článek do tohoto článku, aby bylo možné ho rychle a snadno najít.

## <a name="update-management"></a>Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Můžu zabránit neočekávaným upgradům na úrovni operačního systému?

U některých variant systému Linux, například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může způsobit Update Management spuštění, ve kterém se číslo verze operačního systému změní. Vzhledem k tomu, že Update Management používá stejné metody k aktualizaci balíčků, které správce používá místně na počítači se systémem Linux, je toto chování záměrné.

Abyste se vyhnuli aktualizace verze operačního systému pomocí Update Management nasazení, použijte funkci **vyloučení** .

V Red Hat Enterprise Linux název balíčku, který se má vyloučit, je `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Proč nejsou důležité nebo se nepoužívají aktualizace zabezpečení?

Když nasadíte aktualizace na počítač se systémem Linux, můžete vybrat možnost klasifikace aktualizací. Tato možnost filtruje aktualizace, které splňují zadaná kritéria. Tento filtr se použije lokálně na počítači při nasazení aktualizace.

Vzhledem k tomu, že Update Management provádí obohacení aktualizace v cloudu, můžete označit některé aktualizace v Update Management, protože mají dopad na zabezpečení, a to i v případě, že místní počítač tyto informace nemá. Pokud na počítači se systémem Linux použijete důležité aktualizace, může dojít k aktualizacím, které nejsou označeny jako bezpečnostní dopad na daný počítač, a proto nejsou použity. Update Management ale přesto může tento počítač ohlásit jako nevyhovující, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje na verzích RTM CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace, aby bylo zajištěno, že budou aktualizace aplikovány. V případě SUSE můžete vybrat jenom **jiné aktualizace** , protože klasifikace může nainstalovat některé další aktualizace zabezpečení, pokud se vztahují k zypperu (Správce balíčků), nebo pokud se její závislosti vyžadují jako první. Toto chování je omezení zypperu. V některých případech může být nutné znovu spustit nasazení aktualizace a pak ověřit nasazení prostřednictvím protokolu aktualizace.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Můžu v klientech Azure nasazovat aktualizace?

Pokud máte počítače, které vyžadují opravy v jiném tenantovi Azure pro Update Management, musíte k jejich naplánování použít následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) s `ForUpdateConfiguration` parametrem zadaným. Můžete použít rutinu [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) a předat do parametru počítače v druhém tenantovi `NonAzureComputer` . Následující příklad ukazuje, jak to provést.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automatizace procesů – Runbooky v Pythonu

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Která verze Pythonu 3 je v Azure Automation podporovaná?

Pro cloudové úlohy je Python 3,8 podporován. Skripty a balíčky z jakékoli 3. x verze mohou fungovat, pokud je kód kompatibilní napříč různými verzemi.

Pro hybridní úlohy na hybridních pracovních procesech Windows Hybrid Runbook můžete zvolit instalaci libovolné verze 3. x, kterou chcete použít. Pro hybridní úlohy na hybridních pracovních procesech Runbooku pro Linux závisí na verzi Pythonu 3 nainstalované na počítači, aby bylo možné spouštět DSC OMSConfig a Linux Hybrid Worker. Doporučujeme nainstalovat verzi 3,6. různé verze by však měly fungovat i v případě, že se nevyskytnou žádné zásadní změny signatury metody nebo kontraktů mezi verzemi Pythonu 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Můžou sady Runbook Python 2 a Python 3 běžet ve stejném účtu Automation?

Ano, neexistuje žádné omezení pro použití runbooků Python 2 a Python 3 ve stejném účtu Automation.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Jaký je plán pro migraci stávajících sad Runbook a balíčků Python 2 do Pythonu 3?

Azure Automation nemá v úmyslu migrovat sady Runbook a balíčky Python 2 na Python 3. Tuto migraci budete muset provést sami. Stávající a nové Runbooky a balíčky Python 2 budou fungovat i nadále.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Jaké jsou balíčky podporované ve výchozím nastavení v prostředí Python 3?

Azure Package 4.0.0 se ve výchozím nastavení instaluje v automatizačním prostředí Python 3. Můžete ručně naimportovat vyšší verzi balíčku Azure a přepsat tak výchozí verzi.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Co když spustím Runbook Python 3, který odkazuje na balíček Python 2 nebo naopak?

Python 2 a Python 3 mají různá prováděcí prostředí. I když je spuštěný Runbook Python 2, lze importovat pouze balíčky Python 2 a podobně jako Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Návody rozlišovat sady Runbook 2 a runbooky a balíčky python 3?

Python 3 je nová definice sady Runbook, která rozlišuje mezi Runbooky Python 2 a Python 3. Podobně se zavádí jiný druh balíčku pro balíčky python 3.

## <a name="next-steps"></a>Další kroky

Pokud tady na vaši otázku neodpovíte, můžete získat další otázky a odpovědi v následujících zdrojích.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Fórum pro názory](https://feedback.azure.com/forums/905242-update-management)
