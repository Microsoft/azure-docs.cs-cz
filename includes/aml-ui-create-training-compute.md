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
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929654"
---
Kanál běží na výpočetním cíli, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru. Po vytvoření cíle výpočetní služby ho můžete znovu použít pro budoucí spuštění.

1. Kanál spustíte tak, že v horní části plátna vyberete **Spustit** .

1. Po zobrazení podokna **Nastavení** vyberte **vybrat cíl výpočtů**.

    Pokud už máte dostupný cíl výpočtů, můžete ho vybrat ke spuštění tohoto kanálu.

    > [!NOTE]
    > Návrhář může spouštět experimenty pouze na Azure Machine Learning výpočetním cíli. Ostatní výpočetní cíle se nezobrazí.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Uložit**.

    ![Nastavit cíl pro výpočet](./media/aml-ui-create-training-compute/set-compute.png)

1. Vyberte **Run** (Spustit).

1. V dialogovém okně **nastavit spuštění kanálu** vyberte **+ nový experiment** pro **experiment**.

    > [!NOTE]
    > Experimenty seskupují podobný kanál společně. Pokud kanál spouštíte několikrát, můžete vybrat stejný experiment pro po sobě jdoucí běhy.

    1. Zadejte popisný název pro **název experimentu**.

    1. Vyberte **Run** (Spustit).
    
    Stav spuštění a podrobnosti můžete zobrazit v pravém horním rohu plátna.

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně pět minut. Po vytvoření prostředku ho můžete znovu použít a tuto dobu čekání přeskočit pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na nulové uzly, pokud je nečinný úspora nákladů. Když ho znovu použijete po uplynutí určité prodlevy, může se při škálování na pozadí vyskytnout přibližně pět minut čekací doby.
