---
title: Přizpůsobení konfigurací sítě pro virtuální počítače s podporou převzetí služeb při selhání | Dokumenty společnosti Microsoft
description: Poskytuje přehled přizpůsobení síťových konfigurací pro virtuální počítač s podporou převzetí služeb při selhání při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292854"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Přizpůsobení síťových konfigurací cílového virtuálního počítače Azure

Tento článek obsahuje pokyny k přizpůsobení konfigurací sítí na cílovém virtuálním počítači Azure (VM) při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do druhé pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak site recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Podporované síťové prostředky

Při replikaci virtuálních počítačů Azure můžete poskytnout následující konfigurace prostředků klíče pro virtuální počítač s podporou převzetí služeb při selhání:

- [Interní systém vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [Veřejná IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Skupina zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) pro podsíť i pro síť nic

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že plánujete konfigurace na straně obnovení předem.
- Vytvořte síťové prostředky předem. Zadejte jej jako vstup, aby služba Azure Site Recovery mohla ctít tato nastavení a zajistit, aby virtuální počítač s podporou převzetí služeb při selhání dodržoval tato nastavení.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Přizpůsobení konfigurace sítě převzetí služeb při selhání a testování služeb při selhání

1. Přejděte na **Položky replikované**. 
2. Vyberte požadovaný virtuální počítač Azure.
3. Vyberte **Vypočítat a síť** a vybrat **Upravit**. Všimněte si, že nastavení konfigurace nic zahrnují odpovídající prostředky ve zdroji. 

     ![Přizpůsobení konfigurací sítě s podporou převzetí služeb při selhání](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Vyberte testovací virtuální síť s podporou převzetí služeb při selhání. Můžete se rozhodnout ponechat prázdné a vybrat jeden v době převzetí služeb při selhání testu.
5. Síť s podporou převzetí služeb při selhání je Vybrat **upravit** v blízkosti síťové sítě, kterou chcete konfigurovat. V dalším okně, které se otevře, vyberte odpovídající předem vytvořené prostředky v testovacím umístění převzetí služeb při selhání a převzetí služeb při selhání.

    ![Úprava konfigurace nic](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Vyberte **OK**.

Obnovení webu bude nyní ctít tato nastavení a ujistěte se, že virtuální počítač při převzetí služeb při selhání je připojen k vybranému prostředku prostřednictvím odpovídající nic.

Když aktivujete převzetí služeb při selhání testu prostřednictvím plánu obnovení, bude vždy požádat virtuální síť Azure. Tato virtuální síť se použije pro převzetí služeb při selhání pro počítače, které neměly předkonfigurované nastavení převzetí služeb při selhání testu.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="unable-to-view-or-select-a-resource"></a>Nelze zobrazit nebo vybrat zdroj.

Pokud nemůžete vybrat nebo zobrazit síťový prostředek, projděte si následující kontroly a podmínky:

- Cílové pole pro síťový prostředek je povoleno pouze v případě, že zdrojový virtuální virtuální soud měl odpovídající vstup. To je založeno na principu, že pro scénář zotavení po havárii byste chtěli přesnou nebo zmenšenou verzi zdroje.
- Pro každý síťový prostředek jsou některé filtry použity v rozevíracím seznamu, aby bylo zajištěno, že se virtuální virtuální připojení s podporou převzetí služeb při selhání může připojit k vybranému prostředku a bude zachována spolehlivost převzetí služeb při selhání. Tyto filtry jsou založeny na stejných síťových podmínkách, které by byly ověřeny při konfiguraci zdrojového virtuálního počítače.

Interní ověření systému pro vyrovnávání zatížení:

- Předplatné a oblast vyrovnávání zatížení a cílový virtuální virtuální byl stejný.
- Virtuální síť přidružená k internímu nástrojovi pro vyrovnávání zatížení a cílovému virtuálnímu počítači by měla být stejná.
- Cílová virtuální pokladna veřejné SKU IP a vnitřní vyrovnávání zatížení sku by měla být stejná.
- Pokud je cílový virtuální virtuální počítače nakonfigurován tak, aby byl umístěn v zóně dostupnosti, zkontrolujte, jestli je správce zatížení zóna redundantní nebo je součástí jakékoli zóny dostupnosti. (Základní vyvažovače skladových položek nepodporují zóny a v tomto případě se v rozevíracím seznamu nezobrazí.)
- Ujistěte se, že interní nástroj pro vyrovnávání zatížení má předem vytvořený back-endový fond a front-endovou konfiguraci.

Public IP address (Veřejná IP adresa):

- Předplatné a oblast veřejné IP adresy a cílový virtuální virtuální byl stejný.
- Cílová virtuální pokladna veřejné SKU IP a vnitřní vyrovnávání zatížení sku by měla být stejná.

Network security group (Skupina zabezpečení sítě):
- Předplatné a oblast skupiny zabezpečení sítě a cílový virtuální virtuální byl stejný.


> [!WARNING]
> Pokud je cílový virtuální virtuální_ virtuální byl přidružený k sadě dostupnosti, musíte přidružit veřejnou IP adresu a interní správce zatížení stejné skladové položky k ip a internímu vyvažovači zatížení v sadě dostupnosti. Pokud tak neučiníte, převzetí služeb při selhání nemusí být úspěšné.
