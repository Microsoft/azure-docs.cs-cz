---
title: Konfigurace automatického vypnutí virtuálních počítačů v Azure Lab Services
description: Tento článek popisuje, jak nakonfigurovat automatické vypínání virtuálních počítačů v účtu testovacího prostředí.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: c0a147a81aaed88313a1b9aa4b0754d9a3badcb5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650030"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Konfigurace automatického vypnutí virtuálních počítačů pro účet testovacího prostředí

Můžete povolit několik funkcí řízení nákladů na automatické vypnutí, aby bylo možné aktivně zabránit dalším nákladům, když se virtuální počítače aktivně nepoužívají. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:
 
- Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné.
- Automaticky vypne virtuální počítače, když se uživatelé odpojí.
- Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.

Přečtěte si další podrobnosti o funkcích automatického vypnutí v části [maximalizovat řízení nákladů pomocí nastavení automatického vypnutí](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

## <a name="enable-automatic-shutdown"></a>Povolit automatické vypnutí

1. V [Azure Portal](https://portal.azure.com/) přejděte na stránku **účet testovacího prostředí** .
1. V nabídce vlevo vyberte **Nastavení Labs** .
1. Vyberte nastavení automatického vypnutí, která jsou vhodná pro váš scénář.  

    > [!div class="mx-imgBorder"]
    > ![Nastavení automatického vypnutí v účtu testovacího prostředí](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Tato nastavení se vztahují na všechna cvičení vytvořená v účtu testovacího prostředí. Tvůrce testovacího prostředí (Educator) může toto nastavení přepsat na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí bude mít vliv jenom na laboratoře, které se vytvoří po provedení změny.

    Chcete-li zakázat nastavení, zrušte zaškrtnutí políček u této stránky. 

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak vlastník testovacího prostředí může nakonfigurovat nebo přepsat toto nastavení na úrovni testovacího prostředí, najdete v tématu [Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí](how-to-enable-shutdown-disconnect.md) .
