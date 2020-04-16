---
title: Nejčastější dotazy k automatizaci Azure | Dokumenty společnosti Microsoft
description: Odpovědi na nejčastější dotazy týkající se Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405967"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation nejčastější dotazy

Tento nejčastější dotazy k Microsoftu je seznam často kladených otázek týkajících se Azure Automation. Máte-li jakékoli další dotazy týkající se jeho schopností, přejděte do diskusního fóra a pošlete své dotazy. Když je otázka často kladena, přidáme ji do tohoto článku, aby ji bylo možné rychle a snadno najít.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Řešení Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Mohu zabránit neočekávaným upgradům na úrovni operačního systému?

U některých variant Linuxu, jako je Například Red Hat Enterprise Linux, může dojít k upgradům na úrovni operačního systému prostřednictvím balíčků. To může vést ke spuštění správy aktualizací, ve kterém se změní číslo verze operačního systému. Vzhledem k tomu, že správa aktualizací používá stejné metody k aktualizaci balíčků, které správce používá místně v počítači s Linuxem, je toto chování úmyslné.

Chcete-li se vyhnout aktualizaci verze operačního systému prostřednictvím nasazení správy aktualizací, použijte funkci **Vyloučení.**

V Red Hat Enterprise Linux je `redhat-release-server.x86_64`název balíčku, který chcete vyloučit .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Proč nejsou použity důležité aktualizace nebo aktualizace zabezpečení?

Při nasazování aktualizací do počítače s Linuxem můžete vybrat klasifikace aktualizací. Tato možnost filtruje aktualizace, které splňují zadaná kritéria. Tento filtr se použije místně v počítači při nasazení aktualizace.

Vzhledem k tomu, že správa aktualizací provádí obohacování aktualizací v cloudu, můžete označit některé aktualizace ve správě aktualizací jako aktualizace s dopadem na zabezpečení, i když místní počítač tyto informace nemá. Pokud použijete důležité aktualizace pro počítač s Linuxem, mohou existovat aktualizace, které nejsou označeny jako s dopadem na zabezpečení tohoto počítače, a proto nejsou použity. Správa aktualizací však může stále hlásit tento počítač jako nekompatibilní, protože obsahuje další informace o příslušné aktualizaci.

Nasazení aktualizací podle klasifikace aktualizací nefunguje u rtm verzí CentOS. Chcete-li správně nasadit aktualizace pro CentOS, vyberte všechny klasifikace a ujistěte se, že jsou použity aktualizace. Pro SUSE, výběr pouze **další aktualizace** jako klasifikace může způsobit některé další aktualizace zabezpečení, které mají být nainstalovány, pokud aktualizace zabezpečení týkající se zypper (správce balíčků) nebo jeho závislosti jsou požadovány jako první. Toto chování je omezení zypper. V některých případech může být nutné znovu spustit nasazení aktualizace a potom ověřit nasazení prostřednictvím protokolu aktualizace.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Můžu nasadit aktualizace mezi klienty Azure?

Pokud máte počítače, které potřebují opravy v jiném azure tenanta vytváření sestav na update management, musíte použít následující řešení, aby si je naplánováno. Rutinu [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) s parametrem `ForUpdateConfiguration` zadaným můžete použít k vytvoření plánu. Můžete použít rutinu [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) a předat počítače v druhém `NonAzureComputer` klientovi parametru. Následující příklad ukazuje, jak to provést.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Další kroky

Pokud zde vaše otázka není zodpovězena, můžete se obrátit na následující zdroje, kde naleznete další otázky a odpovědi.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Fórum pro názory](https://feedback.azure.com/forums/905242-update-management)
