---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66397296"
---
Experimentu se spouští na cílové výpočetní prostředí, výpočetní prostředek, který je připojený k pracovnímu prostoru.  Po vytvoření cílové výpočetní prostředí, můžete znovu použít pro budoucí spouštění.

1. Vyberte **spustit** v dolní části pro spuštění testu.

     ![Spusťte experiment](./media/aml-ui-create-training-compute/run-experiment.png)

1. Když **Compute cíle instalace** se zobrazí dialogové okno, pokud je váš pracovní prostor už výpočetní prostředek, můžete vybrat nyní.  V opačném případě vyberte **vytvořit nový**.

    > [!NOTE]
    > Vizuální rozhraní lze spustit pouze experimentů v Machine Learning Compute cíle. Další cílových výpočetních prostředí se nezobrazí.

1. Zadejte název pro výpočetní prostředek.

1. Vyberte **Run** (Spustit).

    ![Nastavení cílové výpočetní prostředí](./media/aml-ui-create-training-compute/set-compute.png)

    Výpočetní prostředek se nyní vytvoří. Zobrazení stavu v pravém horním rohu experimentu. 

    > [!NOTE]
    > Trvá přibližně 5 minut pro vytvoření výpočetních prostředků. Po vytvoření prostředku, můžete ji znovu použít a Přeskočit tuto dobu čekání pro budoucí spouštění.
    >
    > Výpočetní prostředek bude automaticky škálovat na 0 uzlů při nečinnosti snížení nákladů.  Při použití po určité prodlevě znovu, může znovu objevit přibližně 5 minut čekací doby při škálování zpět.
