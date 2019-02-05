---
title: Řešení potíží s nasazením Linux virtuálního počítače v Azure | Dokumentace Microsoftu
description: Řešení potíží s nasazením Linux virtuálního počítače v modelu nasazení Azurethe Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 56df020b7948ff41ee7710fca19d05924e118c81
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729706"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením Linux virtuálního počítače v Azure

Řešení potíží s problémy při nasazení virtuálního počítače (VM) v Azure, najdete v tématu [nejčastější problémy](#top-issues) pro běžné chyby a jejich řešení.

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Nejčastější problémy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Cluster nepodporuje požadovanou velikost virtuálního počítače
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Opakujte žádost, použijte menší velikost virtuálního počítače.
- Pokud velikost pro požadovaný virtuální počítač nejde změnit:
    - Zastavte všechny virtuální počítače ve skupině dostupnosti. Klikněte na tlačítko **skupiny prostředků** > vaší skupiny prostředků > **prostředky** > dostupnosti > **virtuálních počítačů** > váš virtuální počítač >  **Zastavit**.
    - Po zastavení všech virtuálních počítačů, vytvoření virtuálního počítače do požadované velikosti.
    - Nejprve spusťte nový virtuální počítač a potom vyberte všechny zastavené virtuální počítače a klikněte na příkaz spustit.


## <a name="the-cluster-does-not-have-free-resources"></a>Cluster nemá žádné volné prostředky
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Zopakujte později.
- Pokud nový virtuální počítač může být součástí do jiné skupiny dostupnosti
    - Vytvoření virtuálního počítače v různých dostupnosti (ve stejné oblasti).
    - Přidáte nový virtuální počítač do stejné virtuální síti.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak si můžu aktivovat svůj měsíční kredit pro Visual studio Enterprise (BizSpark)

Pokud chcete aktivovat svůj měsíční kredit, najdete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Proč nelze nainstalovat ovladače GPU pro virtuální počítač s Ubuntu NV?

V současné době Linux GPU podpora je dostupná jenom na virtuálních počítačích Azure NC systémem Ubuntu Server 16.04 LTS. Další informace najdete v tématu [nastavit ovladače GPU pro virtuální počítače řady N-series s Linuxem](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Chybí ovladače pro své Linuxové virtuální počítače řady N-Series

Ovladače pro virtuální počítače se systémem Linux se nacházejí [tady](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nemůžu najít instanci GPU v rámci Můj virtuální počítač řady N-Series

Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Windows serverem 2016 nebo Windows Server 2012 R2, je nutné nainstalovat ovladače NVIDIA grafiky na každém virtuálním počítači po nasazení. Informace o instalaci ovladačů je k dispozici pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md) a [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Je virtuální počítače řady N-Series dostupné v mojí oblasti?

Můžete zkontrolovat dostupnost z [dostupné produkty v jednotlivých oblasti tabulky](https://azure.microsoft.com/regions/services), ceny a [tady](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nejde mi zobrazíte řadu velikostí virtuálního počítače, který chci, aby při změně velikosti virtuálního počítače.

Když na virtuálním počítači běží, je nasazen na fyzickém serveru. Fyzické servery v oblasti Azure, které jsou seskupené v clusterech běžné fyzický hardware. Změnou velikosti virtuálního počítače, které vyžaduje virtuální počítač přesunout na jiný hardware clustery se liší v závislosti na tom, které model nasazení byla použita k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic, nasazení cloudové služby musí být odebrány a znovu nasadit, chcete-li změnit velikost v jinou řadu velikostí virtuálních počítačů.

- Virtuální počítače nasazené v modelu nasazení Resource Manager, je nutné zastavit všechny virtuální počítače ve skupině dostupnosti před změnou velikosti ze všech virtuálních počítačů ve skupině dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není podporována při nasazení ve skupině dostupnosti.

Zvolte velikost, která je podporována v clusteru pro skupinu dostupnosti. Doporučujeme vytvořit skupinu dostupnosti nastavena na hodnotu zvolte největší velikost virtuálního počítače, které se domníváte, že potřebujete a mít, které bude váš první nasazení do skupiny dostupnosti.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jaké distribuce a verze Linux jsou podporovány v Azure?

Můžete najít na seznamu v systému Linux [Azure-Endorsed distribuce](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžete přidat existující klasický virtuální počítač do skupiny dostupnosti?

Ano. Chcete nové nebo existující skupinu dostupnosti můžete přidat existující klasický virtuální počítač. Další informace najdete v části [přidat existující virtuální počítač do skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Další postup
Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
