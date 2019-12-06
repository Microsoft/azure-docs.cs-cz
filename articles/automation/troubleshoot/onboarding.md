---
title: Řešení chyb při registraci Update Management, Change Tracking a inventáře
description: Naučte se řešit chyby při připojování pomocí řešení Update Management, Change Tracking a inventáře.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 0371c59ae63389bc3f7f0132260b0d98f496086c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849305"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Řešení chyb při připojování řešení

Při připojování řešení, jako je Update Management nebo Change Tracking a inventáře, může dojít k chybám. Tento článek popisuje různé chyby, ke kterým může dojít, a způsob jejich řešení.

## <a name="known-issues"></a>Známé problémy

### <a name="node-rename"></a>Scénář: přejmenování registrovaného uzlu vyžaduje zrušení registrace nebo registrace znovu.

#### <a name="issue"></a>Problém

Uzel je zaregistrován pro Azure Automation a pak se změní název počítače operačního systému.  Sestavy z uzlu se budou dál zobrazovat s původním názvem.

#### <a name="cause"></a>Příčina

Přejmenování registrovaných uzlů neaktualizuje název uzlu v Azure Automation.

#### <a name="resolution"></a>Rozlišení

Zrušte registraci uzlu v konfiguraci stavu Azure Automation a pak ho znovu zaregistrujte.  Sestavy publikované do služby již nebudou k dispozici.


### <a name="resigning-cert"></a>Scénář: Opětovné podepisování certifikátů prostřednictvím proxy serveru HTTPS se nepodporuje.

#### <a name="issue"></a>Problém

Zákazníci oznámili, že při připojení prostřednictvím řešení proxy, které ukončí provoz https a pak znovu zašifruje provoz pomocí nového certifikátu, služba nepovoluje připojení.

#### <a name="cause"></a>Příčina

Azure Automation nepodporuje opakované podepisování certifikátů používaných k šifrování provozu.

#### <a name="resolution"></a>Rozlišení

Pro tento problém neexistuje alternativní řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="missing-write-permissions"></a>Scénář: připojení se nezdařilo se zprávou – řešení nelze povolit.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí jedna z následujících zpráv:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobena nesprávnými nebo chybějícími oprávněními pro virtuální počítač, pracovní prostor nebo pro uživatele.

#### <a name="resolution"></a>Rozlišení

Ujistěte se, že máte správná oprávnění k připojování virtuálního počítače. Zkontrolujte [oprávnění potřebná k zprovoznění počítačů](../automation-role-based-access-control.md#onboarding) a pokuste se řešení připojit znovu. Pokud se zobrazí chyba `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, ujistěte se, že máte oprávnění `Microsoft.OperationalInsights/workspaces/read`, abyste mohli zjistit, jestli se virtuální počítač připojil k pracovnímu prostoru.

### <a name="diagnostic-logging"></a>Scénář: připojování se nepovedlo, zpráva – nepovedlo se nakonfigurovat účet Automation pro diagnostické protokolování.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí následující zpráva:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena tím, že cenová úroveň neodpovídá modelu fakturace předplatného. Další informace najdete v tématu [monitorování využití a odhadované náklady v Azure monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Rozlišení

Vytvořte pracovní prostor Log Analytics ručně a zopakováním procesu připojování vyberte pracovní prostor, který jste vytvořili.

### <a name="computer-group-query-format-error"></a>Scénář: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že dotaz na skupinu uloženého hledání, který se používá k cílení na řešení, nebyl správně naformátován. 

#### <a name="cause"></a>Příčina

Je možné, že jste dotaz změnili nebo mohl být změněn systémem.

#### <a name="resolution"></a>Rozlišení

Dotaz pro toto řešení můžete odstranit a reonboard řešení, které dotaz znovu vytvoří. Dotaz najdete v pracovním prostoru v části **uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**a kategorie dotazu je název řešení přidruženého k tomuto dotazu. Pokud je povolené víc řešení, **MicrosoftDefaultComputerGroup** se v **uložených hledáních**zobrazuje víckrát.

### <a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu porušení jedné nebo více zásad.

#### <a name="cause"></a>Příčina 

Je zavedena zásada, která blokuje dokončení operace.

#### <a name="resolution"></a>Rozlišení

Aby bylo řešení úspěšně nasazeno, je třeba zvážit změnu označených zásad. V případě, že existuje mnoho různých typů zásad, které je možné definovat, závisí konkrétní požadované změny na zásadách, které jsou porušené. Pokud jste například v rámci skupiny prostředků definovali zásadu, která odepřela oprávnění ke změně obsahu určitých typů prostředků v této skupině prostředků, můžete například provést některou z následujících akcí:

* Zásadu odeberte úplně.
* Pokuste se připojit do jiné skupiny prostředků.
* Upravte zásady, například:
  * Změna cílení zásad na konkrétní prostředek (například na konkrétní účet Automation).
  * Kontrola sady prostředků, u kterých byla zásada nakonfigurována pro odepření.

Zkontrolujte oznámení v pravém horním rohu Azure Portal nebo přejděte do skupiny prostředků, která obsahuje váš účet Automation, a v části **Nastavení** vyberte **nasazení** . zobrazí se neúspěšné nasazení. Další informace o Azure Policy najdete tady: [přehled Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Scénář: Chyby při pokusu o odpojení pracovního prostoru

#### <a name="issue"></a>Problém

Při pokusu o odpojení pracovního prostoru se zobrazí následující chyba:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Příčina

K této chybě dochází, když máte pořád aktivní řešení v pracovním prostoru Log Analytics, který závisí na vašem účtu Automation a na propojených pracovních prostorech analýzy log.

### <a name="resolution"></a>Rozlišení

Pokud je chcete vyřešit, budete muset z pracovního prostoru odebrat následující řešení, pokud je používáte:

* Správa aktualizací
* Sledování změn
* Spuštění/zastavení virtuálních počítačů mimo špičku

Po odebrání řešení můžete zrušit propojení pracovního prostoru. Z těchto řešení je důležité vyčistit všechny existující artefakty i z vašeho pracovního prostoru a účtu Automation.  

* Správa aktualizací
  * Odeberte nasazení aktualizací (plány) z účtu Automation.
* Spuštění/zastavení virtuálních počítačů mimo špičku
  * V části **nastavení** > **zámky**odeberte všechny zámky v součástech řešení v účtu Automation.
  * Další kroky pro odebrání řešení Start/Stop VMs during off-hours najdete v tématu [Odebrání virtuálního počítače spustit/zastavit během doby mimo špičku](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>Selhání rozšíření MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Při nasazování řešení se nasadí celá řada souvisejících prostředků. Jedním z těchto prostředků je rozšíření Microsoft Monitoring Agent nebo agent pro Log Analytics pro Linux. Jedná se o rozšíření virtuálních počítačů nainstalované agentem hosta virtuálního počítače, který zodpovídá za komunikaci s nakonfigurovaným pracovním prostorem Log Analytics pro účely pozdější koordinace stahování binárních souborů a dalších souborů. řešení, ke kterému se připojujete, závisí na tom, kdy začne provádění.
Obvykle se můžete seznámit s MMA nebo Log Analytics agenta pro chyby instalace pro Linux z oznámení zobrazeného v centru oznámení. Kliknutím na toto oznámení získáte další informace o konkrétním selhání. Navigace do prostředku skupiny prostředků a potom do prvku nasazení v rámci něj také uvádí podrobnosti o selháních nasazení, ke kterým došlo.
Instalace agenta MMA nebo Log Analytics pro Linux se může z různých důvodů zdařit a kroky, které je potřeba provést při řešení těchto chyb, se liší v závislosti na problému. Projděte si konkrétní kroky pro řešení potíží.

V následující části jsou popsány různé problémy, které můžete při připojování k chybě způsobit při nasazení rozšíření MMA.

### <a name="webclient-exception"></a>Scénář: během žádosti WebClient došlo k výjimce.

Rozšíření MMA ve virtuálním počítači nemůže komunikovat s externími prostředky a nasazení se nepodaří.

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

* Ve virtuálním počítači je nakonfigurovaný proxy server, který povoluje jenom konkrétní porty.

* Nastavení brány firewall zablokovalo přístup k požadovaným portům a adresám.

#### <a name="resolution"></a>Rozlišení

Ujistěte se, že máte správné porty a adresy otevřené pro komunikaci. Seznam portů a adres najdete v tématu [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénář: instalace se nezdařila kvůli problémům s přechodným prostředím

Instalace rozšíření Microsoft Monitoring Agent během nasazení selhala kvůli jiné instalaci nebo akci blokující instalaci.

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

#### <a name="resolution"></a>Rozlišení

Tato chyba je v podstatě přechodná chyba. Opakujte nasazení pro instalaci rozšíření.

### <a name="installation-timeout"></a>Scénář: časový limit instalace

Instalace rozšíření MMA se nedokončila kvůli vypršení časového limitu.

#### <a name="issue"></a>Problém

Následující příklad je chybová zpráva, která může být vrácena:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

K této chybě dochází, protože během instalace je virtuální počítač v rámci vysoké zátěže.

### <a name="resolution"></a>Rozlišení

Pokuste se nainstalovat rozšíření MMA, když je virtuální počítač pod nižší zátěží.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
