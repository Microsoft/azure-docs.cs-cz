---
title: Řešení potíží s chyby registrace Update Management, Change Tracking a Inventory
description: Zjistěte, jak řešit potíže s Update Management, Change Tracking a Inventory řešení chybami připojení
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 78e78bc019ab5f8be1cfd3448220b97b89cde6a5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228776"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Řešení chyb při registraci řešení

Pravděpodobně narazíte na chyby při připojování řešení, jako jsou Update Management nebo Change Tracking a Inventory. Tento článek popisuje různé chyby, které mohou nastat a způsob jejich řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="missing-write-permissions"></a>Scénář: Registrace selže se zprávou – řešení není možné.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače do řešení zobrazí následující zpráva:

```
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobena nesprávnou nebo chybějící oprávnění na virtuálním počítači nebo pro uživatele.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správná oprávnění k připojení virtuálního počítače. Zkontrolujte [práva potřebná k připojení počítačů](../automation-role-based-access-control.md#onboarding) a připojit řešení znovu.

### <a name="computer-group-query-format-error"></a>Scénář: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že uložený vyhledávací dotaz skupiny počítače používají k zaměření řešení nemá správný formát. 

#### <a name="cause"></a>Příčina

Můžete změnit dotaz, nebo může byla změněna systému.

#### <a name="resolution"></a>Řešení

Dotaz pro toto řešení a reonboard řešení, která znovu vytvoří dotaz, můžete odstranit. Dotaz můžete najít v rámci pracovního prostoru v části **uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**, a název řešení spojených s Tento dotaz je kategorie dotazu. Pokud je povoleno více řešení, **MicrosoftDefaultComputerGroup** zobrazuje více než jednou v rámci **uložená hledání**.

### <a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu porušení zásady jednoho nebo více.

#### <a name="cause"></a>Příčina 

Zásady je na místě, které blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Pokud chcete úspěšně nasadit řešení, je potřeba zvážit změnu označený zásad. Jsou různé druhy zásad, které lze definovat konkrétní změny požadované závisí na zásadu, která je porušena. Například pokud zásada byla definována na skupinu prostředků, která odepřena oprávnění ke změně obsahu určité typy prostředků v rámci této skupiny prostředků, můžete je například provedete kterýkoli z následujících:

* Úplně odeberte zásady.
* Pokuste se připojit k jiné skupině prostředků.
* Upravit zásady, tím, k například:
  * Změnu cíle zásad do konkrétního prostředku (například za účelem konkrétní účet Automation).
  * Úprava sady prostředků této zásadě byl konfigurován k odepření.

Zkontrolovat oznámení v pravém horním rohu webu Azure portal nebo přejděte do skupiny prostředků obsahující účet automation a vyberte **nasazení** pod **nastavení** zobrazíte neúspěšný nasazení. Další informace o službě Azure Policy najdete v tématu: [Přehled služby Azure Policy](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Chyby rozšíření agenta MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Při nasazení řešení, jsou nasazené širokou škálu související prostředky. Jeden z těchto prostředků je rozšíření Microsoft Monitoring Agent nebo Log Analytics agenta pro Linux. Jedná se o rozšíření virtuálního počítače nainstalovat agenta hosta virtuálního počítače, který zodpovídá za komunikaci s nakonfigurovaný pracovní prostor Log Analytics pro účely novější koordinaci stahování binárních souborů a další soubory, které řešení se registrace závisí na po zahájení provádění.
Je obvykle nejprve zaregistrují agenta MMA nebo Log Analytics pro Linux chyby při instalaci z oznámení v centru oznámení. Kliknutím na toto oznámení obsahuje další informace o toto specifické selhání. Navigace na prostředek skupiny prostředků a potom na element nasazení v ní také poskytuje podrobné informace o selhání nasazení, ke kterým došlo.
Instalace agenta MMA nebo Log Analytics pro Linux může selhat z různých důvodů a kroky pro řešení těchto chyb lišit v závislosti na problému. Postupujte podle konkrétní postup řešení potíží.

Následující část popisuje různé problémy, které se můžete setkat při připojování, způsobit selhání nasazení rozšíření agenta MMA.

### <a name="webclient-exception"></a>Scénář: Během požadavku WebClient došlo k výjimce

Rozšíření agenta MMA na virtuálním počítači se nemůže komunikovat s externím prostředkům a nasazení se nezdaří.

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy, které jsou vráceny:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Příčina

Některé možné příčiny této chyby jsou:

* Je proxy server nakonfigurovaný na virtuálním počítači, povolující jen určité porty.

* Nastavení brány firewall má zablokovaný přístup k požadované porty a adresy.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správné porty a adresy otevřít pro komunikaci. Seznam portů a adres najdete v tématu [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénář: Instalace se nezdařila z důvodu chyby přechodné prostředí

Během nasazení, protože jiná instalace nebo akce, které blokuje instalaci se nepovedlo nainstalovat rozšíření Microsoft Monitoring Agent

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy mohou být vráceny:

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

Některé možné příčiny této chyby jsou:

* Probíhá jiná instalace
* Systém se aktivovalo se restartování během nasazování šablony

#### <a name="resolution"></a>Řešení

Tato chyba je přechodná chyba ze své podstaty. Pokusem o nasazení k instalaci rozšíření.

### <a name="installation-timeout"></a>Scénář: Časový limit instalace

Instalace agenta MMA rozšíření nebyla dokončena z důvodu vypršení časového limitu.

#### <a name="issue"></a>Problém

Následuje příklad, který může být vrácen chybovou zprávu:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobená virtuálního počítače se v případě velkého zatížení během instalace.

### <a name="resolution"></a>Řešení

Pokus o instalaci rozšíření agenta MMA, při nižší zatížení virtuálního počítače.

## <a name="next-steps"></a>Další postup

Pokud nenalezli váš problém nebo nepovedlo se vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
