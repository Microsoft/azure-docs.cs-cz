---
title: Latence připojení uživatele k virtuální ploše Windows – Azure
description: Latence připojení pro uživatele virtuálních klientů Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574590"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Určení latence připojení uživatele ve virtuální ploše Windows

Virtuální klient Windows je globálně dostupný. Správci můžou vytvářet virtuální počítače v libovolné oblasti Azure, které chtějí. Latence připojení se bude lišit v závislosti na umístění uživatelů a virtuálních počítačů. Služba Windows Virtual Desktop Service se průběžně zavede do nových geografických oblastí, aby se zlepšila latence.

[Nástroj Estimator Experience pro virtuální počítače s Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) vám pomůže určit nejlepší umístění pro optimalizaci latence vašich virtuálních počítačů. Doporučujeme, abyste tento nástroj používali každých dvou až tří měsíců, abyste se ujistili, že se optimální umístění nezměnilo, protože se virtuální plocha Windows vyřadí do nových oblastí.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interpretace výsledků z nástroje Estimator pro prostředí virtuálních počítačů s Windows

Na virtuálním počítači s Windows by latence až 150 MS neměla mít vliv na uživatelské prostředí, které nezahrnuje vykreslování ani video. Latence mezi 150 MS a 200 MS by měly být pro zpracování textu přesné. Latence nad 200 ms může ovlivnit činnost koncového uživatele. 

Připojení k virtuálním plochám Windows navíc závisí na připojení k Internetu počítače, ze kterého uživatel službu používá. Uživatelé můžou přijít o zpoždění při navázání nebo zaznamenání vstupu v jedné z následujících situací:

 - Uživatel nemá stabilní místní připojení k Internetu a latence je vyšší než 200 ms.
 - Síť je nastavena na sytost nebo s omezením na míru.

Doporučujeme vybrat umístění virtuálních počítačů, která jsou co nejblíže vašim uživatelům. Pokud se například uživatel nachází v Indii, ale virtuální počítač je ve USA, bude latence, která bude mít vliv na celkové uživatelské prostředí. 

## <a name="azure-front-door"></a>Azure Front Door

Virtuální počítač s Windows používá [přední dvířka Azure](https://azure.microsoft.com/services/frontdoor/) k přesměrování připojení uživatele k nejbližší bráně virtuálního počítače s Windows na základě zdrojové IP adresy. Virtuální plocha Windows bude vždycky používat bránu virtuální plochy Windows, kterou si klient zvolí.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit nejlepší umístění pro optimální latenci, přečtěte si [Nástroj Estimator pro Windows Virtual Desktop Experience](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Cenové tarify najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pokud chcete začít s nasazením virtuálního počítače s Windows, přečtěte si [náš kurz](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
