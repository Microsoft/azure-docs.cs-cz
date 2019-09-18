---
layout: LandingPage
description: Zjistěte, jak řešit potíže s nasazením virtuálních počítačů.
title: Dokumentace k řešení potíží pro Azure Virtual Machines | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: d7ceb3acb1d2e3d174f3b665ec6210d3ddac9970
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059164"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Řešení potíží virtuálních počítačů Azure

## <a name="tools-for-troubleshooting"></a>Nástroje pro řešení potíží

- [Sériová konzola](serial-console-windows.md)
- [Diagnostika spouštění](boot-diagnostics.md)
- [Virtuální počítač s Windows: Připojení disku s operačním systémem k jinému virtuálnímu počítači pro řešení potíží](troubleshoot-recovery-disks-portal-windows.md)
- [Virtuální počítač s Linuxem: Připojení disku s operačním systémem k jinému virtuálnímu počítači pro řešení potíží](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Nejde se připojit k virtuálnímu počítači

### <a name="windows"></a>Windows

**Běžné řešení**

- [Resetování protokolu RDP](reset-rdp.md)
- [Řešení potíží s protokolem RDP](troubleshoot-rdp-connection.md)
- [Podrobné řešení potíží s protokolem RDP](detailed-troubleshoot-rdp.md)

**Chyby RDP**

- [Chybí licenční server](troubleshoot-rdp-no-license-server.md)
- [Interní ](Troubleshoot-rdp-internal-error.md)
- [Chyby ověřování](troubleshoot-authentication-error-rdp-vm.md)
- [Řešení konkrétních chyb](troubleshoot-specific-rdp-errors.md)

**Chyby spuštění virtuálního počítače**

* [Chyby spuštění nástroje BitLocker](troubleshoot-bitlocker-boot-error.md) 
* [Ve Windows se při spouštění zobrazuje Kontrola systému souborů](troubleshoot-check-disk-boot-error.md)
* [Chyby typu modrá obrazovka](troubleshoot-common-blue-screen-error.md)
* [Spuštění virtuálního počítače se zaseklo na Probíhá příprava Windows](troubleshoot-vm-boot-configure-update.md)
* [Chyba typu SELHÁNÍ KRITICKÉ SLUŽBY na modré obrazovce](troubleshoot-critical-service-failed-boot-error.md)
* [Potíže se smyčkou restartování](troubleshoot-reboot-loop.md)
* [Spuštění virtuálního počítače se zaseklo ve fázi aktualizace Windows](troubleshoot-stuck-updating-boot-error.md)
* [Virtuální počítač se spouští v bezpečném režimu](troubleshoot-rdp-safe-mode.md)

**Ostatní**
- [Resetování hesla virtuálního počítače pro virtuální počítač s Windows v offline režimu](reset-local-password-without-agent.md)
- [Resetování síťové karty po chybné konfiguraci](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Řešení potíží s protokolem SSH](troubleshoot-ssh-connection.md)
- [Podrobné řešení potíží s protokolem SSH](detailed-troubleshoot-ssh-connection.md)
- [Běžné chybové zprávy](error-messages.md)
- [Resetování hesla virtuálního počítače pro virtuální počítač s Linuxem v offline režimu](reset-password.md)

## <a name="vm-deployment-issues"></a>Problémy s nasazením virtuálního počítače

- [Selhání přidělení](allocation-failure.md)
- Znovunasazení virtuálního počítače
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Řešení potíží s nasazeními
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Změna názvů zařízení](troubleshoot-device-names-problems.md)
- [Offline instalace agenta virtuálního počítače s Windows](install-vm-agent-offline.md)
- [Restartování nebo změna velikosti virtuálního počítače](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problém s výkonem virtuálního počítače
- [Problémy s výkonem virtuálních počítačů](performance-diagnostics.md)
- Windows
    - [Použití PerfInsights](how-to-use-perfinsights.md)
    - [Rozšíření Diagnostika výkonu](performance-diagnostics-vm-extension.md)
- Linux
    - [Použití PerfInsights](how-to-use-perfinsights-linux.md)