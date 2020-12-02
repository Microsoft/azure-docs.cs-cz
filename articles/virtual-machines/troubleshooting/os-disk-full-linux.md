---
title: Problémy s plným diskem s operačním systémem na virtuálním počítači se systémem Linux
description: Řešení potíží s plným diskem s operačním systémem na virtuálním počítači se systémem Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523448"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problémy s plným diskem s operačním systémem na virtuálním počítači se systémem Linux

Když se disk s operačním systémem virtuálního počítače (VM) pro Linux zaplní, může to způsobit problémy se správnou operací virtuálního počítače.

## <a name="symptom"></a>Příznak

Při pokusu o vytvoření nového souboru se zobrazí tato zpráva:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Několik démonů pak indikuje, že během relace spuštění nelze vytvářet dočasné soubory.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Příčina

Existuje několik důvodů, proč tato chybová zpráva může nastat:

1. Disk může být plný.
1. Systém souborů možná vyčerpal z uzlů inode.
1. Datový disk může být připojen k existujícímu adresáři, což způsobuje, že soubory budou skryté.
1. Soubory, které jsou otevřeny procesem a následně odstraněny.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvořte a získejte přístup k opravnému virtuálnímu počítači.
1. Volné místo na disku.
1. Znovu sestavte virtuální počítač.

> [!NOTE]
> Při výskytu této chyby hostující operační systém není funkční. Pokud chcete tento problém vyřešit, odstraňte tento problém v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí kroků 1-3 příkazů pro [opravu virtuálního počítače](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) Připravte opravný virtuální počítač.
1. Pomocí SSH se připojte k opravnému virtuálnímu počítači.

### <a name="free-up-space-on-the-disk"></a>Uvolněte místo na disku.

Řešení problému:

- Změňte velikost disku na 1 TB, pokud již není maximální velikost 1 TB.
- Uvolněte místo na disku.

1. Ověřte, zda je disk plný. Pokud je velikost disku menší než 1 TB, rozšiřte ho pomocí rozhraní příkazového [řádku Azure](../linux/expand-disks.md)na maximálně 1 TB.
1. Pokud je disk již 1 TB, budete muset uvolnit místo na disku.
1. Po dokončení změny velikosti a vyčištění budete moct znovu sestavit virtuální počítač.

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

K opětovnému sestavení virtuálního počítače použijte [Krok 5 příkazů pro opravu virtuálního počítače](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
