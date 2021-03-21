---
title: Přizpůsobení síťových konfigurací pro virtuální počítač s podporou převzetí služeb při selhání | Microsoft Docs
description: V této části najdete Přehled přizpůsobení síťových konfigurací pro virtuální počítač s podporou převzetí služeb při selhání při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541186"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Přizpůsobení síťových konfigurací cílového virtuálního počítače Azure

Tento článek poskytuje pokyny k přizpůsobení síťových konfigurací v cílovém virtuálním počítači Azure při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Přečtěte si, jak Site Recovery poskytuje zotavení po havárii pro [Tento scénář](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Podporované síťové prostředky

Při replikaci virtuálních počítačů Azure můžete pro virtuální počítač s podporou převzetí služeb při selhání zadat následující hlavní konfigurace prostředků:

- [Interní nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md)
- [Veřejná IP adresa](../virtual-network/public-ip-addresses.md)
- [Skupina zabezpečení sítě](../virtual-network/manage-network-security-group.md) pro podsíť i pro síťovou kartu

## <a name="prerequisites"></a>Předpoklady

- Ujistěte se, že vaše konfigurace na straně obnovení naplánujete předem.
- Vytvořte si síťové prostředky předem. Poskytněte ji jako vstup, aby služba Azure Site Recovery mohla přijmout tato nastavení a zajistit, aby se virtuální počítač s podporou převzetí služeb při selhání dodržoval s těmito nastaveními.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Přizpůsobení konfigurací převzetí služeb při selhání a testovacího převzetí služeb

1. Přejít na **replikované položky**. 
2. Vyberte požadovaný virtuální počítač Azure.
3. Vyberte **výpočty a síť** a vyberte **Upravit**. Všimněte si, že nastavení konfigurace síťových adaptérů obsahují odpovídající prostředky ve zdroji. 

     ![Přizpůsobení síťových konfigurací převzetí služeb při selhání](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Vyberte virtuální síť testovacího převzetí služeb při selhání.
5. Síť s podporou převzetí služeb při selhání je výběr **Upravit** u síťového adaptéru, který chcete konfigurovat. V dalším okně, které se otevře, vyberte odpovídající předem vytvořené prostředky v části testovací převzetí služeb při selhání a umístění pro převzetí služeb při selhání.

    ![Úprava konfigurace síťové karty](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Vyberte **OK**.

Site Recovery teď budou dodržovat tato nastavení a zajistěte, aby se virtuální počítač pro převzetí služeb při selhání připojil k vybranému prostředku přes odpovídající síťové rozhraní.

Při aktivaci testovacího převzetí služeb při selhání prostřednictvím plánu obnovení se vždy požádá o virtuální síť Azure. Tato virtuální síť se použije pro testovací převzetí služeb při selhání pro počítače, které nemají předem nakonfigurovaná nastavení testovacího převzetí služeb při selhání.

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="unable-to-view-or-select-a-resource"></a>Nepovedlo se zobrazit nebo vybrat prostředek.

Pokud nemůžete vybrat nebo zobrazit síťový prostředek, Projděte si následující kontroly a podmínky:

- Cílové pole pro síťový prostředek je povolené jenom v případě, že zdrojový virtuální počítač měl odpovídající vstup. To je založeno na principu, který se nachází ve scénáři zotavení po havárii, pokud chcete, aby byl váš zdroj přesný nebo se škálováním na více verzí.
- U každého síťového prostředku se v rozevíracím seznamu aplikují některé filtry, které zajistí, že se virtuální počítač s podporou převzetí služeb při selhání může připojit k vybranému prostředku a zachová se spolehlivost převzetí služeb při selhání. Tyto filtry jsou založené na stejných síťových podmínkách, které by se ověřily při konfiguraci zdrojového virtuálního počítače.

Ověření interního nástroje pro vyrovnávání zatížení:

- Předplatné a oblast nástroje pro vyrovnávání zatížení a cílový virtuální počítač by měly být stejné.
- Virtuální síť přidružená k internímu nástroji pro vyrovnávání zatížení a cílový virtuální počítač by měla být stejná.
- SKU veřejné IP adresy cílového virtuálního počítače a SKU interního nástroje pro vyrovnávání zatížení by měly být stejné.
- Pokud je cílový virtuální počítač umístěný v zóně dostupnosti, zkontrolujte, jestli je nástroj pro vyrovnávání zatížení v zóně redundantní nebo součástí žádné zóny dostupnosti. (Základní nástroje pro vyrovnávání zatížení SKU nepodporují zóny a v tomto případě se v rozevíracím seznamu nezobrazí.)
- Ujistěte se, že interní nástroj pro vyrovnávání zatížení má předem vytvořený fond back-end a konfiguraci front-endu.

Veřejná IP adresa:

- Předplatné a oblast veřejné IP adresy a cílového virtuálního počítače by měly být stejné.
- SKU veřejné IP adresy cílového virtuálního počítače a SKU interního nástroje pro vyrovnávání zatížení by měly být stejné.

Skupina zabezpečení sítě:
- Předplatné a oblast skupiny zabezpečení sítě a cílový virtuální počítač by měly být stejné.


> [!WARNING]
> Pokud je cílový virtuální počítač přidružený ke skupině dostupnosti, musíte k ní přidružit veřejnou IP adresu a interní nástroj pro vyrovnávání zatížení stejné SKU s tím, že veřejná IP adresa virtuálního počítače a interní nástroj pro vyrovnávání zatížení ve skupině dostupnosti. Pokud to neuděláte, převzetí služeb při selhání nemusí být úspěšné.
