---
title: Konfigurace automatického vypnutí virtuálních počítačů v Azure Lab Services
description: Tento článek popisuje, jak nakonfigurovat automatické vypínání virtuálních počítačů v účtu testovacího prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588203"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Konfigurace automatického vypnutí virtuálních počítačů při nastavení odpojení pro účet testovacího prostředí
Po odpojení připojení ke vzdálené ploše můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s Windows Lab (šablona nebo student). Můžete také určit, jak dlouho mají služby testovacího prostředí čekat, než se uživatel znovu připojí, než se automaticky vypíná.

## <a name="enable-automatic-shutdown"></a>Povolit automatické vypnutí

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo možnost **Nastavení Labs** .
2. Zaškrtněte možnost **automaticky vypnout virtuální počítače při odpojení uživatele** .
3. Určete, jak dlouho by měly služby testovacího prostředí čekat, než se uživatel znovu připojí, než se virtuální počítače automaticky vypíná.

    ![Nastavení automatického vypnutí v účtu testovacího prostředí](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Toto nastavení se vztahuje na všechny laboratoře vytvořené v účtu testovacího prostředí. Tvůrce testovacího prostředí (Educator) může toto nastavení přepsat na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí bude mít vliv jenom na laboratoře, které se vytvoří po provedení změny.

    Pokud chcete toto nastavení zakázat, zrušte zaškrtnutí políčka **automaticky vypínat virtuální počítače, když uživatelé** na této stránce mají možnost Odpojit. 

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak vlastník testovacího prostředí může nakonfigurovat nebo přepsat toto nastavení na úrovni testovacího prostředí, najdete v [tomto článku](how-to-enable-shutdown-disconnect.md) .
