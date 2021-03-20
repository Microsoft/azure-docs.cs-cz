---
title: Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí v Azure Lab Services
description: Naučte se, jak povolit nebo zakázat automatické vypnutí virtuálních počítačů při odpojení připojení ke vzdálené ploše.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 8f9080f3b7b762d3b9fa448a903a4167cd2cec4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433934"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí

V tomto článku se dozvíte, jak můžete nakonfigurovat automatické vypnutí virtuálních počítačů pro testovací prostředí.

Můžete povolit několik funkcí řízení nákladů na automatické vypnutí, aby bylo možné aktivně zabránit dalším nákladům, když se virtuální počítače aktivně nepoužívají. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:
 
* Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné.
* Automaticky vypne virtuální počítače, když se uživatelé odpojí.
* Automaticky vypne virtuální počítače, které jsou spuštěné, ale uživatelé se nepřipojí.

Přečtěte si další podrobnosti o funkcích automatického vypnutí v části [maximalizovat řízení nákladů pomocí nastavení automatického vypnutí](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

Správce účtu testovacího prostředí může nakonfigurovat toto nastavení pro účet testovacího prostředí, ve kterém vytvoříte Labs. Další informace najdete v tématu [Konfigurace automatického vypnutí virtuálních počítačů pro účet testovacího prostředí](how-to-configure-lab-accounts.md). Jako vlastník testovacího prostředí můžete nastavení přepsat při vytváření testovacího prostředí nebo po vytvoření testovacího prostředí. 

## <a name="configure-for-the-lab-level"></a>Konfigurace pro úroveň testovacího prostředí

Nastavení automatického vypínání můžete nakonfigurovat v [Azure Lab Services](https://labs.azure.com/).

* Při vytváření testovacího prostředí (v **zásadách testovacího prostředí**) nebo
* Po vytvoření testovacího prostředí (v **Nastavení**)

> [!div class="mx-imgBorder"]
> ![Konfigurace v době vytváření testovacího prostředí](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Zkontrolujte podrobnosti o automatickém vypnutí v části [maximalizovat řízení nákladů pomocí nastavení automatického vypnutí](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

> [!WARNING]
> Pokud jste před odpojením relace RDP k virtuálnímu počítači vypnuli operační systém Linux nebo Windows (OS) na virtuálním počítači, funkce Automatické vypnutí nebude správně fungovat.  
## <a name="next-steps"></a>Další kroky

[Řídicí panel pro cvičení](use-dashboard.md)
