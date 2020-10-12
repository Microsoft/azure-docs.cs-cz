---
title: Virtuální počítač Azure při použití zásad zabezpečení pro systém nereaguje.
description: Tento článek popisuje kroky pro řešení problémů, při kterých se při použití zásad zabezpečení pro systém na VIRTUÁLNÍm počítači Azure zablokuje načítání obrazovky, když virtuální počítač nereaguje.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908101"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Virtuální počítač Azure při použití zásad zabezpečení pro systém nereaguje.

Tento článek popisuje kroky pro řešení problémů, při kterých operační systém přestane reagovat a přestane reagovat, když používá zásady zabezpečení na virtuálním počítači Azure.

## <a name="symptoms"></a>Příznaky

Když k zobrazení obrazovky virtuálního počítače použijete [diagnostiku spouštění](boot-diagnostics.md) , uvidíte, že se na snímku obrazovky zobrazuje zablokované operační systém při spuštění se zprávou:

> ' Použití zásad zabezpečení pro systém '.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Obrazovka úvodní obrazovky Windows Serveru 2012 R2 je zablokovaná.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Obrazovka úvodní obrazovky Windows Serveru 2012 R2 je zablokovaná.":::

## <a name="cause"></a>Příčina

Existuje spoustu možných příčin tohoto problému. Zdroj nebudete moci znát, dokud nebude provedena analýza výpisu paměti.

## <a name="resolution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. [Vytvoření a přístup k opravnému virtuálnímu počítači](#create-and-access-a-repair-vm)
2. [Povolit kolekci sériového stavu a výpisu paměti](#enable-serial-console-and-memory-dump-collection)
3. [Opětovné sestavení virtuálního počítače](#rebuild-the-vm)
4. [Shromáždit soubor výpisu paměti](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) Připravte opravný virtuální počítač.
2. Použijte Připojení ke vzdálené ploše Připojte se k opravnému virtuálnímu počítači.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Povolit kolekci sériového stavu a výpisu paměti

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte tento skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Seznamte se s daty úložiště BCD a určete identifikátor zaváděcího programu, který použijete v dalším kroku.

     1. Pro virtuální počítač 1. generace zadejte následující příkaz a Všimněte si uvedeného identifikátoru:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        V příkazu nahraďte \<BOOT PARTITON> písmenem oddílu připojeným diskem, který obsahuje spouštěcí složku.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Obrazovka úvodní obrazovky Windows Serveru 2012 R2 je zablokovaná." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Uvolnit poškozený disk s operačním systémem:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) znovu sestavte virtuální počítač.

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, je třeba nejprve shromáždit soubor výpisu paměti pro chybu a kontaktovat podporu pomocí souboru s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

1. Připojte disk s operačním systémem k novému virtuálnímu počítači pro opravu:

    - K přípravě nového opravného virtuálního počítače použijte [kroky 1-3 příkazů pro opravu virtuálního počítače](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
    - Použijte Připojení ke vzdálené ploše Připojte se k opravnému virtuálnímu počítači.

2. Vyhledejte soubor s výpisem stavu systému a odešlete lístek podpory:

    - V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je k připojenému disku s operačním systémem přiřazené písmeno jednotky `F`, musíte přejít do složky `F:\Windows`.
    - Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.
    - Pokud se vám nedaří najít soubor Memory. dmp, možná budete chtít místo toho použít [volání NMI (nemaskovaná přerušení) v sériové konzole](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Podle pokynů průvodce můžete [vygenerovat soubor s výpisem stavu systému pomocí volání NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Další kroky

Pokud máte problémy při použití zásad místních uživatelů a skupin, přečtěte si, že [virtuální počítač nereaguje při použití zásady skupiny zásad místních uživatelů a skupin](unresponsive-vm-apply-group-policy.md) .