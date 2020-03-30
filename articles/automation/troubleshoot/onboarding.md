---
title: Řešení potíží s řešením pro správu Azure Automation
description: Zjistěte, jak řešit chyby při zařazování pomocí řešení Správa aktualizací, Sledování změn a Inventář
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c949556949e0c187d7c23c4dd32436e245bfbb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75889336"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Poradce při potížích při zařazování správy aktualizací, sledování změn a inventáře

Při řešeních registrace, jako je správa aktualizací nebo sledování změn a inventář, se může vyskytnut počet chyb. Tento článek popisuje různé chyby, které mohou nastat a jak je vyřešit.

## <a name="known-issues"></a>Známé problémy

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scénář: Přejmenování registrovaného uzlu vyžaduje znovu zrušit registraci / registraci.

#### <a name="issue"></a>Problém

Uzel je registrován na Azure Automation a potom se změní název počítače operačního systému.  Sestavy z uzlu se nadále zobrazují s původním názvem.

#### <a name="cause"></a>Příčina

Přejmenování registrovaných uzlů neaktualizuje název uzlu v Azure Automation.

#### <a name="resolution"></a>Řešení

Zrušení registrace uzlu z konfigurace stavu azure automatizace a pak jej znovu zaregistrovat.  Zprávy publikované do služby před tímto časem již nebudou k dispozici.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scénář: Opětovné podepisování certifikátů přes proxy server https není podporováno.

#### <a name="issue"></a>Problém

Zákazníci hlásili, že při připojení prostřednictvím proxy řešení, které ukončí přenoshttps a pak znovu šifruje provoz pomocí nového certifikátu, služba neumožňuje připojení.

#### <a name="cause"></a>Příčina

Azure Automation nepodporuje znovu podpisové certifikáty používané k šifrování provozu.

#### <a name="resolution"></a>Řešení

Pro tento problém neexistuje alternativní řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scénář: Registrace se nezdaří se zprávou – řešení nelze povolit

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí jedna z následujících zpráv:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobena nesprávnými nebo chybějícími oprávněními ve virtuálním počítači, pracovním prostoru nebo pro uživatele.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správná oprávnění k připojení virtuálního počítače. Zkontrolujte [oprávnění potřebná k nasazení počítačů](../automation-role-based-access-control.md#onboarding) a pokuste se znovu zadat řešení. Pokud se zobrazí `The solution cannot be enabled on this VM because the permission to read the workspace is missing`chyba , `Microsoft.OperationalInsights/workspaces/read` ujistěte se, že máte oprávnění k nalezení, pokud je virtuální počítač na palubě do pracovního prostoru.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scénář: Registrace se nezdaří se zprávou – nepodařilo se nakonfigurovat účet automatizace pro protokolování diagnostiky.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí následující zpráva:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena, pokud cenová úroveň neodpovídá fakturačnímu modelu předplatného. Další informace najdete [v tématu Sledování využití a odhadovaných nákladů ve službě Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Řešení

Vytvořte pracovní prostor Log Analytics ručně a opakujte proces registrace a vyberte vytvořený pracovní prostor.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scénář: Chyba computergroupqueryformaterror

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že uložený dotaz skupiny vyhledávacích počítačů použitý k cílení na řešení nebyl správně formátován. 

#### <a name="cause"></a>Příčina

Pravděpodobně jste změnili dotaz nebo byl změněn systémem.

#### <a name="resolution"></a>Řešení

Můžete odstranit dotaz pro toto řešení a reonboard řešení, které znovu vytvoří dotaz. Dotaz najdete v pracovním prostoru v části **Uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**a kategorie dotazu je název řešení přidruženého k tomuto dotazu. Pokud je povoleno více řešení, **microsoftdefaultcomputergroup** se zobrazí vícekrát v části **Uložená hledání**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu porušení jedné nebo více zásad.

#### <a name="cause"></a>Příčina 

Zásada je na místě, která blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Chcete-li úspěšně nasadit řešení, je třeba zvážit změnu uvedené zásady. Vzhledem k tomu, že existuje mnoho různých typů zásad, které lze definovat, konkrétní požadované změny závisí na zásadách, které jsou porušeny. Pokud byla například definována zásada pro skupinu prostředků, která odepřela oprávnění ke změně obsahu určitých typů prostředků v rámci této skupiny prostředků, můžete například provést některou z následujících akcí:

* Odeberte zásady úplně.
* Pokuste se nastoupit do jiné skupiny prostředků.
* Revidovat zásady, například takto:
  * Opětovné zacílení zásady na konkrétní prostředek (například na konkrétní účet automatizace).
  * Přehodnoce sady prostředků, které byly zásady nakonfigurovány tak, aby je odepřela.

Zkontrolujte oznámení v pravém horním rohu portálu Azure nebo přejděte do skupiny prostředků, která obsahuje váš účet automatizace a vyberte **nasazení** v části **Nastavení,** chcete-li zobrazit neúspěšné nasazení. Další informace o azure [zásadách](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)najdete na webu: Přehled zásad Azure .

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scénář: Chyby při pokusu o odpojení pracovního prostoru

#### <a name="issue"></a>Problém

Při pokusu o odpojení pracovního prostoru se zobrazí následující chyba:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud máte stále aktivní řešení v pracovním prostoru Log Analytics, která závisí na propojení vašeho účtu automation a log analytics.

### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, budete muset z pracovního prostoru odebrat následující řešení, pokud je používáte:

* Update Management
* Sledování změn
* Spuštění/zastavení virtuálních počítačů mimo špičku

Po odebrání řešení můžete pracovní prostor odpojit. Je důležité vyčistit všechny existující artefakty z těchto řešení z vašeho pracovního prostoru a účet automatizace také.  

* Update Management
  * Odebrání nasazení aktualizací (plánů) z účtu automatizace
* Spuštění/zastavení virtuálních počítačů mimo špičku
  * Odstraňte všechny zámky na součásti řešení v účtu automatizace v části **Zámky** > **nastavení**.
  * Další kroky k odebrání spuštění a zastavení virtuálních mích během mimo pracovní dobu řešení, viz [Odebrání spuštění a zastavení virtuálního virtuálního mísa během mimo pracovní dobu řešení](../automation-solution-vm-management.md#remove-the-solution).

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Selhání rozšíření MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Při nasazování řešení se nasazují různé související prostředky. Jedním z těchto prostředků je Microsoft Monitoring Agent Rozšíření nebo Log Analytics agent pro Linux. Jedná se o rozšíření virtuálních strojů nainstalovaná agentem hosta virtuálního počítače, který je zodpovědný za komunikaci s nakonfigurovaným pracovním prostorem Log Analytics za účelem pozdější koordinace stahování binárních souborů a dalších souborů, které řešení, na které se zapisujete, závisí, jakmile začne s exekucí.
Obvykle se nejprve dozvíte o agentovi MMA nebo Log Analytics pro selhání instalace Linuxu z oznámení, které se zobrazí v centru Oznámení. Kliknutím na toto oznámení získáte další informace o konkrétním selhání. Navigace k prostředku skupiny prostředků a potom na nasazení elementu v něm také obsahuje podrobnosti o selhání nasazení, ke kterým došlo.
Instalace agenta MMA nebo Log Analytics pro Linux může selhat z různých důvodů a kroky k řešení těchto chyb se liší v závislosti na problému. Postupujte podle konkrétních kroků pro řešení potíží.

Následující část popisuje různé problémy, které můžete narazit při zapisování, které způsobují selhání v nasazení rozšíření MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scénář: Během požadavku webového klienta došlo k výjimce.

Rozšíření MMA ve virtuálním počítači nemůže komunikovat s externími prostředky a nasazení se nezdaří.

#### <a name="issue"></a>Problém

Následují příklady vrácených chybových zpráv:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Příčina

Některé potenciální příčiny této chyby jsou:

* Ve virtuálním počítače je nakonfigurovaný proxy server, který povoluje jenom konkrétní porty.

* Nastavení brány firewall zablokovalo přístup k požadovaným portům a adresám.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správné porty a adresy otevřené pro komunikaci. Seznam portů a adres naleznete v [tématu Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scénář: Instalace se nezdařila z důvodu přechodných problémů s prostředím.

Instalace rozšíření Microsoft Monitoring Agent se nezdařila během nasazení z důvodu jiné instalace nebo akce blokující instalaci

#### <a name="issue"></a>Problém

Následují příklady chybových zpráv mohou být vráceny:

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

Některé potenciální příčiny této chyby jsou:

* Probíhá další instalace
* Systém se spustí restartovat během nasazení šablony

#### <a name="resolution"></a>Řešení

Tato chyba je přechodná chyba v přírodě. Opakujte nasazení k instalaci rozšíření.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scénář: Časový limit instalace

Instalace rozšíření MMA nebyla dokončena z důvodu časového oddlužení.

#### <a name="issue"></a>Problém

Následující příklad je chybová zpráva, která může být vrácena:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

K této chybě dochází, protože virtuální počítač je pod velkým zatížením během instalace.

### <a name="resolution"></a>Řešení

Pokuste se nainstalovat rozšíření MMA, když je virtuální měna pod nižším zatížením.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
