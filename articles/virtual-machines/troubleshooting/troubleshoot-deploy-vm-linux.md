---
title: Poradce při potížích s nasazením problémů s virtuálním počítačem s Linuxem v Azure | Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením problémů s virtuálním počítačem Linuxu v modelu nasazení Azure Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5a8a58a3935b7cd5efb8565f7e3278ccaae4f4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921432"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením linuxového virtuálního počítače v Azure

Chcete-li vyřešit problémy s nasazením virtuálního počítače (VM) v Azure, zkontrolujte [hlavní problémy](#top-issues) s běžnými chybami a řešeními.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Cluster nemůže podporovat požadovanou velikost virtuálního počítače.
\<vlastnosti supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Opakujte požadavek pomocí menší velikosti virtuálního počítače.
- Pokud velikost požadovaného virtuálního počítače nelze změnit:
    - Zastavte všechny virtuální ho disponibilní služby v sadě dostupnosti. Klikněte na **skupiny prostředků** > skupině prostředků > **zdroje** > vaší skupiny **dostupnosti** > virtuální počítače > virtuálním počítači > **Stop**.
    - Po zastavení všech virtuálních počítače vytvořte virtuální počítače v požadované velikosti.
    - Nejdřív spusťte nový virtuální virtuální ms, pak vyberte každý z zastavených virtuálních ms a klikněte na Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Cluster nemá volné prostředky.
\<vlastnosti supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Opakujte požadavek později.
- Pokud nový virtuální virtuální město může být součástí jiné sady dostupnosti
    - Vytvořte virtuální virtuální ho v jiné sadě dostupnosti (ve stejné oblasti).
    - Přidejte nový virtuální počítač do stejné virtuální sítě.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktivuji měsíční kredit pro Visual studio Enterprise (BizSpark)

Informace o aktivaci měsíčního kreditu naleznete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Proč nemohu nainstalovat ovladač GPU pro Ubuntu NV VM?

V současné době je podpora LINUX GPU dostupná jenom na virtuálních počítačích Azure NC se systémem Ubuntu Server 16.04 LTS. Další informace najdete [v tématu Nastavení ovladačů GPU pro virtuální počítače řady N se systémem Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Moje ovladače chybí pro můj Linux N-Series VM

Ovladače pro virtuální počítače založené na Linuxu jsou umístěné [zde](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>V rámci virtuálního zařízení řady N nelze najít instanci GPU

Pokud chcete využít funkce GPU virtuálních počítačích řady Azure N se systémem Windows Server 2016 nebo Windows Server 2012 R2, musíte po nasazení nainstalovat grafické ovladače NVIDIA na každý virtuální počítač. Informace o nastavení ovladače jsou k dispozici pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md) a virtuální počítače s [Linuxem](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Jsou virtuální aplikace Řady N dostupné v mé oblasti?

Dostupnost můžete zkontrolovat v [tabulce Produkty dostupné podle oblastí](https://azure.microsoft.com/regions/services)a ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Při změně velikosti virtuálního počítače nevidím rodinu velikosti virtuálního počítače, kterou chci.

Když je virtuální hod spuštěný, nasadí se na fyzický server. Fyzické servery v oblastech Azure jsou seskupeny do clusterů běžného fyzického hardwaru. Změna velikosti virtuálního počítače, který vyžaduje, aby se virtuální počítač přesunul do různých hardwarových clusterů, se liší v závislosti na tom, který model nasazení byl použit k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu klasického nasazení, nasazení cloudové služby musí být odebrány a znovu nasazené změnit virtuální počítače na velikost v jiné velikosti rodiny.

- Virtuální počítače nasazené v modelu nasazení Resource Manager, musíte zastavit všechny virtuální počítače v sadě dostupnosti před změnou velikosti libovolného virtuálního počítače v sadě dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není podporována při nasazování v sadě dostupnosti.

Zvolte velikost, která je podporována v clusteru skupiny dostupnosti. Doporučuje se při vytváření sady dostupnosti zvolit největší velikost virtuálního počítače si myslíte, že potřebujete a mají být vaše první nasazení do sady dostupnosti.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jaké linuxové distribuce/verze jsou v Azure podporované?

Seznam najdete na Linuxu [v Azure-endorsed Distributions](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžu do sady dostupnosti přidat existující klasický virtuální virtuální hod?

Ano. Existující klasický virtuální virtuální hod můžete přidat do nové nebo existující skupiny dostupnosti. Další informace najdete [v tématu Přidání existujícího virtuálního počítače do skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/).

Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
