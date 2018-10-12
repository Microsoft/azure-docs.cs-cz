---
title: Připojení spravovaného datového disku k virtuálnímu počítači s Windows – Azure | Dokumentace Microsoftu
description: Jak se připojit spravovaného datového disku k virtuálnímu počítači s Windows pomocí webu Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 8d83af114ebb5e5ff78372897d3e08ed592d4012
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093897"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí webu Azure portal

V tomto článku se dozvíte, jak připojit nový spravovaného datového disku k virtuálnímu počítači (VM) Windows pomocí webu Azure portal. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).


## <a name="add-a-data-disk"></a>Přidání datového disku

1. V [webu Azure portal](https://portal.azure.com), v nabídce na levé straně vyberte **virtuálních počítačů**.
2. Vyberte virtuální počítač ze seznamu.
3. Na **virtuálního počítače** stránce **disky**.
4. Na **disky** stránce **přidat datový disk**.
5. V rozevíracím seznamu pro nový disk, vyberte **vytvořit disk**.
6. V **vytvoření spravovaného disku** stránky, zadejte název pro disk a podle potřeby upravte ostatní nastavení. Až to budete mít, vyberte **Vytvořit**.
7. V **disky** stránce **Uložit** uložte novou konfiguraci disku pro virtuální počítač.
8. Poté, co Azure disk vytvoří a připojí ho k virtuálnímu počítači, nový disk je uvedený v nastavení disku virtuálního počítače v rámci **datové disky**.


## <a name="initialize-a-new-data-disk"></a>Inicializovat nový datový disk

1. Připojte se k virtuálnímu počítači.
1. Vyberte Windows **Start** nabídku uvnitř spuštěného virtuálního počítače a zadejte **diskmgmt.msc** do vyhledávacího pole. **Správa disků** konzole otevře.
2. Správa disků rozpozná, že máte novou neinicializované disků a **inicializovat Disk** zobrazí se okno.
3. Ověřte nový disk je vybraná a pak vyberte **OK** inicializovat ji.
4. Nový disk se zobrazí jako **nepřidělené**. Klikněte pravým tlačítkem na libovolné místo na disku a vyberte **nový jednoduchý svazek**. **Průvodci vytvořením jednoduchého svazku** otevře se okno.
5. Pokračujte podle pokynů průvodce, všechny výchozí hodnoty, a po dokončení vyberte **Dokončit**.
6. Zavřít **nástroji Správa disků**.
7. Automaticky otevírané okno se zobrazí upozornění, že je nutné naformátovat nový disk, než budete moct použít. Vyberte **naformátovat disk**.
8. V **naformátovat nový disk** okna, zkontrolujte nastavení a pak vyberte **Start**.
9. Zobrazí se upozornění oznamující, že formátování disky vymaže všechna data. Vyberte **OK**.
10. Po dokončení formátování vyberte **OK**.

## <a name="use-trim-with-standard-storage"></a>Pomocí operace TRIM na úložiště úrovně standard

Pokud používáte storage úrovně standard (HDD), měli byste povolit **TRIM** příkazu. **TRIM** příkaz zahodí nepoužívané bloky na disku tak, že bude se vám účtovat jenom za úložiště, které skutečně používáte. S použitím **TRIM**, ušetříte náklady na Pokud vytvoříte velkých souborů a pak je později odstranit. 

Chcete-li zkontrolovat **TRIM** nastavení, otevřete příkazový řádek na virtuálním počítači Windows a zadejte následující příkaz:

```
fsutil behavior query DisableDeleteNotify
```

Pokud příkaz vrátí hodnotu 0, **TRIM** je povolená správně. V opačném případě vrátí hodnotu 1, spusťte následující příkaz umožňující **TRIM**:

```
fsutil behavior set DisableDeleteNotify 0
```

Po odstranění dat z disku, můžete zajistit **TRIM** operace flush správně spuštěním defragmentační s **TRIM**:

```
defrag.exe <volume:> -l
```

Můžete také formátovat svazku a ujistěte se, že celý svazek je oříznuta.

## <a name="next-steps"></a>Další postup

- Můžete také [připojení datového disku pomocí prostředí PowerShell](attach-disk-ps.md).
- Pokud vaše aplikace potřebuje používat *D:* jednotka pro ukládání dat, můžete [změnit písmeno jednotky dočasného disku Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
