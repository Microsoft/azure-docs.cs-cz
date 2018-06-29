---
title: Řešení chyb pomocí správy aktualizací
description: Zjistěte, jak vyřešit problémy s správy aktualizací
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063817"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží s správy aktualizací

Tento článek popisuje řešení problémů, které mohou nastat při použití správy aktualizací

## <a name="windows"></a>Windows

Pokud dojde k potížím při pokusu o zařadit do řešení na virtuálním počítači, zkontrolujte **nástroje Operations Manager** protokolu událostí v části **protokoly aplikací a služeb** v místním počítači pro události se ID události **4502** a událostí zpráva obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

V následující části jsou zdůrazněné specifické chybové zprávy a možným řešením pro každou. Pro ostatní registrace najdete v části problémy, [řešení potíží s řešení registrace](onboarding.md).

### <a name="machine-already-registered"></a>Scénář: Počítač je již zaregistrována k jinému účtu

#### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Příčina

Tento počítač je již zařazený, nemá do jiného pracovního prostoru pro správu aktualizací.

#### <a name="resolution"></a>Řešení

Vyčistit stará artefakty na počítače pomocí [odstraňuje se skupina hybridních runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) a zkuste to znovu.

### <a name="machine-unable-to-communicate"></a>Scénář: Počítač se nepodařilo komunikovat se službou

#### <a name="issue"></a>Problém

Zobrazí jednu z následujících chybových zpráv:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Příčina

Může být server proxy, brány nebo firewall blokující komunikaci v síti.

#### <a name="resolution"></a>Řešení

Zkontrolujte vaší sítě a ujistěte se, že jsou povolené příslušné porty a adresy. V tématu [požadavky na síťovou](../automation-hybrid-runbook-worker.md#network-planning), seznam porty a adresy, které jsou vyžadované správy aktualizací a procesy Hybrid Runbook Worker.

### <a name="unable-to-create-selfsigned-cert"></a>Scénář: Nelze vytvořit certifikát podepsaný svým držitelem

#### <a name="issue"></a>Problém

Zobrazí jednu z následujících chybových zpráv:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Příčina

Hybridní pracovní proces Runbooku se nepodařilo vygenerovat certifikát podepsaný svým držitelem

#### <a name="resolution"></a>Řešení

Ověřte systémový účet má přístup pro čtení do složky **C:\ProgramData\Microsoft\Crypto\RSA** a zkuste to znovu.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scénář: Hromadná postupná aktualizace se nepodaří spustit

#### <a name="issue"></a>Problém

Selhání spuštění aktualizace spustit na počítač s Linuxem.

#### <a name="cause"></a>Příčina

Hybridní pracovní proces Linux není v pořádku.

#### <a name="resolution"></a>Řešení

Zkopírujte následující souboru protokolu a zachovat pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scénář: Hromadná postupná aktualizace spustí, ale výskyt chyb

#### <a name="issue"></a>Problém

Hromadná postupná aktualizace spustí, ale výskyt chyb během spuštění.

#### <a name="cause"></a>Příčina

Možných příčin může být:

* Správce balíčků není v pořádku
* Konkrétní balíčky mohou ovlivňovat cloudové opravy
* Z jiných důvodů

#### <a name="resolution"></a>Řešení

Pokud dojde k selhání během aktualizace spustit po úspěšně spustí v systému Linux, zjistíte z úlohy, výstup z napadeného počítače v spustit. Možná ze Správce balíčků váš počítač, který můžete prozkoumat a provést akci pro specifické chybové zprávy. Správa aktualizací vyžaduje správce balíčků jako v pořádku pro nasazení aktualizace úspěšná.

Aktualizace balíčků v některých případech může narušovat aktualizovat správy brání nasazení aktualizací z dokončení. Pokud uvidíte, že, budete muset tyto balíčky vyloučit z budoucí aktualizace spustí nebo je nainstalovat ručně sami.

Pokud oprav problém nelze vyřešit, zkopírujte následující souboru protokolu a zachovat ji **před** další nasazení aktualizace spustí pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Další postup

Pokud váš problém nenalezli nebo nemůžete vyřešit problém, navštíví některý z následujících kanály pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.