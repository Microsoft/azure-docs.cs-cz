---
title: Poradce při potížích se správou aktualizací Azure
description: Zjistěte, jak řešit a řešit problémy s řešením správy aktualizací v Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c9ff05591c98fda8be39e32f26da484f56e0831b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984619"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží se správou aktualizací

Tento článek popisuje řešení problémů, které se mohou sejít při používání správy aktualizací.

Existuje poradce při potížích s agentem pro agenta hybridního pracovníka k určení základního problému. Další informace o poradci při potížích najdete [v tématu Poradce při potížích s agentem aktualizace](update-agent-issues.md). Pro všechny ostatní problémy použijte následující pokyny pro řešení potíží.

Pokud při připojování řešení na virtuálním počítači (VM) zjistíte **problémy,** zkontrolujte protokol Nástroje pro řízení **provozu** v části Protokoly aplikací a služeb v místním počítači. Vyhledejte události s ID události 4502 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`a podrobnosti o události, které obsahují .

V následující části jsou zvýrazněny konkrétní chybové zprávy a možná řešení pro každou z nich. Další problémy s přizapisováním najdete [v tématu Poradce při potížích s řešením .](onboarding.md)

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scénář: Zobrazí se chyba "Nepodařilo se povolit řešení aktualizace"

### <a name="issue"></a>Problém

Při pokusu o povolení řešení správy aktualizací v účtu automatizace se zobrazí následující chyba:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Požadavky na síťovou bránu firewall pro agenta Analýzy protokolů nemusí být správně nakonfigurovány. Tato situace může způsobit selhání agenta při řešení adres URL DNS.

* Cílení na řešení je nesprávně nakonfigurováno a počítač nepřijímá aktualizace podle očekávání.

* Můžete si také všimnout, že `Non-compliant` zařízení zobrazuje stav v části **Dodržování předpisů**. Agent **Desktop Analytics** současně agent Desktop `Disconnected`Analytics informuje o agentovi jako .

### <a name="resolution"></a>Řešení

* V závislosti na operačním systému spusťte poradce při potížích s [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linuxem.](update-agent-issues-linux.md#troubleshoot-offline)

* Přejděte na [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) a zjistěte, které adresy a porty musí být povoleny, aby správa aktualizací fungovala.  

* Přejděte na [plánování sítě,](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) kde se dozvíte, které adresy a porty musí být povoleny, aby agent Analýzy protokolů fungoval.

* Zkontrolujte problémy s konfigurací oboru. [Konfigurace oboru](../automation-onboard-solutions-from-automation-account.md#scope-configuration) určuje, které počítače jsou pro řešení konfigurovány. Pokud se váš počítač zobrazuje ve vašem pracovním prostoru, ale ne na portálu pro správu aktualizací**, budete muset nastavit konfiguraci oboru tak, aby cílila na počítače. Další informace o konfiguraci oboru naleznete [v tématu Palubní počítače v pracovním prostoru](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Odeberte konfiguraci pracovního procesu podle kroků v [části Odstranění hybridního pracovníka runbooku](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scénář: Nahrazená aktualizace označená jako chybějící ve správě aktualizací

### <a name="issue"></a>Problém

Staré aktualizace se zobrazují pro účet Automatizace jako chybějící, i když byly nahrazeny. Nahrazená aktualizace je aktualizace, kterou nemusíte instalovat, protože je k dispozici pozdější aktualizace, která opravuje stejnou chybu zabezpečení. Správa aktualizací ignoruje nahrazenou aktualizaci a činí ji použitelnou ve prospěch nahrazující aktualizace. Informace o souvisejícím problému naleznete v tématu [Aktualizace je nahrazena](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Příčina

Nahrazené aktualizace nejsou správně označeny jako odmítnuté, takže je nelze považovat za nepoužitelné.

### <a name="resolution"></a>Řešení

Pokud se nahrazená aktualizace stane 100 % nepoužitelnou, měli byste `Declined`změnit stav schválení této aktualizace na . Změna stavu schválení pro všechny aktualizace:

1. V účtu Automatizace vyberte **Aktualizovat správu,** chcete-li zobrazit stav počítače. Viz [Zobrazit hodnocení aktualizací](../manage-update-multi.md#view-an-update-assessment).

2. Zkontrolujte nahrazenou aktualizaci, abyste se ujistili, že je 100 procent nepoužitelná. 

3. Označte aktualizaci jako odmítnutou, pokud nemáte dotaz týkající se aktualizace. 

4. Vyberte **Počítače** a ve sloupci **Dodržování předpisů** vynuťte opětovné prohledávádodržování předpisů. Viz [Správa aktualizací pro více počítačů](../manage-update-multi.md).

5. Opakujte výše uvedené kroky pro další nahrazené aktualizace.

6. Spusťte průvodce vyčištěním a odstraňte soubory z odmítnutých aktualizací. 

7. V systému Windows Server Update Services (WSUS) ručně vyčistěte všechny nahrazené aktualizace a aktualizujte infrastrukturu.

8. Opakujte tento postup pravidelně opravit problém se zobrazením a minimalizovat množství místa na disku používané pro správu aktualizací.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scénář: Počítače se nezobrazují na portálu v části Správa aktualizací

### <a name="issue"></a>Problém

Vaše stroje mají následující příznaky:

* Váš počítač `Not configured` se zobrazí ze zobrazení správy aktualizací virtuálního počítače.

* Vaše počítače chybí v zobrazení správy aktualizací vašeho účtu Azure Automation.

* Máte počítače, které `Not assessed` se zobrazují v části **Compliance**. Data prezenčního signálu se však zobrazují v protokolech Azure Monitor pro pracovníka hybridního runbooku, ale ne pro správu aktualizací.

### <a name="cause"></a>Příčina

Tento problém může být způsoben problémy s místní konfigurací nebo nesprávně nakonfigurovanou konfigurací oboru. Možné specifické příčiny jsou:

* Možná budete muset znovu zaregistrovat a znovu nainstalovat hybridní runbook worker.

* Je možné, že jste v pracovním prostoru definovali kvótu, která byla dosažena a která brání dalšímu ukládání dat.

### <a name="resolution"></a>Řešení

1. V závislosti na operačním systému spusťte poradce při potížích s [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linuxem.](update-agent-issues-linux.md#troubleshoot-offline)

2. Ujistěte se, že váš počítač je hlášení do správného pracovního prostoru. Pokyny k ověření tohoto aspektu naleznete v [tématu Ověření připojení agenta k log analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Také se ujistěte, že tento pracovní prostor je propojený s vaším účtem Azure Automation. Chcete-li to potvrdit, přejděte na účet Automation a v části **Související zdroje**vyberte Propojený **pracovní prostor** .

3. Ujistěte se, že se počítače zobrazují v pracovním prostoru Log Analytics propojeném s vaším účtem Automation. Spusťte následující dotaz v pracovním prostoru Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Pokud počítač ve výsledcích dotazu nevidíte, není v poslední době vrácení se změnami. Pravděpodobně došlo k problému s místní konfigurací a měli byste [přeinstalovat agenta](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Pokud se váš počítač zobrazí ve výsledcích dotazu, zkontrolujte problémy s konfigurací oboru. [Konfigurace oboru](../automation-onboard-solutions-from-automation-account.md#scope-configuration) určuje, které počítače jsou konfigurovány pro řešení. 

6. Pokud se váš počítač zobrazuje ve vašem pracovním prostoru, ale ne ve správě aktualizací, musíte nakonfigurovat konfiguraci oboru tak, aby se zaměřila na počítač. Informace o tom, jak to provést, naleznete [v tématu Palubní počítače v pracovním prostoru](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. V pracovním prostoru spusťte tento dotaz.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Pokud získáte `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` výsledek, bylo dosaženo kvóty definované v pracovním prostoru, což zastavilo ukládání dat. V pracovním prostoru přejděte na **správu objemu dat** v části **Využití a odhadované náklady**a změňte nebo odeberte kvótu.

9. Pokud váš problém stále není vyřešen, postupujte podle pokynů v [tématu Nasazení pracovníka hybridního runbooku windows](../automation-windows-hrw-install.md) a přeinstalujte hybridní ho pracovníka pro Windows. U Linuxu postupujte podle pokynů v [části Nasazení hybridního pracovníka runbooku pro Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scénář: Nelze zaregistrovat zprostředkovatele prostředků automatizace pro předplatná

### <a name="issue"></a>Problém

Při práci s řešeními v účtu Automation dojde k následující chybě:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků automatizace není registrován v předplatném.

### <a name="resolution"></a>Řešení

Pokud chcete zaregistrovat poskytovatele prostředků automatizace, postupujte takto na webu Azure Portal.

1. V seznamu služeb Azure v dolní části portálu vyberte **Všechny služby**a pak vyberte Předplatná ve skupině Obecné **služby.**

2. Vyberte své předplatné.

3. V části **Nastavení**vyberte **zprostředkovatele prostředků**.

4. Ze seznamu poskytovatelů prostředků ověřte, zda je poskytovatel prostředků Microsoft.Automation registrován.

5. Pokud není uvedena, zaregistrujte poskytovatele Microsoft.Automation podle pokynů na [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scénář: Plánovaná aktualizace s dynamickým plánem zmeškané některé počítače

### <a name="issue"></a>Problém

Počítače zahrnuté v náhledu aktualizace se nezobrazují v seznamu počítačů opravených během plánovaného spuštění.

### <a name="cause"></a>Příčina

Tento problém může mít jednu z následujících příčin:

* Odběry definované v oboru v dynamickém dotazu nejsou nakonfigurovány pro registrovaného zprostředkovatele prostředků automatizace.

* Počítače nebyly k dispozici nebo neměly příslušné značky při spuštění plánu.

### <a name="resolution"></a>Řešení

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Odběry nejsou nakonfigurovány pro registrovaného zprostředkovatele prostředků automatizace

Pokud vaše předplatné není nakonfigurované pro poskytovatele prostředků automatizace, nemůžete dotazovat nebo načítat informace o počítačích v tomto předplatném. Pomocí následujících kroků ověřte registraci předplatného.

1. Na [webu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)přecte seznam služeb Azure.

2. Vyberte **Všechny služby**a pak vyberte Předplatná ve skupině Obecné **služby.** 

3. Najděte předplatné definované v oboru pro vaše nasazení.

4. V části **Nastavení**zvolte **Zprostředkovatelé prostředků**.

5. Ověřte, zda je zaregistrován poskytovatel prostředků Microsoft.Automation.

6. Pokud není uvedena, zaregistrujte poskytovatele Microsoft.Automation podle pokynů na [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Počítače nejsou k dispozici nebo nejsou správně označeny při provádění plánu

Pokud je vaše předplatné nakonfigurováno pro zprostředkovatele prostředků automatizace, ale spuštění plánu aktualizací se zadanými [dynamickými skupinami](../automation-update-management-groups.md) vynechalo některé počítače, použijte následující postup.

1. Na webu Azure Portal otevřete účet Automation a vyberte **Správa aktualizací**.

2. Zkontrolujte [historii správy aktualizací](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) a určete přesný čas spuštění nasazení aktualizace. 

3. U počítačů, u kterých máte podezření, že je správa aktualizací zmeškala, použijte Azure Resource Graph (ARG) k [vyhledání změn počítače](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Vyhledejte změny po značnou dobu, například jeden den před spuštěním nasazení aktualizace.

5. Zkontrolujte výsledky hledání pro všechny systémové změny, jako je například odstranit nebo aktualizovat změny počítačů v tomto období. Tyto změny mohou změnit stav počítače nebo značky tak, aby počítače nebyly vybrány v seznamu počítačů při nasazení aktualizací.

6. Podle potřeby upravte počítače a nastavení prostředků, abyste opravili stav počítače nebo problémy se značkami.

7. Znovu spusťte plán aktualizace, abyste zajistili, že nasazení se zadanými dynamickými skupinami zahrnuje všechny počítače.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scénář: Očekávané počítače se nezobrazují ve verzi Preview pro dynamickou skupinu

### <a name="issue"></a>Problém

Virtuální počítače pro vybrané obory dynamické skupiny se nezobrazují v seznamu preview portálu Azure. Tento seznam se skládá ze všech počítačů načtených dotazem ARG pro vybrané obory. Obory jsou filtrovány pro počítače, které mají nainstalovány hybridní pracovníci runbooku a pro které máte přístupová oprávnění. 

### <a name="cause"></a>Příčina
 
Zde jsou možné příčiny tohoto problému:

* Nemáte správný přístup k vybraným oborům.
* Dotaz ARG nenačte očekávané počítače.
* Hybridní runbook worker není nainstalován v počítačích.

### <a name="resolution"></a>Řešení 

#### <a name="incorrect-access-on-selected-scopes"></a>Nesprávný přístup k vybraným oborům

Portál Azure zobrazuje pouze počítače, pro které máte přístup pro zápis v daném oboru. Pokud nemáte správný přístup k oboru, najdete v [tématu Kurz: Udělit uživateli přístup k prostředkům Azure pomocí RBAC a portálazure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Dotaz ARG nevrací očekávané počítače

Podle následujících kroků zjistíte, jestli vaše dotazy fungují správně.

1. Spusťte dotaz ARG formátovaný tak, jak je znázorněno níže v okně průzkumníka resource graphu na webu Azure Portal. Tento dotaz napodobuje filtry, které jste vybrali při vytváření dynamické skupiny ve správě aktualizací. Viz [Použití dynamických skupin se správou aktualizací](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Zde naleznete příklad:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Zkontrolujte, jestli jsou počítače, které hledáte, uvedeny ve výsledcích dotazu. 

3. Pokud počítače nejsou uvedeny, pravděpodobně došlo k problému s filtrem vybraným v dynamické skupině. Podle potřeby upravte konfiguraci skupiny.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybridní pracovník runbooku není nainstalován v počítačích

Počítače se zobrazí ve výsledcích dotazu ARG, ale stále se nezobrazují v náhledu dynamické skupiny. V takovém případě nemusí být počítače označeny jako hybridní pracovníci, a proto nelze spustit úlohy Azure Automation a Update Management. Chcete-li zajistit, aby počítače, které očekáváte, byly nastaveny jako hybridní pracovníci runbooku:

1. Na webu Azure Portal přejděte na účet automatizace pro počítač, který se nezobrazuje správně.

2. V části **Automatizace procesů**vyberte **Hybridní pracovní skupiny** .

3. Vyberte kartu **Systémové hybridní pracovní skupiny.**

4. Ověřte, zda je pro tento počítač přítomen hybridní pracovník.

5. Pokud počítač není nastaven jako hybridní pracovník, proveďte úpravy pomocí pokynů na [automatiké prostředky v datovém centru nebo cloudu pomocí hybridní hospo- pracovníka runbooku](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Připojte počítač ke skupině Hybridní pracovní kniha Runbook.

7. Opakujte výše uvedené kroky pro všechny počítače, které nebyly zobrazeny v náhledu.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scénář: Komponenty pro řešení správy aktualizací povoleny, zatímco virtuální počítače se nadále zobrazují jako nakonfigurované

### <a name="issue"></a>Problém

Na virtuálním počítači se zobrazí i další zpráva 15 minut po připojení:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Komunikace s účtem Automation je blokována.

* Existuje duplicitní název počítače s různými ID zdrojového počítače. K tomuto scénáři dochází, když je virtuální počítač s konkrétním názvem počítače vytvořen v různých skupinách prostředků a hlásí se stejnému pracovnímu prostoru logistického agenta v předplatném.

* Image virtuálního počítače, která je na palubě, může pocházet z klonovaného počítače, který nebyl připraven s nainstalovaným systémem Příprava (sysprep) s nainstalovaným agentem Microsoft Monitoring Agent (MMA).

### <a name="resolution"></a>Řešení

Chcete-li pomoci při určování přesný problém s virtuálním počítačem, spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojen s účtem Automation.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Komunikace s účtem Automation byla zablokována

Přejděte na [plánování sítě](../automation-update-management.md#ports) a zjistěte, které adresy a porty musí být povoleny, aby správa aktualizací fungovala.

#### <a name="duplicate-computer-name"></a>Duplicitní název počítače

Přejmenujte virtuální počítače, abyste zajistili jedinečné názvy v jejich prostředí.

#### <a name="onboarded-image-from-cloned-machine"></a>Palubní obraz z klonovaného stroje

Pokud používáte klonovaný obrázek, mají různé názvy počítačů stejné ID zdrojového počítače. V tomto případě:

1. V pracovním prostoru Log Analytics odeberte virtuální počítač `MicrosoftDefaultScopeConfig-Updates` z uloženého hledání konfigurace oboru, pokud je zobrazen. Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.

2. Spusťte následující rutinu.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Spuštěním `Restart-Service HealthService` restartu je služba health service. Tato operace znovu vytvoří klíč a generuje nové UUID.

4. Pokud tento přístup nefunguje, spusťte nejprve program Sysprep na bitové kopii a potom nainstalujte MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scénář: Zobrazí se chyba propojeného předplatného při vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure

### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí následující chyba:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Příčina

K této chybě dochází při vytvoření nasazení aktualizace, která má virtuální počítače Azure v jiném tenantovi, který je součástí nasazení aktualizace.

### <a name="resolution"></a>Řešení

Pomocí následujícího řešení naplánujete tyto položky. Rutinu [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s parametrem `ForUpdate` můžete použít k vytvoření plánu. Potom použijte rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předaj te počítače `NonAzureComputer` v druhém tenantovi parametru. Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scénář: Nevysvětlitelné restartování

### <a name="issue"></a>Problém

I když jste nastavili možnost **Řízení restartování** na **možnost Nikdy se nerestartovat**, počítače se po instalaci aktualizací stále restartují.

### <a name="cause"></a>Příčina

Služba Windows Update může být upravena několika klíči registru, z nichž každý může změnit chování při restartování.

### <a name="resolution"></a>Řešení

Zkontrolujte klíče registru uvedené v části [Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru používaných ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) a ujistěte se, že jsou počítače správně nakonfigurovány.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scénář: Počítač zobrazuje "Nepodařilo se spustit" v nasazení aktualizace

### <a name="issue"></a>Problém

Zařízení zobrazuje `Failed to start` stav. Při zobrazení konkrétních podrobností o počítači se zobrazí následující chyba:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Ten stroj už neexistuje.
* Zařízení je vypnuté a nedosažitelné.
* Počítač má problém s připojením k síti, a proto je hybridní pracovník v počítači nedostupný.
* Došlo k aktualizaci MMA, která změnila ID zdrojového počítače.
* Spuštění aktualizace bylo omezeno, pokud jste dosáhli limitu 2000 souběžných úloh v účtu automatizace. Každé nasazení je považováno za úlohu a každý počítač v nasazení aktualizace se počítá jako úloha. Všechny ostatní úlohy automatizace nebo nasazení aktualizace aktuálně spuštěné v účtu automatizace se započítává do limitu souběžných úloh.

### <a name="resolution"></a>Řešení

Pokud je to možné, použijte [dynamické skupiny](../automation-update-management-groups.md) pro nasazení aktualizací. Kromě toho můžete provést následující kroky.

1. Ověřte, zda počítač stále existuje a je dostupný. 
2. Pokud počítač neexistuje, upravte nasazení a odeberte ho.
3. Seznam portů a adres, které jsou požadovány pro správu aktualizací, najdete v části [Plánování sítě](../automation-update-management.md#ports) a ověřte, zda počítač tyto požadavky splňuje.
4. Ověřte připojení k hybridnímu pracovníkovi runbooku pomocí poradce při potížích s agentem pracovníka hybridního runbooku. Další informace o poradci při potížích najdete [v tématu Poradce při potížích s agentem aktualizace](update-agent-issues.md).
5. Spusťte následující dotaz v Log Analytics najít počítače ve vašem prostředí, pro které se změnilo ID zdrojového počítače. Vyhledejte počítače, které `Computer` mají stejnou `SourceComputerId` hodnotu, ale jinou hodnotu.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Po nalezení ohrožených počítačů upravte nasazení aktualizací, která se na tyto `SourceComputerId` počítače zaměřují, a pak je odeberte a odečtěte tak, aby odrážely správnou hodnotu.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scénář: Aktualizace jsou nainstalovány bez nasazení

### <a name="issue"></a>Problém

Když zaregistrujete počítač se systémem Windows ve správě aktualizací, zobrazí se aktualizace nainstalované bez nasazení.

### <a name="cause"></a>Příčina

V systému Windows jsou aktualizace nainstalovány automaticky, jakmile jsou k dispozici. Toto chování může způsobit zmatek, pokud jste nenaplánovali aktualizaci, která má být nasazena do počítače.

### <a name="resolution"></a>Řešení

Výchozí `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` hodnota klíče registru je nastavena na hodnotu 4: `auto download and install`.

Pro klienty správy aktualizací doporučujeme nastavit `auto download but do not auto install`tento klíč na 3: .

Další informace naleznete [v tématu Konfigurace automatických aktualizací](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scénář: Počítač je již zaregistrován na jiný účet.

### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Příčina

Počítač již byl založen do jiného pracovního prostoru pro správu aktualizací.

### <a name="resolution"></a>Řešení

1. Postupujte podle kroků v části [Počítače se nezobrazují na portálu v části Správa aktualizací](#nologs) a ujistěte se, že počítač hlásí do správného pracovního prostoru.
2. Vyčistěte artefakty v počítači [odstraněním skupiny hybridních runbooků](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)a akci opakujte.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scénář: Počítač nemůže komunikovat se službou

### <a name="issue"></a>Problém

Zobrazí se jedna z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Příčina

Síťová komunikace může blokovat proxy server, brána nebo brána firewall. 

### <a name="resolution"></a>Řešení

Zkontrolujte síť a ujistěte se, že jsou povoleny příslušné porty a adresy. Podívejte se [na požadavky na síť](../automation-hybrid-runbook-worker.md#network-planning) pro seznam portů a adres, které jsou vyžadovány službou Update Management a Hybridní pracovníci sady Runbook.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scénář: Nelze vytvořit certifikát podepsaný svým držitelem.

### <a name="issue"></a>Problém

Zobrazí se jedna z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Příčina

Hybridní pracovník runbooku nemohl vygenerovat certifikát podepsaný svým držitelem.

### <a name="resolution"></a>Řešení

Ověřte, zda má systémový účet přístup pro čtení do složky **C:\ProgramData\Microsoft\Crypto\RSA,** a akci opakujte.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scénář: Naplánovaná aktualizace se nezdařila s chybou MaintenanceWindowExceeded.

### <a name="issue"></a>Problém

Výchozí okno údržby aktualizací je 120 minut. Okno údržby můžete prodloužit maximálně na 6 hodin nebo 360 minut.

### <a name="resolution"></a>Řešení

Upravte všechna neúspěšná plánovaná nasazení aktualizací a zvětšete okno údržby.

Další informace o údržbě systému Windows naleznete v [tématu Instalace aktualizací](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scénář: Stroj se zobrazí jako "Není hodnoceno" a zobrazuje výjimku HRESULT

### <a name="issue"></a>Problém

* Máte počítače, které `Not assessed` se zobrazují v části **Dodržování předpisů**a zobrazí se pod nimi zpráva o výjimce.
* Na portálu se zobrazí kód chyby HRESULT.

### <a name="cause"></a>Příčina

Agent aktualizace (Agent windows update v systému Windows; správce balíčků pro distribuci Linuxu) není správně nakonfigurován. Správa aktualizací spoléhá na agenta aktualizace počítače, který poskytuje potřebné aktualizace, stav opravy a výsledky nasazených oprav. Bez těchto informací, správa aktualizací nemůže správně sestavy na opravy, které jsou potřebné nebo nainstalované.

### <a name="resolution"></a>Řešení

Pokuste se provádět aktualizace místně v počítači. Pokud se tato operace nezdaří, obvykle to znamená, že došlo k chybě konfigurace agenta aktualizace.

Tento problém je často způsoben konfigurací sítě a problémy s bránou firewall. K opravě problému použijte následující kontroly.

* Pro Linux, zkontrolujte příslušnou dokumentaci a ujistěte se, že můžete dosáhnout koncového bodu sítě úložiště balíčků.

* V systému Windows zkontrolujte konfiguraci agenta, jak je uvedeno v [části Aktualizace se nestahují z koncového bodu intranet (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Pokud jsou počítače nakonfigurovány pro službu Windows Update, ujistěte se, že se můžete dostat ke koncovým bodům popsaným v [části Problémy související s protokolem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Pokud jsou počítače konfigurovány pro službu Windows Server Update Services (WSUS), ujistěte se, že se můžete dostat na server WSUS nakonfigurovaný [klíčem registru WUServer](/windows/deployment/update/waas-wu-settings).

Pokud se zobrazí HRESULT, poklepejte na výjimku zobrazenou červeně, zobrazí se celá zpráva o výjimce. V následující tabulce naleznete možná řešení nebo doporučené akce.

|Výjimka  |Řešení nebo opatření  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Další podrobnosti o příčině výjimky naleznete v příslušném kódu chyby v [seznamu kódů chyb aktualizace systému Windows.](https://support.microsoft.com/help/938205/windows-update-error-code-list)        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ty označují problémy s připojením k síti. Ujistěte se, že váš počítač má síťové připojení ke správě aktualizací. Seznam požadovaných portů a adres naleznete v části [plánování sítě.](../automation-update-management.md#ports)        |
|`0x8024001E`| Operace aktualizace nebyla dokončena, protože služba nebo systém se vypínal.|
|`0x8024002E`| Služba Windows Update je zakázána.|
|`0x8024402C`     | Pokud používáte server Služby WSUS, ujistěte `WUServer` `WUStatusServer` se, `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` že hodnoty registru pro klíč registru a pod ním určují správný server WSUS.        |
|`0x80072EE2`|Došlo k problému s připojením k síti nebo k problému při hovoru na nakonfigurovaný server WSUS. Zkontrolujte nastavení služby WSUS a ujistěte se, že služba je přístupná z klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Zkontrolujte, zda je služba Windows Update (wuauserv) spuštěná a není zakázána.        |
|`0x80070005`| Chyba odepření přístupu může být způsobena některou z následujících příčin:<br> Infikovaný počítač<br> Nastavení služby Windows Update není správně nakonfigurováno<br> Chyba oprávnění souboru se složkou %WinDir%\SoftwareDistribution<br> Nedostatek místa na disku na systémové jednotce (C:).
|Jakákoli jiná obecná výjimka     | Spusťte vyhledávání na internetu pro možná řešení a spolupracujte s místní IT podporou.         |

Kontrola souboru **%Windir%\Windowsupdate.log** vám může také pomoci určit možné příčiny. Další informace o tom, jak číst protokol, naleznete v [tématu Jak číst soubor Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Můžete také stáhnout a spustit Poradce při potížích se [službou Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) a zkontrolovat případné problémy se službou Windows Update v počítači.

> [!NOTE]
> Dokumentace [poradce při potížích se systémem Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) označuje, že je určen pro použití v klientech systému Windows, ale funguje také v systému Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scénář: Aktualizace spustit vrátí neúspěšný stav (Linux)

### <a name="issue"></a>Problém

Spuštění aktualizace se spustí, ale během spuštění dojde k chybám.

### <a name="cause"></a>Příčina

Možné příčiny:

* Správce balíčků není v pořádku.
* Agent aktualizace (WUA pro Windows, správce balíčků specifický pro distro pro Linux) je nesprávně nakonfigurován.
* Konkrétní balíčky narušují opravy na základě cloudu.
* Stroj je nedostupný.
* Aktualizace měly závislosti, které nebyly vyřešeny.

### <a name="resolution"></a>Řešení

Pokud dojde k selhání během spuštění aktualizace po úspěšném spuštění, [zkontrolujte výstup úlohy](../manage-update-multi.md#view-results-of-an-update-deployment) z postiženého počítače v běhu. Můžete najít konkrétní chybové zprávy z vašich počítačů, které můžete zkoumat a přijmout opatření. Správa aktualizací vyžaduje, aby správce balíčků byl v pořádku pro úspěšná nasazení aktualizací.

Pokud jsou bezprostředně před selháním úlohy zpozorovány konkrétní opravy, balíčky nebo aktualizace, můžete zkusit tyto položky [vyloučit](../automation-tutorial-update-management.md#schedule-an-update-deployment) z dalšího nasazení aktualizace. Informace o protokolu ze služby Windows Update naleznete v tématu [Soubory protokolu služby Windows Update](/windows/deployment/update/windows-update-logs).

Pokud se vám nepodaří vyřešit problém s opravami, vytvořte kopii souboru **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** a uchovávejte jej pro účely řešení potíží před zahájením další aktualizace.

## <a name="patches-arent-installed"></a>Opravy nejsou nainstalovány

### <a name="machines-dont-install-updates"></a>Na počítačích se neinstalují aktualizace

Zkuste aktualizace spustit přímo na počítači. Pokud počítač nemůže aktualizace použít, podívejte se do [seznamu potenciálních chyb v průvodci odstraňováním potíží](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Pokud aktualizace spustit místně, zkuste odebrat a přeinstalovat agenta v počítači podle pokynů na [Odebrat virtuální počítač ze správy aktualizací](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Vím, že aktualizace jsou k dispozici, ale nezobrazují se tak, jak jsou k dispozici na mých počítačích

K tomu často dochází, pokud jsou počítače nakonfigurovány tak, aby přijínaly aktualizace ze služby WSUS nebo Microsoft Endpoint Configuration Manager, ale služba WSUS a Configuration Manager aktualizace neschválily.

Můžete zkontrolovat, zda jsou počítače nakonfigurovány pro služby WSUS `UseWUServer` a SCCM, křížovým odkazem na klíč registru na klíče registru v [části Konfigurace automatických aktualizací úpravou registru v tomto článku](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Pokud aktualizace nejsou schváleny ve službách WSUS, nejsou nainstalovány. Neschválené aktualizace můžete zkontrolovat v Log Analytics spuštěním následujícího dotazu.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizace se zobrazují jako nainstalované, ale na počítači je nemůžu najít

Aktualizace se často nahrazují jinými aktualizacemi. Další informace naleznete v tématu [Update je nahrazen](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) v průvodci poradce při potížích se službou Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalace aktualizací podle klasifikace v Linuxu

Pro nasazování aktualizací v Linuxu podle klasifikace (důležité aktualizace a aktualizace zabezpečení) platí důležité výhody a rizika, a to zejména pro CentOS. Tato omezení jsou popsána na [stránce přehledu správy aktualizací](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 trvale chybí

KB2267602 je [aktualizace definice programu Windows Defender](https://www.microsoft.com/wdsi/definitions). Je denně aktualizován.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů.

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
