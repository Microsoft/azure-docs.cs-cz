---
title: Ukončení procesu systému Windows stop stav chyby
description: Tento článek popisuje kroky pro řešení problémů, ve kterých operační systém narazí na chybu STOP 0xC000021A, což brání spuštění virtuálního počítače Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344821"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Chyba stop systému Windows – proces stavu systému 0xC000021A byl ukončen.

Tento článek popisuje kroky pro řešení problémů, při kterých operační systém (OS) zjistí chybu STOP 0xC000021A, která udržuje virtuální počítač Azure ve spuštění.

## <a name="symptom"></a>Příznak

Při použití [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku obrazovky virtuálního počítače zobrazí snímek obrazovky zprávu, že operační systém při spuštění narazil na chybu, a to s následující zprávou:

**Váš počítač narazil na problém a je potřeba ho restartovat. Jenom shromažďujeme nějaké informace o chybách a pak se můžete restartovat. (# #% dokončeno) Pokud se chcete dozvědět víc, můžete pro tuto chybu vyhledat online později: 0xC000021a**.

  ![Obrázek 1 zobrazuje kód chyby #0xC000021A se zprávou, že váš počítač byl příčinou problému a je nutné ho restartovat. Jenom shromažďujeme nějaké informace o chybách a potom můžete restartovat. ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Příčina

Chyba 0xC000021A znamená **STATUS_SYSTEM_PROCESS_TERMINATED**.

K této chybě dochází v případě, že se nepovede o nepostradatelný proces, jako je například WinLogon (winlogon.exe) nebo subsystém run-time (csrss.exe) klientského serveru. Jakmile jádro zjistí, že se některá z těchto služeb zastavila, vyvolá chybu **STOP 0xC000021A** . Tato chyba může mít několik příčin, včetně:

- Byly nainstalovány neshodné systémové soubory.
- Instalace aktualizace Service Pack nebo aktualizace KB se nezdařila.
- Zálohovací program, který se používá k obnovení pevného disku, neobnovil správně soubory, které se mohly použít.
- Byl nainstalován nekompatibilní program třetí strany.

## <a name="solution"></a>Řešení

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, bude nutné analyzovat výpis stavu systému. Shromážděte soubor výpisu paměti pro chybu a obraťte se na podporu. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1.  Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) Připravte opravný virtuální počítač.
2.  Pomocí **připojení ke vzdálené ploše**se připojte k OPRAVNému virtuálnímu počítači.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1.  V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, přejít na F:\Windows.
2.  Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.
3.  Pokud se vám nedaří najít soubor Memory. dmp, možná budete chtít místo toho použít [volání NMI (nemaskovaná přerušení) v sériové konzole](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Můžete postupovat podle tohoto průvodce [vygenerováním souboru s výpisem stavu systému pomocí volání NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Další kroky

- Další informace o řešení potíží najdete v tématu [řešení běžných chyb při spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) nebo řešení [potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Měli byste se také seznámit s postupem [použití diagnostiky spouštění k řešení potíží s virtuálním počítačem](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).
- Pokud se k VIRTUÁLNÍmu počítači nemůžete připojit, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)
