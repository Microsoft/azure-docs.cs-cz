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
ms.openlocfilehash: b13b61aff819271ed1722572f251f9a6d14b17ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976993"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Chyba stop systému Windows – proces stavu systému 0xC000021A byl ukončen.

Tento článek popisuje kroky pro řešení problémů, při kterých operační systém (OS) zjistí chybu STOP 0xC000021A, která udržuje virtuální počítač Azure ve spuštění.

## <a name="symptom"></a>Příznak

Při použití [diagnostiky spouštění](./boot-diagnostics.md) k zobrazení snímku obrazovky virtuálního počítače zobrazí snímek obrazovky zprávu, že operační systém při spuštění narazil na chybu, a to s následující zprávou:

**Váš počítač narazil na problém a je potřeba ho restartovat. Jenom shromažďujeme nějaké informace o chybách a pak se můžete restartovat. (# #% dokončeno) Pokud se chcete dozvědět víc, můžete pro tuto chybu vyhledat online později: 0xC000021a**.

  ![Obrázek 1 zobrazuje kód chyby #0xC000021A se zprávou, že váš počítač byl příčinou problému a je nutné ho restartovat. Jenom shromažďujeme nějaké informace o chybách a potom můžete restartovat. ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Příčina

Chyba 0xC000021A znamená **STATUS_SYSTEM_PROCESS_TERMINATED**.

K této chybě dochází, pokud se nepovede, když se nejedná o kritický proces, jako je například WinLogon (winlogon.exe) nebo podsystém klientského serveru Run-Time (csrss.exe). Jakmile jádro zjistí, že se některá z těchto služeb zastavila, vyvolá chybu **STOP 0xC000021A** . Tato chyba může mít několik příčin, včetně:

- Byly nainstalovány neshodné systémové soubory.
- Instalace aktualizace Service Pack nebo aktualizace KB se nezdařila.
- Zálohovací program, který se používá k obnovení pevného disku, neobnovil správně soubory, které se mohly použít.
- Byl nainstalován nekompatibilní program třetí strany.

## <a name="solution"></a>Řešení

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, bude nutné analyzovat výpis stavu systému. Shromážděte soubor výpisu paměti pro chybu a obraťte se na podporu. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1.  Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2.  Pomocí **připojení ke vzdálené ploše**se připojte k OPRAVNému virtuálnímu počítači.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1.  V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, přejít na F:\Windows.
2.  Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.
3.  Pokud se vám nedaří najít soubor Memory. dmp, možná budete chtít místo toho použít [volání NMI (nemaskovaná přerušení) v sériové konzole](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Můžete postupovat podle tohoto průvodce [vygenerováním souboru s výpisem stavu systému pomocí volání NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Další kroky

- Další informace o řešení potíží najdete v tématu [řešení běžných chyb při spouštění](./boot-error-troubleshoot.md) nebo řešení [potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-windows.md). Měli byste se také seznámit s postupem [použití diagnostiky spouštění k řešení potíží s virtuálním počítačem](./boot-diagnostics.md).
- Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md).
- Pokud se k VIRTUÁLNÍmu počítači nemůžete připojit, přečtěte si téma [řešení potíží s připojením RDP k virtuálnímu počítači Azure](./troubleshoot-rdp-connection.md)