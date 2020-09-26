---
title: Řešení potíží s Azure Automation Update Management
description: V tomto článku se dozvíte, jak řešit problémy s Azure Automation Update Management.
services: automation
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: 26ce59c1d4ec8e3e2bf2096a4688b7639148410f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359235"
---
# <a name="troubleshoot-update-management-issues"></a>Řešení problémů s Update Managementem

Tento článek popisuje problémy, ke kterým může dojít při nasazování funkce Update Management do počítačů. U agenta Hybrid Runbook Worker je poradce při potížích s agentem, aby mohl zjistit základní problém. Další informace o poradci při potížích najdete v tématu řešení potíží s [agentem Windows Update](update-agent-issues.md) a [odstraňování potíží s problémy agenta aktualizací pro Linux](update-agent-issues-linux.md). Další problémy při nasazení funkcí najdete v tématu [řešení potíží s nasazením funkcí](onboarding.md).

>[!NOTE]
>Pokud narazíte na problémy při nasazení Update Management na počítači s Windows, otevřete Prohlížeč událostí systému Windows a v protokolu událostí pro **aplikace a služby** v místním počítači zaškrtněte protokol událostí **Operations Manager** . Vyhledejte události s ID události 4502 a podrobnostmi události, které obsahují `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scénář: zobrazí se chyba "Nepodařilo se povolit řešení aktualizace".

### <a name="issue"></a>Problém

Při pokusu o povolení Update Management ve vašem účtu Automation se zobrazí následující chyba:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Požadavky na bránu firewall sítě pro agenta Log Analytics nemusí být správně nakonfigurované. Tato situace může způsobit selhání agenta při překladu adres URL DNS.

* Update Management cílení není správně nakonfigurované a počítač nepřijímá aktualizace podle očekávání.

* Můžete si také všimnout, že počítač zobrazuje stav `Non-compliant` pod **dodržováním předpisů**. **Analýza stolních počítačů** ve stejnou dobu sestaví agenta jako `Disconnected` .

### <a name="resolution"></a>Řešení

* Spusťte Poradce při potížích pro [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linux](update-agent-issues-linux.md#troubleshoot-offline), a to v závislosti na operačním systému.

* Pokud chcete zjistit, které adresy a porty musí Update Management fungovat, přejít na [konfiguraci sítě](../automation-hybrid-runbook-worker.md#network-planning) .  

* Vyhledejte problémy s konfigurací oboru. [Konfigurace oboru](../update-management/update-mgmt-scope-configuration.md) určuje, které počítače jsou nakonfigurovány pro Update Management. Pokud se Váš počítač zobrazuje v pracovním prostoru, ale ne v Update Management, musíte nastavit konfiguraci oboru pro cílení na počítače. Další informace o konfiguraci oboru najdete v tématu [Povolení počítačů v pracovním prostoru](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace).

* Odeberte konfiguraci pracovního procesu podle kroků v části [odebrání Hybrid Runbook Worker z místního počítače s Windows](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) nebo [Odebrání Hybrid Runbook Worker z místního počítače se systémem Linux](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scénář: nahrazená aktualizace byla označena jako chybějící v Update Management

### <a name="issue"></a>Problém

Pro účet Automation se zobrazí staré aktualizace, které chybí, i když byly nahrazené. Nahrazená aktualizace je ta, kterou nemusíte instalovat, protože je k dispozici pozdější aktualizace, která opravuje stejnou chybu zabezpečení. Update Management ignoruje nahrazenou aktualizaci, takže ji nelze použít ve prospěch nahrazující aktualizace. Informace o souvisejícím problému najdete v tématu [aktualizace je nahrazená](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Příčina

Nahrazené aktualizace nejsou správně označeny jako odmítnuté, aby je bylo možné považovat za nepoužité.

### <a name="resolution"></a>Řešení

Pokud se nahradí nahrazené aktualizace 100 procent, měli byste změnit stav schválení této aktualizace na `Declined` . Změna stavu schválení pro všechny vaše aktualizace:

1. V účtu Automation vyberte **Update Management** pro zobrazení stavu počítače. Viz [zobrazení posouzení aktualizací](../update-management/update-mgmt-view-update-assessments.md).

2. Zkontrolujte nahrazenou aktualizaci a ujistěte se, že se nejedná o 100%.

3. Pokud nemáte dotaz týkající se aktualizace, označte ji jako odmítnutou.

4. Vyberte **počítače** a ve sloupci **dodržování předpisů** vynuťte opětovné prohledání dodržování předpisů. Viz [Správa aktualizací pro virtuální počítače](../update-management/update-mgmt-manage-updates-for-vm.md).

5. Opakujte výše uvedené kroky pro další nahrazené aktualizace.

6. Spuštěním Průvodce vyčištěním odstraňte soubory z odmítnutých aktualizací. 

7. Pro Windows Server Update Services (WSUS) ručně vyčistěte všechny nahrazené aktualizace a aktualizujte infrastrukturu.

8. Opakujte tento postup pravidelně, abyste opravili problém zobrazení a minimalizovali velikost místa na disku využitého pro správu aktualizací.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scénář: počítače se nezobrazují na portálu v části Update Management

### <a name="issue"></a>Problém

Vaše počítače mají následující příznaky:

* Váš počítač se zobrazuje `Not configured` z Update Management zobrazení virtuálního počítače.

* V zobrazení Update Management účtu Azure Automation chybí vaše počítače.

* Máte počítače, které ukazují na `Not assessed` **dodržování předpisů**. Data prezenčního signálu se ale zobrazí v protokolech Azure Monitor pro Hybrid Runbook Worker, ale ne pro Update Management.

### <a name="cause"></a>Příčina

K tomuto problému může dojít při potížích s místními konfiguracemi nebo nesprávně nakonfigurovaným nastavením oboru. Možné konkrétní příčiny:

* Je možné, že bude nutné znovu zaregistrovat a znovu nainstalovat Hybrid Runbook Worker.

* Je možné, že jste v pracovním prostoru nastavili kvótu, která je dosažená a která zabraňuje dalšímu ukládání dat.

### <a name="resolution"></a>Řešení

1. Spusťte Poradce při potížích pro [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linux](update-agent-issues-linux.md#troubleshoot-offline), a to v závislosti na operačním systému.

2. Ujistěte se, že váš počítač hlásí správný pracovní prostor. Pokyny k tomu, jak tento aspekt ověřit, najdete v tématu [ověření připojení agenta k Azure monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Také se ujistěte, že je tento pracovní prostor propojený s vaším účtem Azure Automation. Potvrďte to tak, že přejdete na svůj účet Automation a v části **související prostředky**vyberete **propojený pracovní prostor** .

3. Ujistěte se, že se počítače zobrazí v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation. Spusťte následující dotaz v pracovním prostoru Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Pokud počítač ve výsledcích dotazu nevidíte, nebude nedávno vrácen se změnami. Pravděpodobně došlo k potížím s místní konfigurací a je třeba [agenta přeinstalovat](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

5. Pokud se Váš počítač zobrazí ve výsledcích dotazu, vyhledejte problémy s konfigurací rozsahu. [Konfigurace oboru](../update-management/update-mgmt-scope-configuration.md) určuje, které počítače jsou nakonfigurovány pro Update Management.

6. Pokud se Váš počítač zobrazuje v pracovním prostoru, ale ne v Update Management, musíte nakonfigurovat konfiguraci oboru, aby se na počítač nastavil cíl. Další informace o tom, jak to udělat, najdete v tématu [Povolení počítačů v pracovním prostoru](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace).

7. V pracovním prostoru spusťte tento dotaz.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Pokud získáte `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` výsledek, je dosaženo kvóty definované v pracovním prostoru, která zastavila ukládání dat. V pracovním prostoru přejděte na **Správa objemu dat** v části **využití a odhadované náklady**a změňte nebo odeberte kvótu.

9. Pokud je problém stále nevyřešený, postupujte podle kroků v části [nasazení Hybrid Runbook Worker Windows](../automation-windows-hrw-install.md) a přeinstalujte Hybrid Worker pro Windows. V případě systému Linux postupujte podle pokynů v části [nasazení Hybrid Runbook Worker pro Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scénář: nejde zaregistrovat poskytovatele prostředků služby Automation pro předplatná.

### <a name="issue"></a>Problém

Při práci s nasazeními funkcí v účtu Automation dojde k následující chybě:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků Automation není v předplatném zaregistrován.

### <a name="resolution"></a>Řešení

Chcete-li zaregistrovat poskytovatele prostředků služby Automation, postupujte podle následujících kroků v Azure Portal.

1. V seznamu služeb Azure v dolní části portálu vyberte **všechny služby**a potom vyberte **předplatná** ve skupině obecné služby.

2. Vyberte předplatné.

3. V části **Nastavení**vyberte **poskytovatelé prostředků**.

4. V seznamu poskytovatelů prostředků ověřte, že je zaregistrován poskytovatel prostředků Microsoft. Automation.

5. Pokud není v seznamu uveden, zaregistrujte poskytovatele Microsoft. Automation podle kroků v části [řešení chyb pro registraci poskytovatele prostředků](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scénář: naplánovaná aktualizace s dynamickým plánem nenalezla některé počítače.

### <a name="issue"></a>Problém

Počítače zahrnuté ve verzi Preview se nezobrazí v seznamu počítačů, které byly během naplánovaného spuštění opraveny.

### <a name="cause"></a>Příčina

Tento problém může mít jednu z následujících příčin:

* Odběry definované v oboru v dynamickém dotazu nejsou konfigurovány pro registrovaného poskytovatele prostředků služby Automation.

* Počítače nebyly k dispozici nebo nebyly po provedení plánu příslušné značky.

### <a name="resolution"></a>Řešení

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>U předplatných není nakonfigurovaných pro registrovaného zprostředkovatele prostředků Automation.

Pokud vaše předplatné není pro poskytovatele prostředků služby Automation nakonfigurované, nemůžete na počítače v tomto předplatném dotazovat ani načítat informace. K ověření registrace předplatného použijte následující postup.

1. V [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)přejděte na seznam služeb Azure.

2. Vyberte **všechny služby**a potom vyberte **předplatná** ve skupině obecné služby.

3. Vyhledá předplatné definované v oboru pro vaše nasazení.

4. V části **Nastavení**vyberte **poskytovatelé prostředků**.

5. Ověřte, že je zaregistrován poskytovatel prostředků Microsoft. Automation.

6. Pokud není v seznamu uveden, zaregistrujte poskytovatele Microsoft. Automation podle kroků v části [řešení chyb pro registraci poskytovatele prostředků](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Počítače nejsou k dispozici nebo nejsou při spuštění plánu správně označeny.

Následující postup použijte v případě, že je vaše předplatné nakonfigurované pro poskytovatele prostředků služby Automation, ale spuštění plánu aktualizace se zadanými [dynamickými skupinami](../update-management/update-mgmt-groups.md) nenalezlo některé počítače.

1. V Azure Portal otevřete účet Automation a vyberte **Update Management**.

2. Zkontrolujte [historii Update Management](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment) a určete přesný čas spuštění nasazení aktualizace.

3. Pro počítače, u kterých se domníváte, že Update Management, použijte k [Vyhledání změn počítačů](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)službu Azure Resource Graph (ARG).

4. Před spuštěním nasazení aktualizace hledejte změny po značnou dobu, například jeden den.

5. Prohlédněte si výsledky hledání jakýchkoli systémových změn, jako je například odstranění nebo aktualizace změn, na počítače v tomto období. Tyto změny mohou změnit stav počítače nebo značky, aby počítače nebyly při nasazení aktualizací vybrány v seznamu počítačů.

6. Upravte nastavení počítačů a prostředků podle potřeby, aby byly správné pro stav počítače nebo problémy se značkami.

7. Spusťte znovu plán aktualizace, aby se zajistilo, že nasazení se zadanými dynamickými skupinami zahrnuje všechny počítače.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scénář: pro dynamickou skupinu se nezobrazí očekávané počítače ve verzi Preview.

### <a name="issue"></a>Problém

Virtuální počítače pro vybrané rozsahy dynamické skupiny se v seznamu Azure Portal Preview nezobrazují. Tento seznam se skládá ze všech počítačů načtených dotazem ARG pro vybrané obory. Obory jsou filtrované pro počítače, které mají nainstalované procesy Hybrid Runbook Worker a pro které máte přístupová oprávnění.

### <a name="cause"></a>Příčina

Tady jsou možné příčiny tohoto problému:

* Nemáte správný přístup k vybraným oborům.
* Dotaz ARG nenačte očekávané počítače.
* Na počítačích není nainstalovaná Hybrid Runbook Worker.

### <a name="resolution"></a>Řešení 

#### <a name="incorrect-access-on-selected-scopes"></a>Nesprávný přístup pro vybrané obory

Azure Portal zobrazí jenom počítače, pro které máte v daném oboru přístup pro zápis. Pokud nemáte správný přístup k oboru, přečtěte si téma [kurz: udělení přístupu uživatelům k prostředkům Azure pomocí RBAC a Azure Portal](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG dotaz nevrací očekávané počítače

Použijte následující postup, chcete-li zjistit, zda dotazy fungují správně.

1. Spusťte dotaz ARG, který je uveden níže v okně Průzkumník diagramů prostředků v Azure Portal. Tento dotaz napodobá filtrům, které jste vybrali při vytváření dynamické skupiny v Update Management. Viz [použití dynamických skupin s Update Management](../update-management/update-mgmt-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Tady je příklad:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Zkontrolujte, jestli jsou v seznamu výsledků dotazu uvedené počítače, které chcete najít.

3. Pokud počítače nejsou uvedené, pravděpodobně došlo k potížím s filtrem vybraným v dynamické skupině. Podle potřeby upravte konfiguraci skupiny.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker není na počítačích nainstalovaný.

Počítače se zobrazí ve výsledcích dotazu ARG, ale ve verzi Preview dynamické skupiny se ještě nezobrazují. V takovém případě nemusí být počítače určené jako hybridní pracovní procesy, takže nemůžou spouštět úlohy Azure Automation a Update Management. Pokud chcete zajistit, aby se počítače, které očekáváte zobrazit, nastavily jako procesy Hybrid Runbook Worker:

1. V Azure Portal přejdete do účtu Automation pro počítač, který se nezobrazuje správně.

2. V části **Automatizace procesu**vyberte **skupiny hybridních pracovních procesů** .

3. Vyberte kartu **System Hybrid Worker Groups** .

4. Ověřte, jestli je hybridní pracovní proces pro tento počítač přítomen.

5. Pokud počítač není nastavený jako hybridní pracovní proces, proveďte úpravy pomocí pokynů v tématu [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

6. Připojte počítač ke skupině Hybrid Runbook Worker.

7. Opakujte výše uvedené kroky pro všechny počítače, které se nezobrazovaly ve verzi Preview.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scénář: Update Management komponenty povoleny, zatímco se virtuální počítač nadále zobrazuje jako nakonfigurovaný

### <a name="issue"></a>Problém

Po zahájení nasazení se zobrazí následující zpráva na virtuálním počítači 15 minut:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

* Komunikace s účtem Automation je zablokovaná.

* Existuje duplicitní název počítače s různými ID zdrojového počítače. K tomuto scénáři dochází, když se virtuální počítač s určitým názvem počítače vytvoří v různých skupinách prostředků a hlásí se do stejného pracovního prostoru logistického agenta v rámci předplatného.

* Nasazená bitová kopie virtuálního počítače může pocházet z klonovaného počítače, který nebyl připraven na přípravu systému (Sysprep) s nainstalovaným agentem Log Analytics pro Windows.

### <a name="resolution"></a>Řešení

Pokud chcete určit přesný problém s virtuálním počítačem, spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Komunikace s blokovaným účtem služby Automation

Pokud chcete zjistit, které adresy a porty musí Update Management fungovat, přejít na [Plánování sítě](../update-management/update-mgmt-overview.md#ports) .

#### <a name="duplicate-computer-name"></a>Duplicitní název počítače

Přejmenujte své virtuální počítače, abyste měli jistotu, že jsou ve svém prostředí jedinečné názvy

#### <a name="deployed-image-from-cloned-machine"></a>Nasazená bitová kopie z klonovaného počítače

Pokud používáte Klonovaný obrázek, mají různé názvy počítačů stejné ID zdrojového počítače. V tomto případě:

1. V pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání `MicrosoftDefaultScopeConfig-Updates` Konfigurace oboru, pokud je zobrazený. Uložená hledání najdete v části **Obecné** v pracovním prostoru.

2. Spusťte následující rutinu.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Spusťte `Restart-Service HealthService` pro restartování služby Health Service. Tato operace znovu vytvoří klíč a vygeneruje nový UUID.

4. Pokud tento přístup nefunguje, nejprve na imagi spusťte nástroj Sysprep a pak nainstalujte agenta Log Analytics pro Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scénář: při vytváření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí chyba propojeného předplatného.

### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure dojde k následující chybě:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Příčina

K této chybě dojde, když vytvoříte nasazení aktualizace, které obsahuje virtuální počítače Azure v jiném tenantovi, které jsou zahrnuté v nasazení aktualizace.

### <a name="resolution"></a>Řešení

K naplánování těchto položek použijte následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) s `ForUpdateConfiguration` parametrem. Pak použijte rutinu [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) a předejte do parametru počítače v druhém tenantovi `NonAzureComputer` . Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scénář: Neobjasněná restartování

### <a name="issue"></a>Problém

I když jste nastavili možnost **ovládacího prvku restartování** na **Nikdy nerestartovat**, počítače se pořád po instalaci aktualizací restartují.

### <a name="cause"></a>Příčina

Web Windows Update může upravit několik klíčů registru, z nichž některé můžou upravovat chování při restartování.

### <a name="resolution"></a>Řešení

Zkontrolujte klíče registru uvedené v části [Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru používaných ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) a ujistěte se, že jsou vaše počítače správně nakonfigurované.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scénář: v nasazení aktualizace se v počítači zobrazí zpráva "Nepodařilo se spustit"

### <a name="issue"></a>Problém

Počítač zobrazuje `Failed to start` stav. Při zobrazení konkrétních podrobností pro počítač se zobrazí následující chyba:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Počítač už neexistuje.
* Počítač je vypnutý a nedosažitelný.
* Počítač má problém s připojením k síti, a proto je hybridní pracovní proces na počítači nedosažitelný.
* Byla zjištěna aktualizace agenta Log Analytics, který změnil ID zdrojového počítače.
* Pokud jste dosáhli limitu 200 souběžných úloh v účtu Automation, vaše spuštění aktualizace bylo omezené. Každé nasazení se považuje za úlohu a každý počítač v nasazení aktualizace se počítá jako úloha. Jakékoli jiné úlohy služby Automation nebo nasazení aktualizací, které aktuálně běží ve vašem účtu Automation, se počítá s limitem souběžných úloh.

### <a name="resolution"></a>Řešení

V případě potřeby použijte pro nasazení aktualizací [dynamické skupiny](../update-management/update-mgmt-groups.md) . Kromě toho můžete provést následující kroky.

1. Ověřte, zda váš počítač nebo server splňuje [požadavky](../update-management/update-mgmt-overview.md#client-requirements).
2. Ověřte připojení k Hybrid Runbook Worker pomocí Poradce při potížích Hybrid Runbook Worker agenta. Další informace o poradci při potížích najdete v tématu [Poradce při potížích s aktualizací agenta](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scénář: aktualizace se instalují bez nasazení.

### <a name="issue"></a>Problém

Při registraci počítače se systémem Windows v nástroji Update Management se zobrazí aktualizace nainstalované bez nasazení.

### <a name="cause"></a>Příčina

Ve Windows se aktualizace nainstalují automaticky, jakmile budou k dispozici. Toto chování může způsobit nejasnost, pokud jste neplánovali nasazení aktualizace na počítač.

### <a name="resolution"></a>Řešení

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`Klíč registru je standardně nastaven na hodnotu 4: `auto download and install` .

U Update Management klientů doporučujeme nastavit tento klíč na 3: `auto download but do not auto install` .

Další informace najdete v tématu [Konfigurace automatických aktualizací](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scénář: počítač je už zaregistrovaný na jiný účet.

### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Příčina

Počítač už je nasazený do jiného pracovního prostoru pro Update Management.

### <a name="resolution"></a>Řešení

1. Postupujte podle kroků v části [počítače se nezobrazují na portálu v části Update Management](#nologs) , abyste se ujistili, že se počítač hlásí do správného pracovního prostoru.
2. Odstraňte artefakty na počítači [odstraněním skupiny Hybrid Runbook](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)a pak to zkuste znovu.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scénář: počítač nemůže komunikovat se službou.

### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

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

Server proxy, brány nebo brány firewall můžou blokovat síťovou komunikaci.

### <a name="resolution"></a>Řešení

Zkontrolujte své sítě a ujistěte se, že jsou povolené příslušné porty a adresy. Seznam portů a adres, které jsou vyžadovány Update Management a hybridními pracovními procesy Runbooku, najdete v části [požadavky na síť](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scénář: Nejde vytvořit certifikát podepsaný svým držitelem.

### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Příčina

Hybrid Runbook Worker se nepovedlo vygenerovat certifikát podepsaný svým držitelem.

### <a name="resolution"></a>Řešení

Ověřte, že účet System má oprávnění ke čtení pro složku **C:\ProgramData\Microsoft\Crypto\RSA** , a zkuste to znovu.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scénář: naplánovaná aktualizace selhala s chybou MaintenanceWindowExceeded.

### <a name="issue"></a>Problém

Výchozí časové období údržby pro aktualizace je 120 minut. Časový interval pro správu a údržbu můžete zvýšit na maximálně 6 hodin nebo 360 minut.

### <a name="resolution"></a>Řešení

Upravte všechna neúspěšná naplánovaná nasazení aktualizací a zvyšte časový interval pro správu a údržbu.

Další informace o časových obdobích údržby najdete v tématu [install Updates](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scénář: počítač se zobrazuje jako "nehodnocený" a zobrazuje výjimku HRESULT

### <a name="issue"></a>Problém

* Máte počítače, které ukazují na `Not assessed` **dodržování předpisů**, a pod nimi se zobrazí zpráva s výjimkou.
* Na portálu se zobrazí kód chyby HRESULT.

### <a name="cause"></a>Příčina

Agent aktualizace (agent web Windows Update v systému Windows; Správce balíčků pro distribuci pro Linux) není správně nakonfigurovaný. Update Management spoléhá na agenta aktualizace počítače, aby poskytoval aktualizace, které jsou potřeba, stav opravy a výsledky nasazených oprav. Bez těchto informací Update Management nedokáže správně ohlásit potřebné opravy, které jsou potřeba nebo nainstalované.

### <a name="resolution"></a>Řešení

Zkuste provést aktualizace místně na počítači. Pokud tato operace selhává, obvykle to znamená, že došlo k chybě konfigurace agenta aktualizace.

Tato chyba je často způsobena konfigurací sítě a problémy s bránou firewall. K opravě problému použijte následující kontroly.

* V případě systému Linux zkontrolujte příslušnou dokumentaci a ujistěte se, že máte přístup ke koncovému bodu sítě úložiště balíčků.

* V případě systému Windows ověřte konfiguraci agenta, jak je uvedeno v [části aktualizace nejsou stahovány z intranetového koncového bodu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Pokud jsou počítače nakonfigurované pro web Windows Update, ujistěte se, že máte přístup ke koncovým bodům popsaným v tématu [problémy související s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Pokud jsou počítače nakonfigurované pro Windows Server Update Services (WSUS), ujistěte se, že máte přístup k serveru WSUS nakonfigurovanému pomocí [klíče registru WUServer](/windows/deployment/update/waas-wu-settings).

Pokud se zobrazí hodnota HRESULT, dvakrát klikněte na výjimku zobrazenou červenou a zobrazte celou zprávu o výjimce. V následující tabulce najdete potenciální řešení nebo doporučené akce.

|Výjimka  |Řešení nebo akce  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Vyhledejte v [seznamu kód chyby ve službě Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) příslušný kód chyby, kde najdete další podrobnosti o příčině výjimky.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ty naznačují problémy s připojením k síti. Ujistěte se, že váš počítač má síťové připojení k Update Management. Seznam požadovaných portů a adres najdete v části [Plánování sítě](../update-management/update-mgmt-overview.md#ports) .        |
|`0x8024001E`| Operace aktualizace nebyla dokončena, protože služba nebo systém se vypnul.|
|`0x8024002E`| Služba web Windows Update je zakázána.|
|`0x8024402C`     | Pokud používáte server WSUS, ujistěte se, že hodnoty registru pro `WUServer` a `WUStatusServer` v  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` klíči registru URČUJÍ správný server WSUS.        |
|`0x80072EE2`|Došlo k potížím s připojením k síti nebo k potížím při komunikaci s nakonfigurovaným serverem WSUS. Zkontrolujte nastavení služby WSUS a ujistěte se, že je služba z klienta přístupná.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ujistěte se, že služba web Windows Update (wuauserv) je spuštěná a není zakázaná.        |
|`0x80070005`| Chyba odepření přístupu může být způsobena některou z následujících příčin:<br> Nakažený počítač<br> Nastavení web Windows Update není správně nakonfigurováno.<br> Chyba oprávnění souboru ve složce%WinDir%\SoftwareDistribution<br> Nedostatek místa na disku na systémové jednotce (C:).
|Jakákoli jiná obecná výjimka     | Spusťte hledání na internetu z hlediska možných řešení a pracujte s místní podporou IT.         |

Kontrola souboru **%windir%\windowsupdate.log** vám také pomůže určit možné příčiny. Další informace o tom, jak číst protokol, najdete v tématu [postup čtení souboru Windowsupdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Můžete si také stáhnout a spustit [Poradce při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) a vyhledat případné problémy s web Windows Update v počítači.

> [!NOTE]
> V dokumentaci k [poradci při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) označuje, že se má používat na klientech Windows, ale funguje taky na Windows serveru.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scénář: příkaz Update Run vrátí stav selhání (Linux).

### <a name="issue"></a>Problém

Spustí se aktualizace, ale během běhu dojde k chybám.

### <a name="cause"></a>Příčina

Možné příčiny:

* Správce balíčků není v pořádku.
* Aktualizace agenta (WUA pro Windows, distribuce pro konkrétního správce balíčků pro Linux) je chybně nakonfigurovaná.
* Konkrétní balíčky jsou v konfliktu s využitím cloudových oprav.
* Počítač je nedosažitelný.
* Aktualizace obsahovaly závislosti, které nebyly vyřešeny.

### <a name="resolution"></a>Řešení

Pokud dojde k selhání během hromadné postupné aktualizace po úspěšném spuštění, [Podívejte se ve výstupu úlohy](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment) na příslušný počítač v běhu. Můžete najít konkrétní chybové zprávy z počítačů, které můžete prozkoumat a provádět s nimi akce. Update Management vyžaduje, aby správce balíčků byl v pořádku pro úspěšná nasazení aktualizací.

Pokud se určité opravy, balíčky nebo aktualizace zobrazují hned před tím, než se úloha nezdařila, můžete se pokusit [vyloučit](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment) tyto položky z nasazení Next Update. Informace o shromažďování informací o protokolu z web Windows Update najdete v tématu [web Windows Update souborů protokolu](/windows/deployment/update/windows-update-logs).

Pokud nemůžete vyřešit problém s opravou, vytvořte kopii souboru **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** a zachovejte ho pro účely řešení potíží před zahájením příštího nasazení aktualizace.

## <a name="patches-arent-installed"></a>Opravy nejsou nainstalované.

### <a name="machines-dont-install-updates"></a>Na počítačích se neinstalují aktualizace

Zkuste aktualizace spustit přímo na počítači. Pokud počítač nemůže aktualizace použít, Projděte si [seznam potenciálních chyb v Průvodci odstraňováním potíží](#hresult).

Pokud se aktualizace spouštějí místně, zkuste odebrat a znovu nainstalovat agenta v počítači podle pokynů uvedených v části [Odebrání virtuálního počítače z Update Management](../update-management/update-mgmt-remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ví, že aktualizace jsou k dispozici, ale nezobrazují se jako dostupné na mých počítačích

K tomu často dochází, když jsou počítače nakonfigurované tak, aby získaly aktualizace ze služby WSUS nebo Microsoft Endpoint Configuration Manager, ale služby WSUS a Configuration Manager aktualizace neschválily.

Můžete zjistit, jestli jsou počítače nakonfigurované pro WSUS a SCCM pomocí křížového odkazu na `UseWUServer` klíč registru v [části Konfigurace automatických aktualizací úpravou registru v tomto článku](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Nejsou-li aktualizace schváleny ve službě WSUS, nejsou nainstalovány. Neschválené aktualizace můžete vyhledat v Log Analytics spuštěním následujícího dotazu.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizace se zobrazují jako nainstalované, ale na počítači je nemůžu najít

Aktualizace se často nahrazují jinými aktualizacemi. Další informace najdete v tématu [aktualizace je nahrazená](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) v Průvodci odstraňováním potíží s web Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalace aktualizací podle klasifikace v Linuxu

Pro nasazování aktualizací v Linuxu podle klasifikace (důležité aktualizace a aktualizace zabezpečení) platí důležité výhody a rizika, a to zejména pro CentOS. Tato omezení jsou popsaná na [stránce přehled Update Management](../update-management/update-mgmt-overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 se konzistentně schází.

KB2267602 je [aktualizace definice programu Windows Defender](https://www.microsoft.com/wdsi/definitions). Aktualizuje se každý den.

## <a name="next-steps"></a>Další kroky

Pokud nevidíte svůj problém nebo ho nemůžete vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů.

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
