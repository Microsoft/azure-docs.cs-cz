---
title: Virtuální počítač nereaguje při použití výchozích zásad řadičů domén
titlesuffix: Azure Virtual Machines
description: Tento článek popisuje kroky pro řešení problémů, při kterých výchozí zásada řadičů domény brání spuštění virtuálního počítače Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632270"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>Virtuální počítač nereaguje při použití výchozích zásad řadičů domén

Tento článek popisuje kroky pro řešení problémů, při kterých výchozí zásada řadičů domény brání spuštění virtuálního počítače Azure (VM).

## <a name="symptom"></a>Příznak

Když použijete [diagnostiku spouštění](./boot-diagnostics.md) k zobrazení snímku obrazovky virtuálního počítače, uvidíte, že snímek obrazovky při spuštění s **výchozí zásadou řadičů domény** zobrazuje operační systém přestane reagovat.

  ![Obrázek 1 zobrazuje zablokování operačního systému pomocí výchozí zásady řadičů domény.](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Příčina

K tomuto problému může dojít z důvodu nedávných změn v zásadách výchozích řadičů domény. V opačném případě bude nutné provést analýzu souboru výpisu paměti, aby bylo možné zjistit původní příčinu.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud jste nedávno provedli změny ve výchozích zásadách řadičů domény, možná budete chtít tyto změny zrušit a problém vyřešit. Pokud si nejste jisti, co je příčinou problému, shromážděte výpis paměti a pak odešlete lístek podpory.

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, je třeba nejprve shromáždit soubor výpisu paměti pro danou chybu a poté kontaktovat podporu pomocí souboru s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.

1. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je k připojenému disku s operačním systémem přiřazené písmeno jednotky `F`, musíte přejít do složky `F:\Windows`.

1. Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.

1. Pokud se vám nedaří najít soubor Memory. dmp, možná budete chtít místo toho použít [volání NMI (nemaskovaná přerušení) v sériové konzole](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Postupujte podle pokynů průvodce a [vygenerujte soubor se stavem systému pomocí volání NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).