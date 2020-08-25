---
title: Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí v Azure Lab Services
description: Naučte se, jak povolit nebo zakázat automatické vypnutí virtuálních počítačů při odpojení připojení ke vzdálené ploše.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 0df3fdcaea99c00461caac37a3b655d152a0e527
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798478"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurace automatického vypnutí virtuálních počítačů pro testovací prostředí

V tomto článku se dozvíte, jak můžete nakonfigurovat automatické vypnutí virtuálních počítačů pro testovací prostředí.

Můžete povolit několik funkcí řízení nákladů na automatické vypnutí, aby bylo možné aktivně zabránit dalším nákladům, když se virtuální počítače aktivně nepoužívají. Kombinace následujících tří funkcí automatického vypnutí a odpojení zachytává většinu případů, kdy uživatelé omylem odejdou z virtuálních počítačů, na kterých běží:
 
* Automaticky odpojí uživatele z virtuálních počítačů, které operační systém považuje za nečinné (pouze Windows).
* Automaticky vypne virtuální počítače, když se uživatelé odpojí (Windows & Linux).
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
> Pokud jste před odpojením relace RDP k VIRTUÁLNÍmu počítači vypnuli operační systém Windows (OS) na virtuálním počítači, funkce Automatické vypnutí nebude správně fungovat.  

## <a name="next-steps"></a>Další kroky

[Řídicí panel pro učebn Labs](use-dashboard.md)
