---
title: 'Vytvořit jednotku D: datový disk virtuálního počítače | Microsoft Docs'
description: 'Popisuje, jak změnit písmena jednotek pro virtuální počítač s Windows, tak, aby D: jednotku můžete používat jako datový disk.'
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
ms.openlocfilehash: a4e29ce7ebb20753c3313e1148e1c91e853f49f5
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27577441"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Jako datový disk pro virtuální počítač Windows jednotku D:
Pokud aplikace potřebuje využívat diskovou jednotku d k ukládání dat, postupujte podle těchto pokynů můžete použít jiné písmeno jednotky pro dočasné disku. Nikdy nepoužívejte dočasným diskovým k ukládání dat, který je třeba zachovat.

Při změně velikosti nebo **zastavení (Deallocate)** virtuální počítač, to může aktivovat umístění virtuálního počítače do nového hypervisoru. Události plánované i neplánované údržby, může se spustit toto umístění. V tomto scénáři bude přiřazen dočasným diskovým první dostupné písmeno jednotky. Pokud máte aplikaci, která vyžaduje konkrétně jednotky D:, budete muset následujícím postupem dočasně přesunout pagefile.sys, připojit nový datový disk a přiřaďte ho písmeno D a poté přesuňte pagefile.sys zpět na dočasné jednotce. Po dokončení Azure nebude zpět D: Pokud virtuální počítač přesune do různých hypervisoru.

Další informace o tom, jak Azure používá dočasným diskovým najdete v tématu [pochopení dočasné jednotce ve virtuálních počítačích Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Připojit datový disk
Nejprve budete muset připojit datový disk k virtuálnímu počítači. To provedete pomocí portálu najdete v části [jak připojit spravované datový disk na portálu Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Dočasně přesunout pagefile.sys na jednotce C
1. Připojte k virtuálnímu počítači. 
2. Klikněte pravým tlačítkem myši **spustit** nabídku a vyberte **systému**.
3. V levé nabídce vyberte **Upřesnit nastavení systému**.
4. V **výkonu** vyberte **nastavení**.
5. Vyberte **Upřesnit** kartě.
6. V **virtuální paměti** vyberte **změnu**.
7. Vyberte **C** jednotky a pak klikněte na **velikost určí systém** a pak klikněte na **nastavit**.
8. Vyberte **D** jednotky a pak klikněte na **stránkovací soubor** a pak klikněte na **nastavit**.
9. Kliknutím na tlačítko použít. Zobrazí se upozornění, že počítač je třeba restartovat změny se projeví.
10. Restartujte virtuální počítač.

## <a name="change-the-drive-letters"></a>Změna písmena jednotek
1. Po restartování virtuálního počítače se přihlásíte do virtuálního počítače.
2. Klikněte **spustit** nabídce a typ **diskmgmt.msc** a stiskněte Enter. Správa disků se spustí.
3. Klikněte pravým tlačítkem na **D**, dočasné úložiště jednotky a vyberte **změnit písmeno jednotky a cesty**.
4. V části písmeno jednotky, vyberte na nový disk **T** a pak klikněte na **OK**. 
5. Klikněte pravým tlačítkem na datový disk a vyberte **změnit písmeno jednotky a cesty**.
6. V části písmeno jednotky, vyberte jednotky **D** a pak klikněte na **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Přesunout pagefile.sys zpět na jednotku dočasné úložiště
1. Klikněte pravým tlačítkem myši **spustit** nabídku a vyberte **systému**
2. V levé nabídce vyberte **Upřesnit nastavení systému**.
3. V **výkonu** vyberte **nastavení**.
4. Vyberte **Upřesnit** kartě.
5. V **virtuální paměti** vyberte **změnu**.
6. Vyberte jednotku operačního systému **C** a klikněte na tlačítko **stránkovací soubor** a pak klikněte na **nastavit**.
7. Vyberte jednotku dočasné úložiště **T** a pak klikněte na **velikost určí systém** a pak klikněte na **nastavit**.
8. Klikněte na tlačítko **Použít**. Zobrazí se upozornění, že počítač je třeba restartovat změny se projeví.
9. Restartujte virtuální počítač.

## <a name="next-steps"></a>Další postup
* Můžete zvýšit úložiště k dispozici k virtuálnímu počítači pomocí [další datový disk se připojuje](attach-managed-disk-portal.md).

