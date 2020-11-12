---
title: Virtuální počítač nereaguje při čekání na správce místní relace
description: Tento článek popisuje kroky pro řešení problémů, při kterých je hostovaný operační systém zablokovaný, čeká, než správce místních relací dokončí zpracování při spuštění virtuálního počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536230"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>Virtuální počítač nereaguje při čekání na správce místní relace

Tento článek popisuje kroky pro řešení problémů, při kterých je hostovaný operační systém zablokovaný, čeká, než správce místních relací dokončí zpracování při spuštění virtuálního počítače Azure (VM).

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje výzvu se zprávou: " *Počkejte prosím na správce místních relací* ".

![Snímek obrazovky s operačním systémem hosta se zablokuje ve zprávě. Počkejte prosím na správce místních relací v systému Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Příčina

Může existovat několik důvodů, proč virtuální počítač zablokuje čekání na správce místní relace. Pokud se čeká na trvalé vydání Správce místních relací, budete muset shromáždit výpis paměti pro účely analýzy.

## <a name="solution"></a>Řešení

V některých případech stačí počkat dostatečně dlouho, než se proces dokončí, aby se váš problém vyřešil. Pokud váš virtuální počítač neodpovídá a zůstane na obrazovce čekání po celou hodinu, měli byste shromáždit výpis paměti a pak kontaktovat podporu Microsoftu.

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
> [Řešení chyb při spouštění virtuálních počítačů Azure](boot-error-troubleshoot.md)