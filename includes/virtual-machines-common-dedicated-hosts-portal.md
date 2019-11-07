---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590574"
---
> [!IMPORTANT]
> Vyhrazené hostitele Azure jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Známá omezení verze Preview**
> - Sady škálování virtuálních počítačů se na vyhrazených hostitelích aktuálně nepodporují.
> - Počáteční verze Preview podporuje následující řadu virtuálních počítačů: DSv3 a ESv3. 


## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů

**Skupina hostitelů** je nový prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti jsou k dispozici další možnosti. U vyhrazených hostitelů můžete použít jednu z následujících možností: 
- Rozsah napříč několika zónami dostupnosti. V takovém případě je nutné mít skupinu hostitelů v každé z zón, které chcete použít.
- Rozložit napříč několika doménami selhání, které jsou namapované na fyzické racky. 
 
V obou případech je nutné zadat počet domén selhání pro skupinu hostitelů. Pokud nechcete rozsah domén selhání ve skupině, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít jak zóny dostupnosti, tak i domény selhání. 

V tomto příkladu vytvoříme skupinu hostitelů s použitím 1 zóny dostupnosti a 2 domén selhání. 


1. Otevřete Azure [Portal](https://portal.azure.com).
1. V levém horním rohu vyberte **vytvořit prostředek** .
1. Vyhledejte **skupinu hostitelů** a pak z výsledků vyberte **skupiny hostitelů (Preview)** .

    ![Výsledek hledání skupin hostitelů](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. Na stránce **skupiny hostitelů (Preview)** vyberte **vytvořit**.
1. Vyberte předplatné, které chcete použít, a pak vyberte **vytvořit novou** a vytvořte novou skupinu prostředků.
1. Jako **název** zadejte *myDedicatedHostsRG* a pak vyberte **OK**.
1. Jako **název skupiny hostitelů**zadejte *myHostGroup*.
1. V **oblasti umístění**vyberte **východní USA**.
1. V **oblasti dostupnost**vyberte **1**.
1. V případě **počtu domén selhání**vyberte **2**.
1. Vyberte **zkontrolovat + vytvořit** a potom počkejte na ověření.

    ![Nastavení skupiny hostitelů](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Jakmile se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit** a vytvořte skupinu hostitelů.

Vytvoření skupiny hostitelů by nemělo chvíli trvat.

## <a name="create-a-dedicated-host"></a>Vytvoření vyhrazeného hostitele

Teď ve skupině hostitelů vytvořte vyhrazeného hostitele. Kromě názvu pro hostitele je nutné zadat SKU pro hostitele. SKU hostitele zachytí podporovanou řadu virtuálních počítačů a také generování hardwaru pro vyhrazeného hostitele.  Během období Preview budeme podporovat následující hodnoty SKU hostitele: DSv3_Type1 a ESv3_Type1.

Další informace o SKU a cenách hostitelů najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete požádáni o zadání domény selhání pro hostitele.  

1. V levém horním rohu vyberte **vytvořit prostředek** .
1. Vyhledejte **vyhrazeného hostitele** a pak z výsledků vyberte **vyhrazené hostitele (Preview)** .

    ![Výsledek hledání skupin hostitelů](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. Na stránce **vyhrazené hostitele (Preview)** vyberte **vytvořit**.
1. Vyberte předplatné, které chcete použít.
1. Jako **skupinu prostředků**vyberte *myDedicatedHostsRG* .
1. V části **Podrobnosti o instanci**zadejte *myHost* pro **název** a vyberte *východní USA* pro umístění.
1. V **části hardwarový profil**vyberte *Standard Es3 Family – typ 1* pro **rodinu velikostí**vyberte *myHostGrup* pro **skupinu hostitelů** a pak pro **doménu selhání**vyberte *1* . Pro zbývající pole ponechte výchozí hodnoty.
1. Až budete hotovi, vyberte **zkontrolovat + vytvořit** a počkejte na ověření.

    ![Nastavení hostitele](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Jakmile se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit** a vytvořte hostitele.


