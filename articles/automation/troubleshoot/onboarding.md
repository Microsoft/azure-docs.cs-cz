---
title: Řešení potíží s registrací řešení pro správu Azure Automation
description: Přečtěte si, jak řešit chyby při zapisování řešení.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679237"
---
# <a name="troubleshoot-solution-onboarding"></a>Poradce při potížích s připisováním řešení

Při zapisování řešení správy aktualizací nebo řešení sledování změn a zásob se mohou zobrazit chyby. Tento článek popisuje různé chyby, které mohou nastat a jak je vyřešit.

## <a name="known-issues"></a>Známé problémy

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scénář: Přejmenování registrovaného uzlu vyžaduje zrušení registrace nebo opětovné registrace.

#### <a name="issue"></a>Problém

Uzel je zaregistrován na Azure Automation a potom se změní název počítače operačního systému. Sestavy z uzlu se nadále zobrazují s původním názvem.

#### <a name="cause"></a>Příčina

Přejmenování registrovaných uzlů neaktualizuje název uzlu v Azure Automation.

#### <a name="resolution"></a>Řešení

Zrušení registrace uzlu z konfigurace stavu azure automatizace a pak jej znovu zaregistrovat. Zprávy publikované do služby před tímto časem již nebudou k dispozici.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scénář: Opětovné podepisování certifikátů přes proxy server https není podporováno.

#### <a name="issue"></a>Problém

Při připojování prostřednictvím proxy řešení, které ukončí provoz HTTPS a potom znovu zašifruje provoz pomocí nového certifikátu, služba neumožňuje připojení.

#### <a name="cause"></a>Příčina

Azure Automation nepodporuje znovu podpisové certifikáty používané k šifrování provozu.

#### <a name="resolution"></a>Řešení

V současné době neexistuje žádné řešení tohoto problému.

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

Tato chyba je způsobena nesprávnými nebo chybějícími oprávněními na virtuálním počítači nebo pracovním prostoru nebo pro uživatele.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správná [oprávnění potřebná pro palubní počítače](../automation-role-based-access-control.md#onboarding-permissions) a zkuste znovu zadat řešení. Pokud se zobrazí `The solution cannot be enabled on this VM because the permission to read the workspace is missing`chyba , ujistěte se, že máte `Microsoft.OperationalInsights/workspaces/read` oprávnění k nalezení, pokud je virtuální počítač na palubě do pracovního prostoru.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scénář: Registrace se nezdaří se zprávou: Nepodařilo se nakonfigurovat účet automatizace pro protokolování diagnostiky.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače k řešení se zobrazí následující zpráva:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena, pokud cenová úroveň neodpovídá fakturačnímu modelu předplatného. Viz [Sledování využití a odhadované náklady ve službě Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Řešení

Vytvořte pracovní prostor Log Analytics ručně a opakujte proces registrace a vyberte vytvořený pracovní prostor.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scénář: Chyba computergroupqueryformaterror

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že uložený dotaz skupiny vyhledávacích počítačů použitý k cílení na řešení není správně formátován. 

#### <a name="cause"></a>Příčina

Je možné, že jste změnili dotaz nebo jej systém změnil.

#### <a name="resolution"></a>Řešení

Můžete odstranit dotaz pro řešení a potom znovu zadejte řešení, které znovu vytvoří dotaz. Dotaz najdete v pracovním prostoru v části **Uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**a kategorie dotazu je název přidruženého řešení. Pokud je povoleno více řešení, dotaz **MicrosoftDefaultComputerGroup** se v části **Uložená hledání**zobrazí vícekrát .

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby označuje, že nasazení se nezdařilo z důvodu porušení jedné nebo více zásad.

#### <a name="cause"></a>Příčina 

Zásada blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Chcete-li úspěšně nasadit řešení, musíte zvážit změnu uvedené zásady. Vzhledem k tomu, že existuje mnoho různých typů zásad, které lze definovat, požadované změny závisí na zásadách, které jsou porušeny. Pokud je například definována zásada pro skupinu prostředků, která odepře oprávnění ke změně obsahu některých obsažených prostředků, můžete zvolit jednu z těchto oprav:

* Odeberte zásady úplně.
* Pokuste se zavést řešení do jiné skupiny prostředků.
* Znovu zacilte zásadu na konkrétní prostředek, například na účet automatizace.
* Revidovat sadu prostředků, které je nakonfigurován zásady odepřít.

Zkontrolujte oznámení v pravém horním rohu portálu Azure nebo přejděte do skupiny prostředků, která obsahuje váš účet Automation a vyberte **nasazení** v části **Nastavení,** chcete-li zobrazit neúspěšné nasazení. Další informace o zásadách Azure najdete v [tématu Přehled zásad Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scénář: Chyby při pokusu o odpojení pracovního prostoru

#### <a name="issue"></a>Problém

Při pokusu o odpojení pracovního prostoru se zobrazí následující chyba:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud máte stále aktivní řešení v pracovním prostoru Log Analytics, která závisí na vašem účtu Automation a pracovním prostoru Log Analytics, který je propojen.

### <a name="resolution"></a>Řešení

Pokud je používáte, odeberte z pracovního prostoru následující řešení:

* Update Management
* Change Tracking a Inventory
* Spuštění/zastavení virtuálních počítačů mimo špičku

Po odebrání řešení můžete pracovní prostor odpojit. Je důležité vyčistit všechny existující artefakty z těchto řešení z vašeho pracovního prostoru a vašeho účtu automation 

* V případě správy aktualizací odeberte z účtu automatizace nasazení aktualizací (plány).
* Pro spuštění a zastavení virtuálních počítačů během mimo pracovní dobu odeberte všechny zámky na součásti řešení v účtu automatizace v části**Zámky** **nastavení** > . Viz [Odebrání spuštění a zastavení virtuálních měn během mimopracovní ho řešení](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Protokol Analytics pro selhání rozšíření systému Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Instalace agenta Log Analytics pro rozšíření systému Windows může selhat z různých důvodů. Následující část popisuje problémy s přiřazováním, které mohou způsobit chyby během nasazení agenta Log Analytics pro rozšíření systému Windows.

>[!NOTE]
>Agent Log Analytics pro Windows je název, který se aktuálně používá v Azure Automation pro Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scénář: Během požadavku webového klienta došlo k výjimce.

Log Analytics pro Windows rozšíření na virtuálním počítači není schopen komunikovat s externími prostředky a nasazení se nezdaří.

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

* Proxy server nakonfigurovaný ve virtuálním počítače povoluje jenom konkrétní porty.
* Nastavení brány firewall zablokovalo přístup k požadovaným portům a adresám.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správné porty a adresy otevřené pro komunikaci. Seznam portů a adres naleznete v [tématu Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scénář: Instalace se nezdařila z důvodu přechodných problémů s prostředím.

Instalace rozšíření Log Analytics pro systém Windows se nezdařila během nasazení z důvodu jiné instalace nebo akce blokující instalaci

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

* Probíhá jiná instalace.
* Systém se spustí restartovat během nasazení šablony.

#### <a name="resolution"></a>Řešení

Tato chyba je přechodné povahy. Opakujte nasazení k instalaci rozšíření.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scénář: Časový limit instalace

Instalace log Analytics pro windows rozšíření nebylo dokončeno z důvodu časového odčasového časového roku.

#### <a name="issue"></a>Problém

Následuje příklad chybové zprávy, která může být vrácena:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

K tomuto typu chyby dochází, protože virtuální měnový virtuální míchaný je během instalace pod velkým zatížením.

### <a name="resolution"></a>Řešení

Pokuste se nainstalovat agenta Log Analytics pro rozšíření Systému Windows, když je virtuální měna pod nižším zatížením.

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
