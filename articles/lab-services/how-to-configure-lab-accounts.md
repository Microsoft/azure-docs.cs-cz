---
title: Konfigurace automatického vypnutí virtuálních počítačů v Azure Lab Services
description: Tento článek popisuje, jak nakonfigurovat automatické vypínání virtuálních počítačů v účtu testovacího prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445810"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Konfigurace automatického vypnutí virtuálních počítačů při nastavení odpojení pro účet testovacího prostředí
Po odpojení připojení ke vzdálené ploše můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s Windows Lab (šablona nebo student). Můžete také určit, jak dlouho mají služby testovacího prostředí čekat, než se uživatel znovu připojí, než se automaticky vypíná.

## <a name="enable-automatic-shutdown"></a>Povolit automatické vypnutí

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo možnost **Nastavení Labs** .
2. Zaškrtněte možnost **automaticky vypnout virtuální počítače při odpojení uživatele** .
3. Určete, jak dlouho by měly služby testovacího prostředí čekat, než se uživatel znovu připojí, než se virtuální počítače automaticky vypíná.

    ![Nastavení automatického vypnutí v účtu testovacího prostředí](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Toto nastavení se vztahuje na všechny laboratoře vytvořené v účtu testovacího prostředí. Tvůrce testovacího prostředí (Educator) může toto nastavení přepsat na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí bude mít vliv jenom na laboratoře, které se vytvoří po provedení změny.

    Pokud chcete toto nastavení zakázat, zrušte zaškrtnutí políčka **automaticky vypínat virtuální počítače, když uživatelé** na této stránce mají možnost Odpojit. 

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak vlastník testovacího prostředí může nakonfigurovat nebo přepsat toto nastavení na úrovni testovacího prostředí, najdete v [tomto článku](how-to-enable-shutdown-disconnect.md) .
