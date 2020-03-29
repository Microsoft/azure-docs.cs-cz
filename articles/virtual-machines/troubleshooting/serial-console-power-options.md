---
title: Možnosti napájení konzoly Azure Serial Console | Dokumenty společnosti Microsoft
description: Možnosti napájení virtuálních počítačí dostupné v rámci konzoly Azure Serial Console
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451200"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Možnosti napájení dostupné v konzole Azure Serial Console

Konzola Azure Serial Console poskytuje několik výkonných nástrojů pro řízení spotřeby na škálovací sadě virtuálních počítačů nebo virtuálních strojů. Tyto možnosti řízení spotřeby může být matoucí pro některé, takže je přehled každého nástroje a jeho zamýšleného případu použití.

Funkce konzoly sériového portu | Popis | Použít případ
:----------------------|:------------|:---------
Restartování virtuálního počítače | Řádné restartování instance škálovací sady virtuálního počítače nebo virtuálního počítače. Tato operace je stejná jako volání funkce restartování, která je k dispozici na stránce Přehled. | Ve většině případů by tato možnost měla být prvním nástrojem při pokusu o restartování virtuálního počítače. Připojení sériové konzole dojde ke krátkému přerušení a bude automaticky pokračovat, jakmile se virtuální počítač restartuje.
Resetování virtuálního počítače | Silný cyklus napájení vašeho virtuálního počítače nebo škálování virtuálního počítače nastavené platformou Azure. | Tato možnost se používá k okamžitému restartování operačního systému bez ohledu na jeho aktuální stav. Vzhledem k tomu, že tato operace není elegantní, existuje riziko ztráty nebo poškození dat. Neexistuje žádné přerušení připojení sériové konzole, což může být užitečné pro odesílání příkazů v rané fázi spuštění (například získání GRUB na virtuálním počítači s Linuxem nebo nouzovém režimu v virtuálním počítači se systémem Windows).
SysRq - Restart (b) | Systémový požadavek na vynucení restartování hosta. | Tato funkce je použitelná pouze pro operační systémy Linux a vyžaduje, [aby byl sysRq povolen](./serial-console-nmi-sysrq.md#system-request-sysrq) v operačním systému. Pokud je operační systém správně nakonfigurován pro SysRq, tento příkaz způsobí restartování operačního systému.
NMI (nemaskovatelné přerušení) | Příkaz přerušení, který bude doručen do operačního systému. | Tato operace je k dispozici pro virtuální počítače [se systémem Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) i [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) a vyžaduje povolení NMI. Odeslání nmi obvykle způsobí selhání operačního systému. Operační systém můžete nakonfigurovat tak, aby vytvořil soubor s výpisem stavu paměti a po přijetí rozhraní NMI restartoval počítač, což může být užitečné při ladění nižší úrovně.

## <a name="next-steps"></a>Další kroky
* Další informace o virtuálních [počítačích Azure Serial Console pro Linux](./serial-console-linux.md)
* Další informace o [službě Azure Serial Console pro virtuální počítače s Windows](./serial-console-windows.md)