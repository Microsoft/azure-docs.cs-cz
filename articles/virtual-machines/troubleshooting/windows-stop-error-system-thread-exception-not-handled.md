---
title: Chyba stop systému Windows – systémová výjimka systémových vláken nebyla zpracována.
description: Tento článek popisuje kroky pro řešení problémů, při kterých hostující operační systém narazí na problém a chce restartovat virtuální počítač Azure. Zpráva bude uvádět, že "výjimka systémového vlákna nebyla zpracována".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: beb658474e49f9b47900d8481dab0424dae8c5be
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661387"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Chyba stop systému Windows – systémová výjimka systémových vláken nebyla zpracována.

Tento článek popisuje kroky pro řešení problémů, při kterých hostovaný operační systém (hostovaný operační systém) narazí na problém a pokusí se restartovat virtuální počítač Azure (VM). Chybová zpráva, která se zobrazí, říká "výjimka systémového vlákna nebyla zpracována."

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky výstupu virtuálního počítače, zjistíte, že je nutné restartovat systém Windows s "výjimkou" systémového vlákna není zpracován kód chyby nebo "0x0000007E".

![Snímek obrazovky, při kterém se Windows zablokuje při spuštění s počítačem, který byl příčinou problému a je potřeba ho restartovat Restartuje se vám. " chybová zpráva a "výjimka systémového vlákna nebyla ZPRACOVÁNa" kód zastavení.](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Příčina

Tuto příčinu nelze určit, dokud nebude analyzován soubor výpisu paměti. Pokračujte ve shromažďování souboru s výpisem paměti.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.


Chcete-li tento problém vyřešit, musíte nejprve shromáždit soubor výpisu paměti pro danou chybu a poté odeslat soubor podpoře společnosti Microsoft. Pokud chcete shromáždit soubor s výpisem paměti, postupujte podle pokynů v následujících dvou částech.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. Pokud chcete připravit opravný virtuální počítač, postupujte podle kroků 1-3 v [příkazech pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Připojte se k opravnému virtuálnímu počítači pomocí Připojení ke vzdálené ploše.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud má například písmeno jednotky přiřazené k připojenému disku s operačním systémem označení *F*, přejít na `F:\Windows` .
1. Vyhledejte soubor *Memory. dmp* a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) s připojeným souborem s výpisem paměti.
1. Pokud máte potíže s umístěním souboru *Memory. dmp* , [vygenerujte soubor s výpisem stavu systému pomocí volání nemaskovatelné přerušení (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Další informace o voláních NMI naleznete v tématu [volání NMI v uživatelské příručce ke službě Azure Serial Console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení chyb při spouštění virtuálních počítačů Azure](./boot-error-troubleshoot.md)
