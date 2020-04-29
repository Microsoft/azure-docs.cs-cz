---
title: Řešení potíží při připojování řešení pro správu Azure Automation
description: Naučte se řešit chyby při připojování řešení.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679237"
---
# <a name="troubleshoot-solution-onboarding"></a>Řešení potíží s připojováním k řešení

Při připojování řešení Update Management nebo řešení Change Tracking a inventáře může docházet k chybám. Tento článek popisuje různé chyby, ke kterým může dojít, a způsob jejich řešení.

## <a name="known-issues"></a>Známé problémy

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scénář: přejmenování registrovaného uzlu vyžaduje zrušení registrace nebo registrace znovu.

#### <a name="issue"></a>Problém

Uzel je zaregistrován pro Azure Automation a pak se změní název počítače s operačním systémem. Sestavy z uzlu se budou dál zobrazovat s původním názvem.

#### <a name="cause"></a>Příčina

Přejmenování registrovaných uzlů neaktualizuje název uzlu v Azure Automation.

#### <a name="resolution"></a>Řešení

Zrušte registraci uzlu v konfiguraci stavu Azure Automation a pak ho znovu zaregistrujte. Sestavy publikované do služby již nebudou k dispozici.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scénář: Opětovné podepisování certifikátů prostřednictvím proxy serveru HTTPS se nepodporuje.

#### <a name="issue"></a>Problém

Při připojení prostřednictvím řešení proxy, které ukončí provoz HTTPS a pak znovu zašifruje provoz pomocí nového certifikátu, služba nepovoluje připojení.

#### <a name="cause"></a>Příčina

Azure Automation nepodporuje opakované podepisování certifikátů používaných k šifrování provozu.

#### <a name="resolution"></a>Řešení

Pro tento problém není aktuálně k dispozici žádné alternativní řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scénář: připojení se nezdařilo se zprávou – řešení nelze povolit.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí jedna z následujících zpráv:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Příčina

K této chybě dochází v důsledku nesprávných nebo chybějících oprávnění na virtuálním počítači nebo v pracovním prostoru nebo pro uživatele.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správná [oprávnění potřebná k připojování počítačů](../automation-role-based-access-control.md#onboarding-permissions) a pak zkuste řešení připojit znovu. Pokud se zobrazí chyba `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, ujistěte se, že máte `Microsoft.OperationalInsights/workspaces/read` oprávnění, abyste mohli zjistit, jestli je virtuální počítač připojen k pracovnímu prostoru.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scénář: připojování se nepovede s touto zprávou: Nepodařilo se nakonfigurovat účet Automation pro protokolování diagnostiky.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí následující zpráva:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena tím, že cenová úroveň neodpovídá modelu fakturace předplatného. Viz téma [monitorování využití a odhadované náklady v Azure monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Řešení

Vytvořte pracovní prostor Log Analytics ručně a zopakováním procesu připojování vyberte pracovní prostor, který jste vytvořili.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scénář: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že dotaz na skupinu uloženého hledání, který se používá k cílení na řešení, není správně naformátován. 

#### <a name="cause"></a>Příčina

Je možné, že jste dotaz změnili nebo ho systém mohl změnit.

#### <a name="resolution"></a>Řešení

Dotaz pro řešení můžete odstranit a pak znovu připojit řešení, které dotaz znovu vytvoří. Dotaz najdete v části **uložená hledání**v pracovním prostoru. Název dotazu je **MicrosoftDefaultComputerGroup**a kategorie dotazu je název přidruženého řešení. Pokud je povolené více řešení, dotaz **MicrosoftDefaultComputerGroup** se v části **uložená hledání**zobrazuje vícekrát.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby označuje, že nasazení se nezdařilo z důvodu porušení jedné nebo více zásad.

#### <a name="cause"></a>Příčina 

Zásada blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Aby bylo možné řešení úspěšně nasadit, je nutné zvážit změnu označených zásad. V případě, že existuje mnoho různých typů zásad, které je možné definovat, závisí požadované změny na zásadách, které jsou porušené. Pokud je například zásada definovaná ve skupině prostředků, která Odepře oprávnění ke změně obsahu některých obsažených prostředků, můžete zvolit jednu z těchto oprav:

* Zásadu odeberte úplně.
* Zkuste řešení připojit do jiné skupiny prostředků.
* Znovu Zaměřte zásadu na konkrétní prostředek, například účet Automation.
* Revidujte sadu prostředků, pro které je zásada nakonfigurovaná na odepřít.

Zkontrolujte oznámení v pravém horním rohu Azure Portal nebo přejděte do skupiny prostředků, která obsahuje váš účet Automation, a v části **Nastavení** vyberte **nasazení** . zobrazí se neúspěšné nasazení. Další informace o Azure Policy najdete v tématu [přehled Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scénář: Chyby při pokusu o odpojení pracovního prostoru

#### <a name="issue"></a>Problém

Při pokusu o odpojení pracovního prostoru se zobrazí následující chyba:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Příčina

K této chybě dochází, když máte pořád aktivní řešení v pracovním prostoru Log Analytics, který závisí na vašem účtu Automation a na propojených pracovních prostorech Log Analytics.

### <a name="resolution"></a>Řešení

Pokud je používáte, odeberte z pracovního prostoru následující řešení:

* Update Management
* Change Tracking a Inventory
* Spuštění/zastavení virtuálních počítačů mimo špičku

Po odebrání řešení můžete zrušit propojení pracovního prostoru. Z těchto řešení je důležité vyčistit všechny existující artefakty z vašeho pracovního prostoru a účtu Automation. 

* V případě Update Management z účtu Automation odeberte nasazení aktualizací (plány).
* V případě Start/Stop VMS during off-hours odeberte všechny zámky na součástech řešení v účtu Automation v části **Nastavení** > **zámků**. Viz [Odebrání řešení Start/Stop VMS during off-hours](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics pro chyby rozšíření Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Instalace rozšíření Log Analytics agenta pro Windows může selhat z nejrůznějších důvodů. Následující část popisuje problémy s připojováním, které mohou způsobit chyby během nasazování rozšíření Log Analytics agenta pro Windows.

>[!NOTE]
>Log Analytics Agent pro Windows je název, který se aktuálně používá v Azure Automation pro Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scénář: během žádosti WebClient došlo k výjimce.

Rozšíření Log Analytics pro Windows na virtuálním počítači nemůže komunikovat s externími prostředky a nasazení se nepodaří.

#### <a name="issue"></a>Problém

Následují příklady chybových zpráv, které se vrátí:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Příčina

Mezi možné příčiny této chyby patří:

* Proxy server nakonfigurovaný na virtuálním počítači povoluje pouze konkrétní porty.
* Nastavení brány firewall zablokovalo přístup k požadovaným portům a adresám.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správné porty a adresy otevřené pro komunikaci. Seznam portů a adres najdete v tématu [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scénář: instalace se nezdařila kvůli problémům s přechodným prostředím

Instalace rozšíření Log Analytics pro Windows selhala během nasazení z důvodu jiné instalace nebo akce blokující instalaci.

#### <a name="issue"></a>Problém

Níže jsou uvedeny příklady chybových zpráv:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Příčina

Mezi možné příčiny této chyby patří:

* Probíhá jiná instalace.
* Systém se aktivuje při nasazování šablony do restartování.

#### <a name="resolution"></a>Řešení

Tato chyba má přechodný charakter. Opakujte nasazení pro instalaci rozšíření.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scénář: časový limit instalace

Instalace rozšíření Log Analytics pro Windows se nedokončila kvůli vypršení časového limitu.

#### <a name="issue"></a>Problém

Následuje příklad chybové zprávy, která může být vrácena:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

K tomuto typu chyby dochází, protože během instalace se virtuální počítač nachází v silné zátěži.

### <a name="resolution"></a>Řešení

Zkuste nainstalovat rozšíření Log Analytics agenta pro Windows, když je virtuální počítač pod nižší zátěží.

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
