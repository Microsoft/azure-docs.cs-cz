---
title: Možnosti napájení v konzole sériového režimu Azure | Microsoft Docs
description: Možnosti napájení virtuálního počítače dostupné v rámci konzole Azure Serial Console
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451200"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Možnosti napájení dostupné z konzole Azure Serial

Konzola sériového prostředí Azure poskytuje několik výkonných nástrojů pro řízení spotřeby na vašem VIRTUÁLNÍm počítači nebo v sadě škálování virtuálních počítačů. Tyto možnosti řízení spotřeby můžou být matoucí, takže se jedná o přehled jednotlivých nástrojů a zamýšleného případu použití.

Funkce sériové konzoly | Popis | Případ použití
:----------------------|:------------|:---------
Restartovat virtuální počítač | Bezproblémové restartování instance virtuálního počítače nebo sady škálování virtuálních počítačů. Tato operace je stejná jako volání funkce restartu, která je k dispozici na stránce Přehled. | Ve většině případů by tato možnost měla být prvním nástrojem při pokusu o restartování virtuálního počítače. Připojení k sériové konzole bude mít krátké přerušení a automaticky se obnoví hned po restartování virtuálního počítače.
Resetování virtuálního počítače | Vynutí se cyklus napájení virtuálního počítače nebo škálování virtuálního počítače na platformě Azure. | Tato možnost slouží k okamžitému restartování operačního systému bez ohledu na jeho aktuální stav. Vzhledem k tomu, že tato operace není plynulá, existuje riziko ztráty nebo poškození dat. Připojení k sériové konzole není nijak přerušeno. to může být užitečné při posílání příkazů v čase spuštění (například při získávání GRUB na virtuálním počítači Linux nebo v nouzovém režimu na virtuálním počítači s Windows).
SysRq-restart (b) | Systémová žádost o vynucení restartování hostů. | Tato funkce se vztahuje pouze na operační systémy Linux a vyžaduje, aby v operačním systému [byla povolená SysRq](./serial-console-nmi-sysrq.md#system-request-sysrq) . Pokud je operační systém správně nakonfigurovaný pro SysRq, tento příkaz způsobí restartování operačního systému.
NMI (nelze maskovat přerušení) | Příkaz přerušení, který se doručí operačnímu systému | Tato operace je k dispozici pro virtuální počítače se [systémem Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) i [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) a vyžaduje, aby byl NMI povolen. Odeslání NMI obvykle způsobí, že dojde k chybě operačního systému. Můžete nakonfigurovat operační systém, aby vytvořil soubor s výpisem paměti a pak se restartoval po přijetí NMI, což může být užitečné při ladění nízké úrovně.

## <a name="next-steps"></a>Další kroky
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače se systémem Linux](./serial-console-linux.md)
* Další informace o [konzole sériového rozhraní Azure pro virtuální počítače s Windows](./serial-console-windows.md)