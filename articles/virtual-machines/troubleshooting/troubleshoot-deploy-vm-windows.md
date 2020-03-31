---
title: Poradce při potížích s nasazením problémů s virtuálním počítačem windows v Azure | Dokumenty společnosti Microsoft
description: Poradce při potížích s nasazením problémů s virtuálním počítačem Windows v modelu nasazení Azure Resource Manageru.
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
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921407"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením virtuálního počítače s Windows v Azure

Chcete-li vyřešit problémy s nasazením virtuálního počítače (VM) v Azure, zkontrolujte [hlavní problémy](#top-issues) s běžnými chybami a řešeními.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak můžu použít a nasadit image klienta windows do Azure?

Windows 7, Windows 8 nebo Windows 10 můžete použít v Azure pro scénáře pro vývoj a testování, pokud máte příslušné předplatné Visual Studia (dříve MSDN). Tento [článek](../windows/client-images.md) popisuje požadavky na způsobilost pro spuštění klienta Windows v Azure a použití iložek Azure Gallery.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak můžu nasadit virtuální počítač pomocí výhody hybridního použití (HUB)?

Existuje několik různých způsobů nasazení virtuálních počítačů s Windows s výhodou azure hybridní použití.

U předplatného smlouvy Enterprise:

• Nasazujte virtuální počítače z konkrétních ibi Marketplace, které jsou předem nakonfigurované s výhodou hybridního využití Azure.

Pro smlouvu Enterprise:

• Nahrajte vlastní virtuální počítač a nasaďte pomocí šablony Správce prostředků nebo Azure PowerShellu.

Další informace najdete v následujících materiálech:

 - [Přehled výhod hybridního využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nejčastější dotazy ke stažení](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Výhoda hybridního využití Azure pro Windows Server a Klienta Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Jak můžu využít výhodu hybridního využití v Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak aktivuji měsíční kredit pro Visual studio Enterprise (BizSpark)

Informace o aktivaci měsíčního kreditu naleznete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Jak přidat podnik ový vývoj/testování do smlouvy Enterprise (EA), abyste získali přístup k bitovým kopiím klienta okna?

Možnost vytvářet předplatná na základě nabídky Enterprise Dev/Test je omezena na vlastníky účtů, kterým k tomu udělil oprávnění podnikový správce. Vlastník účtu vytvoří předplatná prostřednictvím portálu účtu Azure a pak by měl přidat aktivní předplatitele Visual Studia jako spolusprávce. Aby mohli spravovat a využívat zdroje potřebné pro vývoj a testování. Další informace najdete v tématu věnovaném [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Chybí mi ovladače pro virtuální mísu Windows Řady N

Ovladače pro virtuální servery založené na Windows jsou umístěné [zde](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>V rámci virtuálního zařízení řady N nelze najít instanci GPU

Pokud chcete využít funkce GPU virtuálních počítačích řady Azure N se systémem Windows Server 2016 nebo Windows Server 2012 R2, musíte po nasazení nainstalovat grafické ovladače NVIDIA na každý virtuální počítač. Informace o nastavení ovladače jsou k dispozici pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md) a virtuální počítače s [Linuxem](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Jsou virtuální aplikace Řady N dostupné v mé oblasti?

Dostupnost můžete zkontrolovat v [tabulce Produkty dostupné podle oblastí](https://azure.microsoft.com/regions/services)a ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jaké inamblové image klientů můžu v Azure používat a nasazovat a jak je získat?

Windows 7, Windows 8 nebo Windows 10 můžete používat v Azure pro scénáře pro vývoj a testování za předpokladu, že máte příslušné předplatné Visual Studia (dříve MSDN). 

- Image Windows 10 jsou dostupné v Galerii Azure v rámci [vhodných nabídek pro vývoj a testování](../windows/client-images.md#eligible-offers). 
- Předplatitelé Visual Studia v rámci libovolného typu nabídky mohou také [adekvátně připravit a vytvořit](../windows/prepare-for-upload-vhd-image.md) 64bitovou bitovou kopii windows 7, Windows 8 nebo Windows 10 a pak [se nahrát do Azure](../windows/upload-generalized-managed.md). Použití zůstává omezeno na vývoj a testování aktivními předplatiteli sady Visual Studio.

Tento [článek](../windows/client-images.md) popisuje požadavky na způsobilost pro spuštění klienta Windows v Azure a použití iložek Azure Gallery.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Při změně velikosti virtuálního počítače nevidím rodinu velikosti virtuálního počítače, kterou chci.

Když je virtuální hod spuštěný, nasadí se na fyzický server. Fyzické servery v oblastech Azure jsou seskupeny do clusterů běžného fyzického hardwaru. Změna velikosti virtuálního počítače, který vyžaduje, aby se virtuální počítač přesunul do různých hardwarových clusterů, se liší v závislosti na tom, který model nasazení byl použit k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu klasického nasazení, nasazení cloudové služby musí být odebrány a znovu nasazené změnit virtuální počítače na velikost v jiné velikosti rodiny.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Virtuální počítače nasazené v modelu nasazení Resource Manager, musíte zastavit všechny virtuální počítače v sadě dostupnosti před změnou velikosti libovolného virtuálního počítače v sadě dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není podporována při nasazování v sadě dostupnosti.

Zvolte velikost, která je podporována v clusteru skupiny dostupnosti. Doporučuje se při vytváření sady dostupnosti zvolit největší velikost virtuálního počítače si myslíte, že potřebujete a mají být vaše první nasazení do sady dostupnosti.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžu do sady dostupnosti přidat existující klasický virtuální virtuální hod?

Ano. Existující klasický virtuální virtuální hod můžete přidat do nové nebo existující skupiny dostupnosti. Další informace najdete [v tématu Přidání existujícího virtuálního počítače do skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/).

Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
