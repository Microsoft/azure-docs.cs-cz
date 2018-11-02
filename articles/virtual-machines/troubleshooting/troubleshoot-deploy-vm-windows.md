---
title: Řešení potíží s nasazením Windows virtuální počítač v Azure | Dokumentace Microsoftu
description: Řešení potíží s nasazením Windows virtuálního počítače v modelu nasazení Azurethe Resource Manager.
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
ms.openlocfilehash: 665e3cbf0c0cfbcd4b2d19f34b9be1dc4a45b09e
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747792"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením Windows virtuální počítač v Azure

Řešení potíží s problémy při nasazení virtuálního počítače (VM) v Azure, najdete v tématu [nejčastější problémy](#top-issues) pro běžné chyby a jejich řešení.

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Nejčastější problémy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak můžete použít a nasadit image klienta windows do Azure?

Můžete Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje/testování v případě, že máte příslušné předplatné sady Visual Studio (dříve MSDN). To [článku](../windows/client-images.md) popisuje požadavky zakládající nárok pro spuštění klienta Windows v Azure a použití Image Galerie Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Nasazení virtuálního počítače pomocí výhoda hybridního používání (HUB)

Existuje několik různých způsobů, jak nasadit virtuální počítače Windows Azure Hybrid Use Benefit.

Pro předplatné smlouvy Enterprise:

• Virtuální počítače nasadit konkrétní z imagí z Marketplace s předem nakonfigurovaným programem Azure Hybrid Use Benefit.

Pro smlouvy Enterprise:

• Nahrání vlastního virtuálního počítače a nasazení pomocí šablony Resource Manageru nebo Azure Powershellu.

Další informace najdete v následujících materiálech:

 - [Přehled služby Azure Hybrid Use Benefit ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nejčastější dotazy ke stažení](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Program Azure Hybrid Use Benefit pro Windows Server a klienta Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Jak můžete program Hybrid Use Benefit v Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak si můžu aktivovat svůj měsíční kredit pro Visual studio Enterprise (BizSpark)

Pokud chcete aktivovat svůj měsíční kredit, najdete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Postup přidání Enterprise pro vývoj/testování pro moje Enterprise Agreement (EA) získat přístup k obrázkům okna klienta?

Možnost vytvářet předplatná na základě nabídky Enterprise pro vývoj/testování je omezená na vlastníky účtů, který jste dostali oprávnění správce podniku. Vlastník účtu vytvoří předplatné na portálu účtů Azure a pak by měl přidat aktivní předplatitele sady Visual Studio jako další spolupracující správce. Tak, aby mohli spravovat a využívat prostředky potřebné pro vývoj a testování. Další informace najdete v tématu [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Ovladače nebyly nalezeny virtuální počítače řady N-Series Windows

Ovladače pro virtuální počítače se systémem Windows jsou umístěny [tady](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nemůžu najít instanci GPU v rámci Můj virtuální počítač řady N-Series

Abyste mohli využívat výhod GPU virtuální počítače Azure řady N-series s Windows serverem 2016 nebo Windows Server 2012 R2, je nutné nainstalovat ovladače NVIDIA grafiky na každém virtuálním počítači po nasazení. Informace o instalaci ovladačů je k dispozici pro [virtuální počítače s Windows](../windows/n-series-driver-setup.md) a [virtuální počítače s Linuxem](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Je virtuální počítače řady N-Series dostupné v mojí oblasti?

Můžete zkontrolovat dostupnost z [dostupné produkty v jednotlivých oblasti tabulky](https://azure.microsoft.com/regions/services), ceny a [tady](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jaké imagí klienta můžu používat a nasazovat v Azure, a jak k můžu získat?

Můžete použít Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje a testování za předpokladu, že máte příslušné předplatné sady Visual Studio (dříve MSDN). 

- Image Windows 10 jsou dostupná v galerii Azure v rámci [způsobilé pro vývoj/testování nabízí](../windows/client-images.md#eligible-offers). 
- Předplatitelé sady Visual Studio v rámci libovolného typu nabídka lze také [odpovídajícím způsobem připravit a vytvořit](../windows/prepare-for-upload-vhd-image.md) 64bitová verze Windows 7, Windows 8 nebo Windows 10 image a pak [nahrát do Azure](../windows/upload-generalized-managed.md). Použití zůstává omezen na vývoj a testování aktivní předplatitelé sady Visual Studio.

To [článku](../windows/client-images.md) popisuje požadavky zakládající nárok pro spuštění klienta Windows v Azure a použití Image Galerie Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nejde mi zobrazíte řadu velikostí virtuálního počítače, který chci, aby při změně velikosti virtuálního počítače.

Když na virtuálním počítači běží, je nasazen na fyzickém serveru. Fyzické servery v oblasti Azure, které jsou seskupené v clusterech běžné fyzický hardware. Změnou velikosti virtuálního počítače, které vyžaduje virtuální počítač přesunout na jiný hardware clustery se liší v závislosti na tom, které model nasazení byla použita k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic, nasazení cloudové služby musí být odebrány a znovu nasadit, chcete-li změnit velikost v jinou řadu velikostí virtuálních počítačů.

- Virtuální počítače nasazené v modelu nasazení Resource Manager, je nutné zastavit všechny virtuální počítače ve skupině dostupnosti před změnou velikosti ze všech virtuálních počítačů ve skupině dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není podporována při nasazení ve skupině dostupnosti.

Zvolte velikost, která je podporována v clusteru pro skupinu dostupnosti. Doporučujeme vytvořit skupinu dostupnosti nastavena na hodnotu zvolte největší velikost virtuálního počítače, které se domníváte, že potřebujete a mít, které bude váš první nasazení do skupiny dostupnosti.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžete přidat existující klasický virtuální počítač do skupiny dostupnosti?

Ano. Chcete nové nebo existující skupinu dostupnosti můžete přidat existující klasický virtuální počítač. Další informace najdete v části [přidat existující virtuální počítač do skupiny dostupnosti](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Další postup
Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
