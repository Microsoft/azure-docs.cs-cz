---
title: 'Vytvoření jednotky D: jednotky virtuálního počítače jako datového disku '
description: Popisuje, jak změnit písmena jednotek pro virtuální hod windows, takže můžete použít jednotku D:jako datovou jednotku.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 15df3178f2860fa066a82cb1429e0c1a6e5c2b08
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083418"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Použití jednotky D: jako datové jednotky na virtuálním počítači s Windows
Pokud vaše aplikace potřebuje k ukládání dat použít jednotku D, použijte pro dočasný disk jiné písmeno jednotky. Nikdy nepoužívejte dočasný disk k ukládání dat, která je třeba zachovat.

Pokud změníte velikost nebo **zastavit (Navrátit)** virtuální počítač, to může vyvolat umístění virtuálního počítače do nového hypervisoru. Plánované nebo neplánované události údržby může také vyvolat toto umístění. V tomto scénáři bude dočasný disk znovu přiřazen k prvnímu dostupnému písmenu jednotky. Pokud máte aplikaci, která konkrétně vyžaduje jednotku D:, je třeba dočasně přesunout stránkovací soubor pagefile.sys, připojit nový datový disk a přiřadit mu písmeno D a potom přesunout stránkovací soubor pagefile.sys zpět na dočasnou jednotku. Po dokončení Azure nebude mít zpět D: Pokud virtuální počítač přesune do jiného hypervisoru.

Další informace o tom, jak Azure používá dočasný disk, najdete [v tématu Principy dočasné jednotky na virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Připojení datového disku
Nejprve budete muset připojit datový disk k virtuálnímu počítači. Postup pomocí portálu najdete v [tématu Jak připojit spravovaný datový disk na webu Azure Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Dočasné přesunutí souboru pagefile.sys na jednotku C
1. Připojte se k virtuálnímu počítači. 
2. Klepněte pravým tlačítkem myši na nabídku **Start** a vyberte **položku Systém**.
3. V levé nabídce vyberte **Upřesnit nastavení systému**.
4. V části **Výkon** vyberte **Nastavení**.
5. Vyberte kartu **Upřesnit**.
6. V části **Virtuální paměť** vyberte **Změnit**.
7. Vyberte jednotku **C,** klepněte na **položku Systémová spravovaná velikost** a klepněte na tlačítko **Nastavit**.
8. Vyberte jednotku **D** a klepněte na položku **Žádný stránkovací soubor** a klepněte na tlačítko **Nastavit**.
9. Klikněte na Použít. Zobrazí se upozornění, že počítač je třeba restartovat, aby změny mohly mít vliv.
10. Restartujte virtuální počítač.

## <a name="change-the-drive-letters"></a>Změna písmen jednotek
1. Po restartování virtuálního počítače se znovu přihlaste k virtuálnímu počítači.
2. Klepněte na nabídku **Start** a zadejte **diskmgmt.msc** a stiskněte Enter. Spustí se správa disků.
3. Klepněte pravým tlačítkem myši na **položku D**, jednotku dočasného úložiště a vyberte **možnost Změnit písmeno jednotky a cesty**.
4. V části Písmeno jednotky vyberte novou jednotku, například **T,** a klepněte na tlačítko **OK**. 
5. Klepněte pravým tlačítkem myši na datový disk a vyberte **možnost Změnit písmeno jednotky a cesty**.
6. V části Písmeno jednotky vyberte jednotku **D** a klepněte na tlačítko **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Přesunutí souboru pagefile.sys zpět na jednotku dočasného úložiště
1. Klikněte pravým tlačítkem myši na nabídku **Start** a vyberte **systém**
2. V levé nabídce vyberte **Upřesnit nastavení systému**.
3. V části **Výkon** vyberte **Nastavení**.
4. Vyberte kartu **Upřesnit**.
5. V části **Virtuální paměť** vyberte **Změnit**.
6. Vyberte jednotku Operačního systému **C** a klepněte na **položku Žádný stránkovací soubor** a klepněte na tlačítko **Nastavit**.
7. Vyberte jednotku dočasného úložiště **T** a klepněte na **položku Systémová spravovaná velikost** a klepněte na tlačítko **Nastavit**.
8. Klikněte na **Použít**. Zobrazí se upozornění, že počítač je třeba restartovat, aby změny mohly mít vliv.
9. Restartujte virtuální počítač.

## <a name="next-steps"></a>Další kroky
* Úložiště, které je k dispozici pro váš virtuální počítač, můžete zvětšit [připojením dalšího datového disku](attach-managed-disk-portal.md).

