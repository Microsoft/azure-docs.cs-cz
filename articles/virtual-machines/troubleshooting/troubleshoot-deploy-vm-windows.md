---
title: Řešení potíží při nasazení virtuálních počítačů s Windows v Azure | Microsoft Docs
description: Řešení potíží při nasazení virtuálních počítačů s Windows v modelu nasazení Azure Resource Manager.
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
ms.openlocfilehash: 98a16e0a60ddf149e8f0e1a092051f3e98ea8225
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627058"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Řešení potíží s nasazením virtuálního počítače s Windows v Azure

Pokud chcete řešit problémy s nasazením virtuálního počítače v Azure, přečtěte si [nejdůležitější problémy](#top-issues) s běžnými chybami a řešeními.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="top-issues"></a>Hlavní problémy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak můžu použít a nasadit image klienta Windows do Azure?

Pokud máte příslušné předplatné sady Visual Studio (dříve MSDN), můžete použít Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje a testování. Tento [článek](../windows/client-images.md) popisuje požadavky na způsobilost pro spuštění klienta Windows v Azure a používání imagí z Galerie Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak můžu nasadit virtuální počítač pomocí programu zvýhodněné hybridní využití (centrum)?

Existuje několik různých způsobů nasazení virtuálních počítačů s Windows pomocí zvýhodněné hybridní využití Azure.

Smlouva Enterprise předplatného:

• Nasaďte virtuální počítače z konkrétních imagí Marketplace, které jsou předem nakonfigurované pomocí zvýhodněné hybridní využití Azure.

Pro smlouvu Enterprise:

• Nahrajte vlastní virtuální počítač a nasaďte ho pomocí Správce prostředků šablony nebo Azure PowerShell.

Další informace naleznete v následujících zdrojích:

 - [Přehled programu zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nejčastější dotazy ke stažení](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Zvýhodněné hybridní využití Azure pro Windows Server a klienta Windows](../windows/hybrid-use-benefit-licensing.md)

 - [Jak můžu využít zvýhodněné hybridní využití v Azure](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Návody aktivovat svůj měsíční kredit pro Visual Studio Enterprise (BizSpark)

Další informace o aktivaci měsíčního kreditu najdete v tomto [článku](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Postup přidání Enterprise pro vývoj/testování do smlouva Enterprise (EA) pro získání přístupu k obrázkům klienta v systému Windows?

Možnost vytvářet předplatná na základě nabídky Enterprise pro vývoj/testování je omezená na vlastníky účtů, kterým k tomu udělil oprávnění správce podniku. Vlastník účtu vytvoří předplatné prostřednictvím portálu účtů Azure a pak by měl jako spolusprávce přidat aktivní předplatitele sady Visual Studio. Aby mohly spravovat a využívat prostředky potřebné pro vývoj a testování. Další informace najdete v tématu věnovaném [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Chybí moje ovladače pro virtuální počítač s Windows N-Series.

Pokyny k instalaci ovladačů pro virtuální počítače s Windows najdete [tady](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nemůžu najít instanci GPU v mém virtuálním počítači řady N-Series

Pokud chcete využívat možnosti GPU pro virtuální počítače řady Azure N-Series, musíte nainstalovat ovladače grafiky na každý virtuální počítač po nasazení. Informace o nastavení ovladače jsou k dispozici [zde](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Jsou virtuální počítače řady N-Series dostupné v mojí oblasti?

Dostupnost můžete ověřit z [tabulky dostupné podle oblastí](https://azure.microsoft.com/regions/services)a na ceny [zde](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jaké image klienta můžu používat a nasazovat v Azure a jak se dají získat?

Pokud máte příslušné předplatné sady Visual Studio (dříve MSDN), můžete použít Windows 7, Windows 8 nebo Windows 10 v Azure pro scénáře vývoje a testování. 

- Image Windows 10 jsou dostupné z Galerie Azure v rámci [způsobilých nabídek pro vývoj a testování](../windows/client-images.md). 
- Předplatitelé sady Visual Studio v jakémkoli typu nabídky můžou také [dostatečně připravit a vytvářet](../windows/prepare-for-upload-vhd-image.md) image s 64 Windows 7, Windows 8 nebo Windows 10 a pak [nahrávat do Azure](../windows/upload-generalized-managed.md). Použití zůstává omezené na vývoj a testování prostřednictvím aktivních předplatitelů sady Visual Studio.

Tento [článek](../windows/client-images.md) popisuje požadavky na způsobilost pro spuštění klienta Windows v Azure a používání imagí z Galerie Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nedá se mi zobrazit rodina velikostí virtuálních počítačů, kterou chci při změně velikosti virtuálního počítače.

Když je virtuální počítač spuštěný, je nasazený na fyzický server. Fyzické servery v oblastech Azure se seskupují v clusterech společného fyzického hardwaru. Změna velikosti virtuálního počítače, který vyžaduje přesun virtuálního počítače do různých hardwarových clusterů, se liší v závislosti na modelu nasazení, který se použil k nasazení virtuálního počítače.

- Virtuální počítače nasazené v modelu nasazení Classic je nutné odebrat a znovu nasadit nasazení cloudové služby, aby se virtuální počítače změnily na velikost v jiné rodině velikostí.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Virtuální počítače nasazené v modelu nasazení Správce prostředků před změnou velikosti libovolného virtuálního počítače ve skupině dostupnosti musíte zastavit všechny virtuální počítače ve skupině dostupnosti.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Uvedená velikost virtuálního počítače není při nasazení v sadě dostupnosti podporována.

Vyberte velikost podporovanou v clusteru skupiny dostupnosti. Doporučuje se při vytváření skupiny dostupnosti zvolit největší velikost virtuálního počítače, kterou si myslíte, že potřebujete, a to je vaše první nasazení do skupiny dostupnosti.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Můžu přidat existující klasický virtuální počítač do skupiny dostupnosti?

Ano. Existující klasický virtuální počítač můžete přidat do nové nebo existující skupiny dostupnosti. Další informace najdete v tématu [Přidání existujícího virtuálního počítače do skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Další kroky
Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/).

Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
