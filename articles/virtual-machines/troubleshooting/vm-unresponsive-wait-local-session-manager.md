---
title: Virtuální počítač nereaguje při čekání na službu Správce místních relací.
description: Tento článek popisuje kroky pro řešení problémů, při kterých je hostovaný operační systém zablokovaný, čeká, než správce místních relací dokončí zpracování během spouštění virtuálního počítače Azure.
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
ms.openlocfilehash: 8af8d7695c48c6ac682109bb38935e98921fa9e4
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681903"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>Virtuální počítač nereaguje při čekání na službu Správce místních relací.

Tento článek popisuje kroky pro řešení problémů, při kterých hostovaný operační systém (hostovaný operační systém) zablokuje čekání na dokončení zpracování během spouštění virtuálního počítače Azure v rámci Správce místních relací.

## <a name="symptoms"></a>Příznaky

Když k zobrazení snímku výstupu virtuálního počítače použijete [diagnostiku spouštění](./boot-diagnostics.md) , uvidíte, že se na snímku obrazovky zobrazí výzva s výzvou, aby se zobrazila zpráva s upozorněním, že se správce místních relací zobrazuje.

![Snímek obrazovky se zablokovaným hostovaným operačním systémem ve Windows Serveru 2012 R2 s "Počkejte prosím na správce místních relací".](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Příčina

Je možné, že virtuální počítač bude zablokovat čekání na správce místní relace, může být několik důvodů. Pokud s tím budou dál problémy, budete muset shromáždit výpis paměti pro účely analýzy.

## <a name="solution"></a>Řešení

V některých případech se tento problém vyřeší pouhým čekáním na dokončení procesu. Pokud váš virtuální počítač neodpovídá a zůstane na obrazovce čekání po dobu více než hodinu, měli byste shromáždit výpis paměti a pak kontaktovat podporu Microsoftu.

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
> [Řešení chyb při spouštění virtuálních počítačů Azure](boot-error-troubleshoot.md)