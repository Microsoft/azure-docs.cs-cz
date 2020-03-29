---
title: Připojení spravovaného datového disku k virtuálnímu počítači s Windows – Azure
description: Jak připojit spravovaný datový disk k virtuálnímu počítači s Windows pomocí portálu Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919375"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí portálu Azure

Tento článek ukazuje, jak připojit nový spravovaný datový disk k virtuálnímu počítači (VM) Windows pomocí portálu Azure. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Další informace najdete [v tématu Velikosti pro virtuální počítače](sizes.md).


## <a name="add-a-data-disk"></a>Přidání datového disku

1. Přejděte na [portál Azure](https://portal.azure.com) a přidejte datový disk. Vyhledejte a vyberte **virtuální počítače**.
2. Vyberte virtuální počítač ze seznamu.
3. Na stránce **Virtuální počítač** vyberte **Disky**.
4. Na stránce **Disky** vyberte **Přidat datový disk**.
5. V rozevíracím programu pro nový disk vyberte **Vytvořit disk**.
6. Na stránce **Vytvořit spravovaný disk** zadejte název disku a podle potřeby upravte další nastavení. Až to budete mít, vyberte **Vytvořit**.
7. Na stránce **Disky** vyberte **Uložit,** chcete-li uložit novou konfiguraci disku pro virtuální počítače.
8. Po Azure vytvoří disk a připojí jej k virtuálnímu počítači, nový disk je uveden v nastavení disku virtuálního počítače v části **Datové disky**.


## <a name="initialize-a-new-data-disk"></a>Inicializovat nový datový disk

1. Připojte se k virtuálnímu počítači.
1. Vyberte nabídku **Start** systému Windows uvnitř spuštěného virtuálního počítače a do vyhledávacího pole zadejte **diskmgmt.msc.** Otevře se konzola **Správa disků.**
2. Správa disků rozpozná, že máte nový, neinicializovaný disk a zobrazí se okno **Inicializovat disk.**
3. Ověřte, zda je vybrán nový disk, a pak vyberte **OK,** chcete-li jej inicializovat.
4. Nový disk se zobrazí jako **nepřidělený**. Klepněte pravým tlačítkem myši na libovolné místo na disku a vyberte **možnost Nový jednoduchý svazek**. Otevře se okno **Průvodce novým jednoduchým svazkem.**
5. Pokračujte průvodcem, zachovte všechny výchozí hodnoty a až budete hotovi, vyberte **dokončit**.
6. Zavřít **správu disků**.
7. Zobrazí se vyskakovací okno s upozorněním, že před použitím je třeba nový disk naformátovat. Vyberte **formát disku**.
8. V okně **Formát ovat nový disk** zaškrtněte nastavení a pak vyberte **Spustit**.
9. Zobrazí se upozornění, že formátování disků vymaže všechna data. Vyberte **OK**.
10. Po dokončení formátování vyberte **ok**.

## <a name="next-steps"></a>Další kroky

- Datový disk můžete připojit také [pomocí prostředí PowerShell](attach-disk-ps.md).
- Pokud vaše aplikace potřebuje k ukládání dat jednotku *D:* použít, můžete [změnit písmeno jednotky dočasného disku systému Windows](change-drive-letter.md).
