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
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493584"
---
Kanál se spouští na výpočetním cíli, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru. Jakmile vytvoříte cílový výpočetní výkon, můžete ho znovu použít pro budoucí spuštění.

1. Kanál spustíte tak, že v horní části plátna vyberete **Spustit** .

1. Po zobrazení podokna **Nastavení** vyberte **vybrat cíl výpočtů**.

    Pokud už máte dostupný cíl výpočtů, můžete ho vybrat ke spuštění tohoto kanálu.

    > [!NOTE]
    > Návrhář může spouštět experimenty na Výpočetní prostředky služby Machine Learningch cílech. Další cíle výpočtů nebudou zobrazeny.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Uložit**.

    ![Nastavit cíl pro výpočet](./media/aml-ui-create-training-compute/set-compute.png)

1. Vyberte **Run** (Spustit).

1. V dialogovém okně **nastavit spuštění kanálu** vyberte **+ nový experiment** pro **experiment** .

    > [!NOTE]
    > Experimenty seskupují podobný kanál společně. Pokud kanál spouštíte několikrát, můžete vybrat stejný experiment pro po sobě jdoucí běhy.

    * Zadejte popisný **název experimentu** .

    * Vyberte **Spustit** .
    
    Stav spuštění a podrobnosti můžete zobrazit v pravém horním rohu plátna.

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně 5 minut. Po vytvoření prostředku ho můžete znovu použít a tuto dobu čekání přeskočit pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na 0 uzlů, pokud je nečinný, aby se ušetřily náklady.  Když ho znovu použijete po uplynutí určité prodlevy, můžete znovu narazit přibližně 5 minut čekací doby, než se škáluje.
