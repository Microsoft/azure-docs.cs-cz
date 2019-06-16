---
title: 'Vytvořit jednotku D: datového disku virtuálního počítače | Dokumentace Microsoftu'
description: 'Popisuje, jak změnit písmena jednotek pro virtuální počítač s Windows tak, aby jednotce D: lze používat jako datový disk.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cfd46d5e9750a81d89ed6d3a79bcc9bffdc3d0dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844195"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Použití disku D: jako datový disk na virtuálním počítači s Windows
Pokud vaše aplikace potřebuje k použití jednotky D k ukládání dat, postupujte podle těchto pokynů můžete použít jiné písmeno jednotky pro dočasný disk. Nikdy použít dočasný disk k uložení dat, která je potřeba nechat.

Při změně velikosti nebo **Stop (přidělení)** virtuální počítač, může to vyvolávat umístění virtuálního počítače do nového hypervisoru. Události plánované i neplánované údržby můžou také aktivovat toto umístění. V tomto scénáři se znovu přiřadí dočasný disk první dostupné písmeno jednotky. Pokud máte aplikaci, která vyžaduje konkrétně jednotce D:, budete muset postupovat podle tohoto postupu dočasně přesunout soubor pagefile.sys, připojte nový datový disk a přiřazení písmena D a pak přejděte zpět do dočasné jednotky pagefile.sys. Jakmile budete hotovi, Azure nepořizuje zpět D: Pokud virtuální počítač přesune do jiné hypervisoru.

Další informace o tom, jak Azure používá dočasný disk najdete v tématu [pochopení dočasné jednotky na virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Připojení datového disku
Nejprve budete muset připojit datový disk k virtuálnímu počítači. Provedete to tak pomocí portálu, najdete v článku [postup připojení spravovaného datového disku na webu Azure Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Dočasně přesunout pagefile.sys na jednotce C
1. Připojte se k virtuálnímu počítači. 
2. Klikněte pravým tlačítkem myši **Start** nabídky a vybereme **systému**.
3. V nabídce vlevo vyberte **Upřesnit nastavení systému**.
4. V **výkonu** vyberte **nastavení**.
5. Vyberte **Upřesnit** kartu.
6. V **virtuální paměti** vyberte **změnu**.
7. Vyberte **C** jednotky a pak klikněte na tlačítko **systému spravovaná velikost** a potom klikněte na tlačítko **nastavit**.
8. Vyberte **D** jednotky a pak klikněte na tlačítko **stránkovací soubor** a potom klikněte na tlačítko **nastavit**.
9. Kliknutím na tlačítko použít. Zobrazí se upozornění, že počítač potřebuje k provedení restartu, aby se změny projeví.
10. Restartujte virtuální počítač.

## <a name="change-the-drive-letters"></a>Změna písmena jednotek
1. Po restartování virtuálního počítače, přihlaste se zpět k virtuálnímu počítači.
2. Klikněte na tlačítko **Start** nabídce a zadejte **diskmgmt.msc** a stiskněte Enter. Správa disků se spustí.
3. Klikněte pravým tlačítkem na **D**, disk dočasného úložiště a vyberte **změnit písmeno jednotky a cesty**.
4. V části písmeno jednotky, vyberte novou jednotku **T** a potom klikněte na tlačítko **OK**. 
5. Klikněte pravým tlačítkem na datový disk a vyberte **změnit písmeno jednotky a cesty**.
6. V části písmeno jednotky, vyberte jednotky **D** a potom klikněte na tlačítko **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile.sys přejděte zpět na disk dočasného úložiště
1. Klikněte pravým tlačítkem myši **Start** nabídky a vybereme **systému**
2. V nabídce vlevo vyberte **Upřesnit nastavení systému**.
3. V **výkonu** vyberte **nastavení**.
4. Vyberte **Upřesnit** kartu.
5. V **virtuální paměti** vyberte **změnu**.
6. Vyberte jednotku operačního systému **C** a klikněte na tlačítko **stránkovací soubor** a potom klikněte na tlačítko **nastavit**.
7. Vyberte disk dočasného úložiště **T** a potom klikněte na tlačítko **systému spravovaná velikost** a potom klikněte na tlačítko **nastavit**.
8. Klikněte na tlačítko **Použít**. Zobrazí se upozornění, že počítač potřebuje k provedení restartu, aby se změny projeví.
9. Restartujte virtuální počítač.

## <a name="next-steps"></a>Další postup
* Můžete zvětšit úložiště k dispozici k virtuálnímu počítači podle [připojení další datový disk](attach-managed-disk-portal.md).

