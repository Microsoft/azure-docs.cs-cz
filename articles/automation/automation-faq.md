---
title: Nejčastější dotazy k automatizaci Azure | Dokumenty společnosti Microsoft
description: Odpovědi na nejčastější dotazy týkající se Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925810"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation nejčastější dotazy

Tento nejčastější dotazy k Microsoftu je seznam často kladených otázek týkajících se Azure Automation. Máte-li jakékoli další dotazy týkající se jeho schopností, přejděte do diskusního fóra a pošlete své dotazy. Když je otázka často kladena, přidáme ji do tohoto článku, aby ji bylo možné rychle a snadno najít.

## <a name="update-management-solution"></a>Řešení Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Mohu zabránit neočekávaným upgradům na úrovni operačního systému?

U některých variant Linuxu, jako je Například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může vést ke spuštění správy aktualizací, kde se změní číslo verze operačního systému. Vzhledem k tomu, že správa aktualizací používá stejné metody k aktualizaci balíčků, které by správce použil místně v počítači s Linuxem, je toto chování úmyslné.

Chcete-li se vyhnout aktualizaci verze operačního systému prostřednictvím nasazení správy aktualizací, použijte funkci **Vyloučení.**

V Red Hat Enterprise Linux je název balíčku, který má být vyloučen, redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Proč nejsou použity důležité aktualizace nebo aktualizace zabezpečení?

Při nasazování aktualizací do počítače s Linuxem můžete vybrat klasifikace aktualizací. Tato možnost filtruje aktualizace použité v počítači, které splňují zadaná kritéria. Tento filtr se použije místně v počítači při nasazení aktualizace.

Vzhledem k tomu, že správa aktualizací provádí obohacování aktualizací v cloudu, některé aktualizace mohou být ve správě aktualizací označeny jako aktualizace, které mají dopad na zabezpečení, i když místní počítač tyto informace nemá. V důsledku toho pokud použijete důležité aktualizace pro počítač s Linuxem, mohou existovat aktualizace, které nejsou označeny jako aktualizace, které mají dopad na zabezpečení tohoto počítače, a proto nejsou použity aktualizace. Správa aktualizací však může stále hlásit tento počítač jako nekompatibilní, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje u rtm verzí CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace a ujistěte se, že jsou použity aktualizace. Pro SUSE, výběr *pouze* **další aktualizace** jako klasifikace může způsobit některé aktualizace zabezpečení také nainstalovat, pokud aktualizace zabezpečení týkající se zypper (správce balíčků) nebo jeho závislosti jsou požadovány jako první. Toto chování je omezení zypper. V některých případech může být nutné znovu spustit nasazení aktualizace. Chcete-li ověřit, zkontrolujte protokol aktualizace.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Můžu nasadit aktualizace mezi klienty Azure?

Pokud máte počítače v jiném tenantovi Azure, které se hlásí ke správě aktualizací, které potřebujete opravit, budete muset použít následující řešení, abyste je naplánovali. Můžete použít [Rutina New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s `-ForUpdate` přepínačem k vytvoření plánu a použít rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat `-NonAzureComputer` počítače v druhém tenantovi parametru. Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Další kroky

Pokud zde není vaše otázka zodpovězena, můžete se obrátit na následující fórum pro další otázky a odpovědi.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Obecnou zpětnou vazbu k řešení správy aktualizací naleznete na [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/905242-update-management).