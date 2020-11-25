---
title: Řešení potíží s nasazením virtuálních počítačů se systémem Linux v Azure | Microsoft Docs
description: Řešení potíží s nasazením virtuálního počítače se systémem Linux v modelu nasazení Azure Resource Manager.
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
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022802"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením linuxového virtuálního počítače v Azure

Pokud chcete řešit problémy s nasazením virtuálního počítače v Azure, přečtěte si [nejdůležitější problémy](#top-issues) s běžnými chybami a řešeními.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Cluster nepodporuje požadovanou velikost virtuálního počítače.
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Opakujte požadavek s menší velikostí virtuálního počítače.
- Pokud velikost požadovaného virtuálního počítače nejde změnit:
    - Zastavte všechny virtuální počítače ve skupině dostupnosti. Klikněte na **skupiny prostředků** > vaší skupiny prostředků > **prostředky** > vaší skupině dostupnosti > **Virtual Machines** > **zastavte** virtuální počítač >.
    - Až se všechny virtuální počítače zastaví, vytvořte virtuální počítač v požadované velikosti.
    - Nejprve spusťte nový virtuální počítač a potom vyberte všechny zastavené virtuální počítače a klikněte na tlačítko Spustit.


## <a name="the-cluster-does-not-have-free-resources"></a>Cluster nemá volné prostředky.
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Opakujte požadavek později.
- Pokud může být nový virtuální počítač součástí jiné skupiny dostupnosti
    - Vytvořte virtuální počítač v jiné skupině dostupnosti (ve stejné oblasti).
    - Přidejte nový virtuální počítač do stejné virtuální sítě.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Návody aktivovat svůj měsíční kredit pro Visual Studio Enterprise (BizSpark)

Další informace o aktivaci měsíčního kreditu najdete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Proč nemůžu nainstalovat ovladač GPU pro virtuální počítač Ubuntu NV?

V současné době je podpora pro Linux GPU dostupná jenom na virtuálních počítačích Azure NC, na kterých běží Ubuntu Server 16,04 LTS. Další informace najdete v tématu [nastavení ovladačů GPU pro virtuální počítače řady N-Series se systémem Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Pro virtuální počítač se systémem Linux N-Series chybí moje ovladače

Pokyny k instalaci ovladačů pro virtuální počítače se systémem Linux najdete [tady](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nemůžu najít instanci GPU v mém virtuálním počítači řady N-Series

Pokud chcete využívat možnosti GPU pro virtuální počítače řady Azure N-Series, musíte nainstalovat ovladače grafiky na každý virtuální počítač po nasazení. Informace o nastavení ovladače jsou k dispozici [zde](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Jsou virtuální počítače řady N-Series dostupné v mojí oblasti?

Dostupnost můžete ověřit z [tabulky dostupné podle oblastí](https://azure.microsoft.com/regions/services)a na ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nedá se mi zobrazit rodina velikostí virtuálních počítačů, kterou chci při změně velikosti virtuálního počítače.

Když je virtuální počítač spuštěný, je nasazený na fyzický server. Fyzické servery v oblastech Azure se seskupují v clusterech společného fyzického hardwaru. Změna velikosti virtuálního počítače, který vyžaduje přesun virtuálního počítače do různých hardwarových clusterů, se liší v závislosti na modelu nasazení, který se použil k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic je nutné odebrat a znovu nasadit nasazení cloudové služby, aby se virtuální počítače změnily na velikost v jiné rodině velikostí.

- Virtuální počítače nasazené v modelu nasazení Správce prostředků před změnou velikosti libovolného virtuálního počítače ve skupině dostupnosti musíte zastavit všechny virtuální počítače ve skupině dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není při nasazení v sadě dostupnosti podporována.

Vyberte velikost podporovanou v clusteru skupiny dostupnosti. Doporučuje se při vytváření skupiny dostupnosti zvolit největší velikost virtuálního počítače, kterou si myslíte, že potřebujete, a to je vaše první nasazení do skupiny dostupnosti.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jaké distribuce a verze systému Linux jsou podporovány v Azure?

Seznam najdete v části Linux v [distribucích schválené pro Azure](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžu přidat existující klasický virtuální počítač do skupiny dostupnosti?

Ano. Existující klasický virtuální počítač můžete přidat do nové nebo existující skupiny dostupnosti. Další informace najdete v tématu [Přidání existujícího virtuálního počítače do skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Další kroky
Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/).

Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
