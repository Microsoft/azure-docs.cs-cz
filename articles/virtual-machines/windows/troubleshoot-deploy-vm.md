---
title: Problémy s nasazení systému Windows virtuálního počítače v Azure | Microsoft Docs
description: Řešení potíží nasazení systému Windows virtuálního počítače v modelu nasazení Azurethe Resource Manager.
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: fda249295cf3bbf6f9577eb90a147f46524b95ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problémy s nasazení systému Windows virtuálního počítače v Azure

Chcete-li vyřešit problémy při nasazení virtuálního počítače (VM) v Azure, zkontrolujte [top problémy](#top-issues) pro běžné chyby a řešení.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Nejdůležitější problémy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak můžete používat a nasazení bitové kopie klienta systému windows do Azure?

Můžete Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje/testování Pokud máte předplatné příslušné sadě Visual Studio (dříve MSDN). To [článku](client-images.md) popisuje požadavky na podmínky pro spuštěné klienta systému Windows v Azure a používá Azure Galerie obrázků.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Nasazení virtuálního počítače pomocí výhody použití hybridní (ROZBOČOVAČ)

Existuje několik různých způsobů, jak nasadit virtuální počítače s Windows s výhodou použití hybridní Azure.

Předplatné Enterprise Agreement:

• Nasazení virtuálních počítačů z konkrétní Marketplace bitové kopie, které jsou předem nakonfigurovaným rozhraním výhody použití hybridní Azure.

Pro smlouvy Enterprise:

• Nahrát vlastní virtuální počítač a nasadit pomocí šablony Resource Manageru nebo Azure PowerShell.

Další informace najdete v následujících materiálech:

 - [Přehled hybridní použití výhody služby Azure ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nejčastější dotazy ke stažení](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Výhody použití Azure hybridní pro Windows Server a klienta Windows](hybrid-use-benefit-licensing.md).

 - [Jak můžete použít výhodou použití hybridní v Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak lze aktivovat moje měsíční kredit pro sadu Visual studio Enterprise (BizSpark)

Aktivace měsíčního kreditu, najdete v části to [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Postup přidání Enterprise pro vývoj/testování pro moje Enterprise Agreement (EA) získat přístup k bitové kopie klienta okna?

Schopnost vytvářet odběry podle nabídku Enterprise pro vývoj/testování je omezeno na vlastníky účet, který jste dostali k tomu oprávnění pomocí Správce podnikové sítě. Vlastníka účtu vytvoří předplatné prostřednictvím portálu účtů Azure a poté by měl přidejte active odběratele Visual Studio jako spolusprávce. Tak, aby můžou spravovat a používat prostředky potřebné pro vývoj a testování. Další informace najdete v tématu [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Moje ovladače chybí pro virtuální počítač Windows N-Series

Ovladače pro virtuální počítače na bázi Windows, které jsou umístěné [zde](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nelze najít instanci GPU v rámci virtuální počítač N-Series

Abyste mohli využívat možnosti GPU Azure N-series virtuální počítače se systémem Windows Server 2016 nebo Windows Server 2012 R2, je nutné nainstalovat NVIDIA grafické ovladače pro každý virtuální počítač po nasazení. Informace o instalaci ovladačů je k dispozici pro [virtuálních počítačů Windows](n-series-driver-setup.md) a [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Je k dispozici N-Series virtuálních počítačů v mojí oblasti?

Můžete zkontrolovat dostupnost z [produkty, které jsou dostupné podle oblasti tabulku](https://azure.microsoft.com/regions/services)a ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jaké obrazy klientů lze použít a nasadit v Azure a jak chcete získat je?

Můžete použít systém Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje/testování za předpokladu, že máte příslušné předplatné sady Visual Studio (dříve MSDN). 

- Bitové kopie systému Windows 10 jsou dostupné z Galerie Azure v rámci [nabízí vhodné pro vývoj/testování](client-images.md#eligible-offers). 
- Visual Studio odběratele v rámci libovolného typu nabídky můžete také [adekvátní Příprava a vytvoření](prepare-for-upload-vhd-image.md) bitovou kopii 64bitová verze Windows 7, Windows 8 nebo Windows 10 a pak [nahrát do Azure](upload-generalized-managed.md). Použití zůstane omezená na vývoj nebo testování odběratelům active Visual Studio.

To [článku](client-images.md) popisuje požadavky na podmínky pro spuštěné klienta systému Windows Azure a použít Azure Galerie obrázků.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nejsem moci zobrazit rodiny velikost virtuálního počítače, které chcete při změně velikosti virtuální počítač.

Když je spuštěný virtuální počítač, je nasazen na fyzickém serveru. Fyzické servery v oblastech Azure jsou seskupené v clusterech běžné fyzický hardware. Změna velikosti virtuálních počítačů, které vyžaduje virtuální počítač se přesunul na jiný hardware clustery se liší v závislosti na modelu nasazení byl použit k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic, nasazení cloudové služby musí být odebrány a znovu nasadit změnit virtuální počítače na velikost v jiné rodiny velikost.

- Virtuální počítače nasazené v modelu nasazení Resource Manager, je potřeba zastavit všechny virtuální počítače ve skupině před změnou velikosti žádné virtuální počítače v sadě dostupnosti dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedené velikosti virtuálního počítače není podporován při nasazení ve skupině dostupnosti.

Zvolte velikost, která je podporována v sadě dostupnosti clusteru. Doporučujeme při vytváření dostupnosti nastavit zvolit největší velikost virtuálního počítače, které se domníváte, že je třeba a mít které bude první nasazení do skupiny dostupnosti.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžete přidat existující klasické virtuální počítač na sadu dostupnosti

Ano. Existující klasické virtuální počítač můžete přidat do nový nebo existující skupiny dostupnosti. Další informace najdete v části [přidat existující virtuální počítač do skupiny dostupnosti](classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Další postup
Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
