---
title: Skupina počítačů pro hodnocení se migrovat Azure | Microsoft Docs
description: Popisuje, jak seskupit počítačů před spuštěním posouzení se službou Azure migrovat.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ccab88c0195a7ca459c8579b7870d121dfd0fe1d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231402"
---
# <a name="group-machines-for-assessment"></a>Skupiny počítačů pro vyhodnocení

Tento článek popisuje, jak vytvořit skupiny počítačů pro vyhodnocení podle [Azure migrovat](migrate-overview.md). Azure migrací vyhodnocuje počítače ve skupině, zkontrolujte, jestli jste vhodné pro migraci na Azure a poskytuje velikost a cenově odhady pro spuštění na počítač v Azure.


## <a name="create-a-group"></a>Vytvořit skupinu

1. V **přehled** projektu Azure migrace, v části Správa, klikněte na tlačítko **skupiny** > **+ skupiny**a zadejte název skupiny.
2. Přidejte jeden nebo více počítačů do skupiny a klikněte na **vytvořit**. 
3. Volitelně můžete vybrat spustit nové vyhodnocení pro skupinu. 

    ![Vytvořit skupinu](./media/how-to-create-a-group/create-group.png)

Po vytvoření skupiny, můžete ho upravit tak, že vyberete skupinu na **skupiny** stránky a přidáním nebo odebráním počítače.

## <a name="next-steps"></a>Další postup

- Další informace o použití [mapování závislostí počítače](how-to-create-group-machine-dependencies.md) k vytvoření vysoce důvěryhodných skupin.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
