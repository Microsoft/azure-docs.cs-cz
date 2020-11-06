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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424037"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Chyba stop systému Windows – systémová výjimka systémových vláken nebyla zpracována.

Tento článek popisuje kroky pro řešení problémů, při kterých hostující operační systém narazí na problém a chce restartovat virtuální počítač Azure. Zpráva bude uvádět, že "výjimka systémového vlákna nebyla zpracována".

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje Windows, který se musí restartovat. buď **není zpracována výjimka systémového vlákna** stop Code, nebo kód chyby **0x0000007E**.

![Snímek obrazovky ukazuje, že se během spouštění Windows zablokuje zpráva: "Váš počítač narazil na problém a musí se restartovat. Restartuje se vám. " Stop kód: "výjimka systémového vlákna není ZPRACOVÁNa"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Příčina

Tuto příčinu nelze určit, dokud nebude analyzován soubor výpisu paměti. Pokračujte ve shromažďování souboru s výpisem paměti.

## <a name="solution"></a>Řešení

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, nejprve je třeba shromáždit soubor výpisu paměti pro danou chybu a pak kontaktovat podporu pomocí souboru s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
2. Připojte se k opravnému virtuálnímu počítači pomocí Připojení ke vzdálené ploše.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem označeno jako *F* , pak musíte přejít na `F:\Windows` .
2. Vyhledejte soubor **Memory. dmp** a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.
3. Pokud se vám nedaří najít soubor **Memory. dmp** , postupujte podle pokynů průvodce a [vygenerujte soubor s výpisem stavu systému pomocí volání nemaskovatelné přerušení (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Další informace o voláních NMI naleznete v tématu [volání NMI (nemaskovaná přerušení) v uživatelské příručce k sériové konzole](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení chyb při spouštění virtuálních počítačů Azure](./boot-error-troubleshoot.md)