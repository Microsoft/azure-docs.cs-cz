---
title: Problémy s nasazení Linux virtuálního počítače v Azure | Microsoft Docs
description: Řešení potíží nasazení Linux virtuálního počítače v modelu nasazení Azurethe Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7e8db2683c301de8d41f7b7276e6290532e647d1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Problémy s nasazení Linux virtuálního počítače v Azure

Chcete-li vyřešit problémy při nasazení virtuálního počítače (VM) v Azure, zkontrolujte [top problémy](#top-issues) pro běžné chyby a řešení.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Nejdůležitější problémy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Cluster nemůže podporovat požadovaná velikost virtuálního počítače
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Opakujte tuto žádost pomocí menší velikost virtuálního počítače.
- Pokud velikost požadovaný virtuální počítač nelze změnit:
    - Zastavte všechny virtuální počítače v sadě dostupnosti. Klikněte na tlačítko **skupiny prostředků** > vaší skupiny prostředků > **prostředky** > vaše skupina dostupnosti > **virtuální počítače** > virtuálního počítače > **Zastavit**.
    - Po zastavit všechny virtuální počítače, vytvořte v požadovaná velikost virtuálního počítače.
    - Nejprve spusťte nový virtuální počítač a vyberte jednotlivé zastaven virtuálních počítačů a klikněte na příkaz spustit.


## <a name="the-cluster-does-not-have-free-resources"></a>Cluster nemá uvolnění prostředků
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Opakujte požadavek později.
- Pokud nový virtuální počítač může být součástí skupiny s jinou dostupnosti
    - Vytvoření virtuálního počítače v různých dostupnosti nastavit (ve stejné oblasti).
    - Přidáte nový virtuální počítač do stejné virtuální síti.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak lze aktivovat moje měsíční kredit pro sadu Visual studio Enterprise (BizSpark)

Aktivace měsíčního kreditu, najdete v části to [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Proč nelze nainstalovat ovladač GPU pro virtuálního počítače s Ubuntu vs?

V současné době Linux GPU podpora je k dispozici pouze na virtuálních počítačích Azure NC systémem Ubuntu Server 16.04 LTS. Další informace najdete v tématu [nastavení GPU ovladače pro N-series virtuální počítače se systémem Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Moje ovladače chybí pro virtuální počítač Linux N-Series

Ovladače pro virtuální počítače na bázi systému Linux jsou umístěné [zde](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nelze najít instanci GPU v rámci virtuální počítač N-Series

Abyste mohli využívat možnosti GPU Azure N-series virtuální počítače se systémem Windows Server 2016 nebo Windows Server 2012 R2, je nutné nainstalovat NVIDIA grafické ovladače pro každý virtuální počítač po nasazení. Informace o instalaci ovladačů je k dispozici pro [virtuálních počítačů Windows](../windows/n-series-driver-setup.md) a [virtuální počítače s Linuxem](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Je k dispozici N-Series virtuálních počítačů v mojí oblasti?

Můžete zkontrolovat dostupnost z [produkty, které jsou dostupné podle oblasti tabulku](https://azure.microsoft.com/regions/services)a ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nejsem moci zobrazit rodiny velikost virtuálního počítače, které chcete při změně velikosti virtuální počítač.

Když je spuštěný virtuální počítač, je nasazen na fyzickém serveru. Fyzické servery v oblastech Azure jsou seskupené v clusterech běžné fyzický hardware. Změna velikosti virtuálních počítačů, které vyžaduje virtuální počítač se přesunul na jiný hardware clustery se liší v závislosti na modelu nasazení byl použit k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic, nasazení cloudové služby musí být odebrány a znovu nasadit změnit virtuální počítače na velikost v jiné rodiny velikost.

- Virtuální počítače nasazené v modelu nasazení Resource Manager, je potřeba zastavit všechny virtuální počítače ve skupině před změnou velikosti žádné virtuální počítače v sadě dostupnosti dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedené velikosti virtuálního počítače není podporován při nasazení ve skupině dostupnosti.

Zvolte velikost, která je podporována v sadě dostupnosti clusteru. Doporučujeme při vytváření dostupnosti nastavit zvolit největší velikost virtuálního počítače, které se domníváte, že je třeba a mít které bude první nasazení do skupiny dostupnosti.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jaké distribucí Linux nebo verze jsou podporovány v Azure?

V seznamu na Linux můžete najít na [Azure-Endorsed distribuce](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžete přidat existující klasické virtuální počítač na sadu dostupnosti

Ano. Existující klasické virtuální počítač můžete přidat do nový nebo existující skupiny dostupnosti. Další informace najdete v části [přidat existující virtuální počítač do skupiny dostupnosti](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
