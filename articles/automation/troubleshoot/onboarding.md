---
title: Řešení chyb registrace správy aktualizací, sledování změn a inventáře
description: Zjistěte, jak vyřešit chyby registrace pomocí správy aktualizací, sledování změn a inventáře řešení
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063780"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Řešení chyb při registraci řešení

Pravděpodobně narazíte na chyby při připojování řešení jako aktualizace nebo změna sledování inventáře a správy. Tento článek popisuje různé chybách, ke kterým může dojít a způsob jejich řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="computer-grou-query-format-error"></a>Scénář: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že dotaz skupiny uloženého hledání počítače používají k zaměření řešení nebyla správně naformátována. 

#### <a name="cause"></a>Příčina

Můžete změnit dotaz, nebo může byla změněna systému.

#### <a name="resolution"></a>Řešení

Dotaz pro toto řešení a reonboard řešení, které vytvoří dotaz, můžete odstranit. Dotaz naleznete v rámci pracovního prostoru, v části **uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**, a kategorie dotazu je název řešení přiřazený k tomuto dotazu. Pokud jsou povolené více řešení, **MicrosoftDefaultComputerGroup** zobrazuje vícekrát pod **uložená hledání**.

### <a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu narušení jedné nebo více zásad.

#### <a name="cause"></a>Příčina 

Zásady je na místě, která blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Chcete-li úspěšně nasadit řešení, je potřeba zvážit, změna zásad uvedené. Jsou mnoho různých typů zásad, které lze definovat určité změny požadované závisí na zásadu, která je došlo k porušení. Například pokud zásady definované ve skupinu prostředků, který má odepřena oprávnění pro změnu obsahu určitých typů prostředků v rámci dané skupiny prostředků, můžete to, například udělat některé z následujících:

* Úplně odeberte zásady.
* Pokuste se připojit k jiné skupině prostředků.
* Zkontrolovat, jestli zásady, tím, například:
  * Znovu cílení zásad pro konkrétní prostředek (například za účelem určitého účtu Automation).
  * Úprava sady prostředků zásad byl konfigurován k odepření.

Zkontrolujte oznámení v pravém horním rohu portálu Azure nebo přejděte do skupiny prostředků, která obsahuje váš účet automation a vyberte **nasazení** pod **nastavení** zobrazíte neúspěšný nasazení. Další informace o návštěvě zásad Azure: [přehled zásad Azure](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Selhání MMA rozšíření

Při nasazení řešení, jsou nasazené celou řadu související prostředky. Jeden z těchto prostředků je rozšíření agenta Microsoft Monitoring nebo OMS agenta pro Linux. Toto jsou rozšíření virtuálního počítače nainstalovat agenta hosta virtuálního počítače, který zodpovídá za komunikaci s nakonfigurovanou pracovního prostoru Operations Management Suite (OMS), za účelem stahování binárních souborů novější koordinaci a Další soubory, které řešení jsou registrace závisí na jednou se zahájí spuštění.
Je obvykle nejprve zaregistrují MMA nebo OMS agenta pro Linux selhání instalace z oznámení zobrazí v centru oznámení. Kliknutím na oznámení, že poskytuje další informace o konkrétní chybě. Navigace na prostředek skupiny prostředků a pak do elementu nasazení v něm také podrobné informace o selhání nasazení, které došlo k chybě.
Pro celou řadu důvodů může selhat instalace MMA nebo OMS agenta pro Linux a postup vedoucí k vyřešení selhání liší v závislosti na problém. Postupujte podle konkrétní řešení potíží.

Následující část popisuje různé problémy, které se můžete setkat při registraci, způsobit selhání nasazení rozšíření MMA.

### <a name="webclient-exception"></a>Scénář: Došlo k výjimce během požadavku WebClient

Rozšíření MMA na virtuálním počítači se nepodařilo komunikovat s externím prostředkům a nasazení selže.

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy, které jsou vráceny:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Příčina

K této chybě některé možné příčiny jsou:

* Je proxy server nakonfigurovaný ve virtuálním počítači, který umožňuje pouze určité porty.

* Nastavení brány firewall zablokoval přístup k požadované porty a adresy.

#### <a name="resolution"></a>Řešení

Zkontrolujte, zda máte správné porty a adresy otevřete pro komunikaci. Seznam portů a adres najdete v tématu [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénář: Instalace se nepovedla kvůli přechodným prostředí problémy

Instalace agenta Microsoft Monitoring Agent rozšíření došlo k chybě během nasazení, protože jiná instalace nebo akce blokování instalace

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy mohou být vráceny:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Příčina

K této chybě některé možné příčiny jsou:

* Probíhá jiná instalace
* Systém je byla aktivována restartování během nasazení šablony

#### <a name="resolution"></a>Řešení

Tato chyba je přechodná chyba ve své podstatě. Zopakujte nasazení k instalaci rozšíření.

### <a name="installation-timeout"></a>Scénář: Instalace vypršení časového limitu

Instalace rozšíření MMA nebyla dokončena z důvodu vypršení časového limitu.

#### <a name="issue"></a>Problém

Následuje příklad chybovou zprávu, která mohou být vráceny:

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

Tato chyba je kvůli virtuálního počítače se v případě velkého zatížení během instalace.

### <a name="resolution"></a>Řešení

Pokuste se nainstalovat rozšíření MMA, při nižší zatížení virtuálního počítače.

## <a name="next-steps"></a>Další postup

Pokud váš problém nenalezli nebo nemůžete vyřešit problém, navštíví některý z následujících kanály pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
