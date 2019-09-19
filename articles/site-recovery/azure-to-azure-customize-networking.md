---
title: Přizpůsobení síťových konfigurací pro virtuální počítač pro převzetí služeb při selhání | Microsoft Docs
description: V této části najdete Přehled přizpůsobení síťových konfigurací pro virtuální počítač s podporou převzetí služeb při selhání při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 1905d6afb023b1bed15f1359fed8477d815acb45
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087710"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Přizpůsobení síťových konfigurací cílového virtuálního počítače Azure

Tento článek poskytuje pokyny k přizpůsobení síťových konfigurací v cílovém virtuálním počítači Azure při replikaci a obnovování virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Přečtěte si, jak Site Recovery poskytuje zotavení po havárii pro [Tento scénář](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Podporované síťové prostředky

Při replikaci virtuálních počítačů Azure je možné k virtuálnímu počítači s podporou převzetí služeb při selhání zadat následující hlavní konfigurace prostředků.

- [Interní nástroj pro vyrovnávání zatížení.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Veřejná IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Skupina zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) pro podsíť i pro síťovou kartu

 > [!IMPORTANT]
  > Tato nastavení se v tuto chvíli podporují jenom v operaci převzetí služeb při selhání, a ne pro testovací převzetí služeb při selhání.

## <a name="pre-requisites"></a>Požadavky

- Ujistěte se, že vaše konfigurace na straně obnovení naplánujete předem.
- Je potřeba vytvořit síťové prostředky předem. Poskytněte ji jako vstup, aby služba Azure Site Recovery mohla přijmout tato nastavení a zajistit, aby se virtuální počítač s podporou převzetí služeb při selhání dodržoval s těmito nastaveními.

## <a name="steps-to-customize-failover-networking-configurations"></a>Postup přizpůsobení síťových konfigurací s podporou převzetí služeb při selhání

1. Přejděte na **replikované položky**. 
2. Klikněte na požadovaný virtuální počítač Azure.
3. Klikněte na **výpočty a síť**a **upravte**. Všimnete si, že nastavení konfigurace síťových adaptérů zahrnují odpovídající prostředky ve zdroji. 

     ![Přizpůsobit](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Klikněte na **Upravit** u síťového adaptéru, který chcete konfigurovat. V dalším okně, které se otevře, vyberte odpovídající předem vytvořené prostředky v cíli.

    ![Síťová karta – podrobné procházení](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Klikněte na **OK**.

Site Recovery teď budou dodržovat tato nastavení a zajistěte, aby se virtuální počítač pro převzetí služeb při selhání připojil k vybranému prostředku přes odpovídající síťové rozhraní.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unable-to-view-or-select-a-resource"></a>Nepovedlo se zobrazit nebo vybrat prostředek.

Pokud nemůžete vybrat nebo zobrazit síťový prostředek, Projděte si následující kontroly & podmínky:

- Cílové pole pro síťový prostředek je povolené jenom v případě, že zdrojový virtuální počítač měl odpovídající vstup. To je založeno na principu, který se nachází ve scénáři zotavení po havárii, a to podle toho, jakou verzi zdroje máte přesně nebo se škáluje.
- U každého ze svých síťových prostředků se v rozevíracím seznamu používají některé filtry, aby se zajistilo, že se virtuální počítač s podporou převzetí služeb při selhání může připojit k vybranému prostředku a že se udržuje spolehlivost při selhání. Tyto filtry jsou založené na stejných síťových podmínkách, které by se ověřily při konfiguraci zdrojového virtuálního počítače.

Ověření interního nástroje pro vyrovnávání zatížení:

1. Předplatné a oblast a cílový virtuální počítač by měly být stejné.
2. Virtuální síť přidružená k internímu Load Balancer a cílovou VMshould je stejná.
3. SKU veřejné IP adresy cílového virtuálního počítače a SKU interního vyrovnávání zatížení sítě by měly být stejné.
4. Pokud je cílový virtuální počítač umístěný v zóně dostupnosti, zkontrolujte, jestli je nástroj pro vyrovnávání zatížení v zóně redundantní nebo součástí žádné zóny dostupnosti. (Základní nástroje pro vyrovnávání zatížení (SKU) nepodporují zóny a v tomto případě se v rozevíracím seznamu nezobrazí.)
5. Ujistěte se, že interní nástroj pro vyrovnávání zatížení má předem vytvořený back-end fond a konfiguraci front-endu.


Veřejná IP adresa:
    
1. Předplatné a oblast veřejné IP adresy a cílový virtuální počítač by měly být stejné.
2. SKU veřejné IP adresy cílového virtuálního počítače a SKU interního vyrovnávání zatížení sítě by měly být stejné.

Skupina zabezpečení sítě:
1. Předplatné a oblast skupiny zabezpečení sítě a cílový virtuální počítač by měly být stejné.


> [!WARNING]
> Pokud je cílový virtuální počítač přidružený ke skupině dostupnosti, musíte k ní přidružit veřejnou službu Load Balancer nebo interní nástroj pro vyrovnávání zatížení stejné SKU, jako je veřejná IP adresa nebo interní nástroj pro vyrovnávání zatížení virtuálního počítače ve skupině dostupnosti. V takovém případě by mohlo dojít k selhání selhání.
