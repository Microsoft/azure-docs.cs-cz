---
title: Žádosti o zvýšení kvóty virtuálních procesorů Azure Resource Manageru | Dokumentace Microsoftu
description: Žádostech o navýšení kvóty virtuálních procesorů Azure Resource Manageru
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076812"
---
# <a name="quota-increase-requests"></a>Žádosti o navýšení kvóty

Kvóty virtuálních procesorů Resource Manageru pro virtuální počítače a škálovací sady virtuálních počítačů se vynucují na dvou úrovních pro každé předplatné, v jednotlivých oblastech. 

První úroveň je celkový počet místních virtuálních procesorů limit (v všechny řady virtuálních počítačů) a druhé vrstvy je za limitu virtuálních počítačů řady virtuálních procesorů (jako je například virtuální procesory řady D-series). Pokaždé, když je nový virtuální počítač nasadit, součet nové i stávající virtuální procesory využití pro tento virtuální počítač řady nesmí překročit kvóty virtuálních procesorů schválené pro konkrétní řady virtuálních počítačů. Dále celkový počet virtuálních procesorů nové i stávající počet nasazených na všechny řady virtuálních počítačů může být maximálně celkový počet překročení kvóty virtuálních procesorů schválení předplatného. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné.
Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro virtuální počítače řady z webu Azure portal. Zvýšení kvóty řadu virtuálních počítačů automaticky zvětší, celkový počet místních virtuálních procesorů limit stejný jmenovce. 

Když se vytvoří nový odběr, nemusí být celkový počet místních virtuálních procesorů výchozí shoduje se součtem výchozí kvóty virtuálních procesorů pro všechny jednotlivé řady virtuálních počítačů. Výsledkem může být předplatné s dostatečnou kvótu pro každé jednotlivé řady virtuálních počítačů, které chcete nasadit, ale ne dostatečnou kvótu pro celkový počet místních virtuálních procesorů pro všechna nasazení. V takovém případě bude muset odeslat žádost o zvýšení limitu celkový počet místních virtuálních procesorů explicitně. Celkový počet místních virtuálních procesorů limit nesmí překročit součet schválené kvóty přes všechny řady virtuálních počítačů pro oblast.

Další informace o kvótách na [stránce kvóty virtuálních procesorů virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [předplatného Azure a omezení služeb](https://aka.ms/quotalimits) stránky. 

