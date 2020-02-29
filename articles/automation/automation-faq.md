---
title: Nejčastější dotazy k Azure Automation | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925810"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation nejčastějších dotazech

Toto je seznam nejčastějších dotazů k Azure Automation. Pokud máte další dotazy týkající se jeho schopností, navštivte diskuzní fórum a publikujte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="update-management-solution"></a>Řešení Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Můžu zabránit neočekávaným upgradům na úrovni operačního systému?

U některých variant systému Linux, například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může vést k tomu, Update Management spustí, kde se změní číslo verze operačního systému. Vzhledem k tomu, že Update Management používá stejné metody k aktualizaci balíčků, které by správce použil místně na počítači se systémem Linux, je toto chování záměrné.

Abyste se vyhnuli aktualizace verze operačního systému pomocí Update Management nasazení, použijte funkci **vyloučení** .

V Red Hat Enterprise Linux název balíčku, který se má vyloučit, je RedHat-Release-Server. x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Proč nejsou důležité nebo se nepoužívají aktualizace zabezpečení?

Když nasadíte aktualizace na počítač se systémem Linux, můžete vybrat možnost klasifikace aktualizací. Tato možnost filtruje aktualizace, které jsou aplikovány na počítač, který splňuje zadaná kritéria. Tento filtr se použije lokálně na počítači při nasazení aktualizace.

Vzhledem k tomu, že Update Management provádí obohacení aktualizace v cloudu, můžou být některé aktualizace příznakem v Update Management, protože mají dopad na zabezpečení, a to i v případě, že místní počítač tyto informace nemá. V důsledku toho, pokud použijete důležité aktualizace na počítači se systémem Linux, může dojít k aktualizacím, které nejsou označeny jako bezpečnostní dopad na daný počítač, a proto se aktualizace nepoužijí. Update Management ale přesto může tento počítač ohlásit jako nekompatibilní, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje na verzích RTM CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace, aby bylo zajištěno, že budou aktualizace aplikovány. V případě SUSE je třeba vybrat *pouze* **jiné aktualizace** , protože klasifikace může způsobit instalaci některých aktualizací zabezpečení, pokud jsou nejprve požadovány aktualizace zabezpečení související s zypperu (Správce balíčků) nebo její závislosti. Toto chování je omezení zypperu. V některých případech může být nutné znovu spustit nasazení aktualizace. Chcete-li ověřit, zkontrolujte protokol aktualizace.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Můžu v klientech Azure nasazovat aktualizace?

Pokud máte počítače v jiném tenantovi Azure, které vám Update Management, že je potřeba opravit, musíte k jejich naplánování použít následující alternativní řešení. Pomocí rutiny [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s přepínačem `-ForUpdate` můžete vytvořit plán a použít rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat počítače do jiného tenanta do parametru `-NonAzureComputer`. Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Další kroky

Pokud tady na vaši otázku neodpovíte, můžete si další otázky a odpovědi vykázat na následující fórum.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Pokud chcete získat obecnou zpětnou vazbu k řešení Update Management, navštivte prosím [fórum pro názory](https://feedback.azure.com/forums/905242-update-management).