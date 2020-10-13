---
title: 'Vytvoření jednotky D: pro virtuální počítač datový disk '
description: 'Popisuje, jak změnit písmena jednotek pro virtuální počítač s Windows, abyste mohli použít jednotku D: jako datovou jednotku.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cae29954211e62601debb35d76f938fb6a92779e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284604"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Použití jednotky D: jako datové jednotky na virtuálním počítači s Windows
Pokud vaše aplikace potřebuje k ukládání dat použít jednotku D, postupujte podle těchto pokynů, abyste pro dočasný disk použili jiné písmeno jednotky. Nikdy nepoužívejte dočasný disk k ukládání dat, která je třeba zachovat.

Při změně velikosti nebo **zastavení (zrušení přidělení)** virtuálního počítače může tato operace aktivovat umístění virtuálního počítače do nového hypervisoru. Toto umístění může také aktivovat plánovaná nebo neplánovaná událost údržby. V tomto scénáři se dočasný disk znovu přiřadí k prvnímu dostupnému písmenu jednotky. Pokud máte aplikaci, která konkrétně vyžaduje jednotku D:, je třeba pomocí následujících kroků dočasně přesunout pagefile.sys, připojit nový datový disk a přiřadit mu písmeno D a pak pagefile.sys přesunout zpátky na dočasnou jednotku. Až to bude hotové, Azure se znovu nevrátí, pokud se virtuální počítač přesune do jiného hypervisoru.

Další informace o tom, jak Azure používá dočasný disk, najdete v článku o [objasnění dočasné jednotky na Microsoft Azure Virtual Machines](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>Připojit datový disk
Nejdřív budete potřebovat připojit datový disk k virtuálnímu počítači. Pokud to chcete provést pomocí portálu, přečtěte si téma [Postup připojení spravovaného datového disku v Azure Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Dočasné přesunutí pagefile.sys do jednotky C
1. Připojte se k virtuálnímu počítači. 
2. Klikněte pravým tlačítkem myši na nabídku **Start** a vyberte možnost **systém**.
3. V nabídce na levé straně vyberte **Upřesnit nastavení systému**.
4. V části **výkon** vyberte **Nastavení**.
5. Vyberte kartu **Upřesnit**.
6. V části **virtuální paměť** vyberte **změnit**.
7. Vyberte jednotku **C** a pak klikněte na **Velikost spravovaná systémem** a pak klikněte na **nastavit**.
8. Vyberte jednotku **D** a pak klikněte na **žádný stránkovací soubor** a pak klikněte na **nastavit**.
9. Klikněte na Použít. Zobrazí se upozornění, že je potřeba restartovat počítač, aby se změny projevily.
10. Restartujte virtuální počítač.

## <a name="change-the-drive-letters"></a>Změna písmen jednotek
1. Po restartování virtuálního počítače se znovu přihlaste k virtuálnímu počítači.
2. Klikněte na nabídku **Start** , zadejte **diskmgmt. msc** a stiskněte klávesu ENTER. Spustí se Správa disků.
3. Klikněte pravým tlačítkem myši na **D**, na dočasné jednotce úložiště a vyberte **změnit písmeno jednotky a cestu**.
4. V části písmeno jednotky vyberte novou jednotku, například **T** , a pak klikněte na **OK**. 
5. Pravým tlačítkem myši klikněte na datový disk a vyberte možnost **změnit písmeno jednotky a cestu**.
6. V části písmeno jednotky vyberte jednotka **D** a pak klikněte na **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Přesunout pagefile.sys zpátky na dočasnou jednotku úložiště
1. Klikněte pravým tlačítkem myši na nabídku **Start** a vyberte možnost **systém** .
2. V nabídce na levé straně vyberte **Upřesnit nastavení systému**.
3. V části **výkon** vyberte **Nastavení**.
4. Vyberte kartu **Upřesnit**.
5. V části **virtuální paměť** vyberte **změnit**.
6. Vyberte jednotku operačního systému **C** a klikněte na možnost **bez stránkovacího souboru** a potom klikněte na možnost **nastavit**.
7. Vyberte jednotku dočasného úložiště **T** a pak klikněte na **Velikost spravovaná systémem** a pak klikněte na **nastavit**.
8. Klikněte na **Použít**. Zobrazí se upozornění, že je potřeba restartovat počítač, aby se změny projevily.
9. Restartujte virtuální počítač.

## <a name="next-steps"></a>Další kroky
* Úložiště, které je k dispozici pro virtuální počítač, můžete zvýšit [připojením dalšího datového disku](attach-managed-disk-portal.md).
