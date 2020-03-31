---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129504"
---
## <a name="limitations"></a>Omezení

- Škálovací sady virtuálních strojů nejsou aktuálně podporovány na vyhrazených hostitelích.
- Velikosti a typy hardwaru, které jsou k dispozici pro vyhrazené hostitele, se liší podle oblasti. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů

**Skupina hostitelů** je nový prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti, existují další možnosti. S vyhrazenými hostiteli můžete použít jednu nebo obě následující možnosti: 
- Rozsah mezi více zónami dostupnosti. V takovém případě musíte mít skupinu hostitelů v každé zóně, kterou chcete použít.
- Rozsah mezi více domén ách selhání, které jsou mapovány na fyzické regály. 
 
V obou případech je třeba zadat počet domén selhání pro vaši skupinu hostitelů. Pokud nechcete, aby se ve skupině promítají domény selhání, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít zóny dostupnosti i domény selhání. 

V tomto příkladu vytvoříme skupinu hostitelů pomocí 1 zóny dostupnosti a 2 domén selhání. 


1. Otevřete [portál](https://portal.azure.com)Azure .
1. V levém horním rohu **vyberte Vytvořit zdroj.**
1. Vyhledejte **skupinu hostitelů** a ve výsledcích vyberte **skupiny hostitelů.**
1. Na stránce **Skupiny hostitelů** vyberte **Vytvořit**.
1. Vyberte předplatné, které chcete použít, a pak vyberte **Vytvořit nový** a vytvořte novou skupinu prostředků.
1. Zadejte *myDedicatedHostsRG* jako **název** a pak vyberte **OK**.
1. Do **pole Název skupiny hostitelů**zadejte *příkaz myHostGroup*.
1. V **pouzdřite možnost Umístění** **vyberte možnost Východní USA**.
1. V **zóně dostupnosti**vyberte **1**.
1. V **případě počtu domén selhání**vyberte možnost **2**.
1. Vyberte **Zkontrolovat + vytvořit** a počkejte na ověření.
1. Jakmile se zobrazí zpráva **Ověření předáno,** vyberte **Vytvořit** a vytvořte skupinu hostitelů.

Vytvoření skupiny hostitelů by mělo trvat jen několik okamžiků.

## <a name="create-a-dedicated-host"></a>Vytvoření vyhrazeného hostitele

Nyní vytvořte vyhrazeného hostitele ve skupině hostitelů. Kromě názvu hostitele je nutné zadat skladovou položku pro hostitele. Skladová položka hostitele zachycuje podporovanou řadu virtuálních počítače a generaci hardwaru pro vašeho vyhrazeného hostitele.

Další informace o hostitelských skum a cenách najdete v [tématu Ceny vyhrazeného hostitele Azure](https://aka.ms/ADHPricing).

Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete vyzváni k zadání domény selhání pro hostitele.  

1. V levém horním rohu **vyberte Vytvořit zdroj.**
1. Vyhledejte **vyhrazeného hostitele** a pak z výsledků vyberte **vyhrazené hostitele.**
1. Na stránce **Vyhrazené hostitele** vyberte **Vytvořit**.
1. Vyberte předplatné, které chcete použít.
1. Jako **skupinu prostředků**vyberte *myDedicatedHostsRG* .
1. V **podrobnostech instance**zadejte *myHost* pro **název** a vyberte *východní USA* pro umístění.
1. V **profilu Hardware**vyberte standardní řadu *Es3 – typ 1* pro rodinu **Velikosti**, vyberte *myHostGroup* pro **skupinu Host** a pak vyberte *1* pro **doménu selhání**. Ponechte výchozí hodnoty pro zbytek polí.
1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit** a čekat na ověření.
1. Jakmile se zobrazí zpráva **Ověření předána,** vyberte **Vytvořit** k vytvoření hostitele.


