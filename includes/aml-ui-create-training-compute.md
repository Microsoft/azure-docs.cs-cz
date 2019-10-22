---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 28478f38df6ba6ea356626cc36c23ad498fc1f47
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692287"
---
Kanál běží na výpočetním cíli, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru.  Jakmile vytvoříte cílový výpočetní výkon, můžete ho znovu použít pro budoucí spuštění.

1. Kanál spustíte tak, že v horní části plátna vyberete **Spustit** .

1. Po zobrazení podokna **Nastavení** vyberte **vybrat cíl výpočtů**.

    Pokud už máte dostupný cíl výpočtů, můžete ho vybrat ke spuštění tohoto kanálu.

    > [!NOTE]
    > Vizuální rozhraní může spouštět experimenty jenom u Výpočetní prostředky služby Machine Learningch cílů. Další cíle výpočtů nebudou zobrazeny.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Save** (Uložit).

    ![Nastavit cíl pro výpočet](./media/aml-ui-create-training-compute/set-compute.png)

1. Vyberte **Run** (Spustit).

1. V dialogovém okně **nastavit spuštění kanálu** vyberte **+ nový experiment** pro **experiment** .

    * Zadejte popisný **název experimentu** .

    * Vyberte **Spustit** .
    
    Stav spuštění a podrobnosti můžete zobrazit v pravém horním rohu plátna.

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně 5 minut. Po vytvoření prostředku ho můžete znovu použít a tuto dobu čekání přeskočit pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na 0 uzlů, pokud je nečinný, aby se ušetřily náklady.  Když ho znovu použijete po uplynutí určité prodlevy, můžete znovu narazit přibližně 5 minut čekací doby, než se škáluje.
