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
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891642"
---
Experiment běží na výpočetním cíli, což je výpočetní prostředek, který je připojený k vašemu pracovnímu prostoru.  Jakmile vytvoříte cílový výpočetní výkon, můžete ho znovu použít pro budoucí spuštění.

1. Vyberte **Spustit** v dolní části a spusťte experiment.

1. Pokud se zobrazí dialogové okno **nastavit cíle výpočtů** , pokud váš pracovní prostor už má výpočetní prostředek, můžete ho vybrat hned teď.  V opačném případě vyberte **vytvořit novou**.

    > [!NOTE]
    > Vizuální rozhraní může spouštět experimenty jenom u Výpočetní prostředky služby Machine Learningch cílů. Další cíle výpočtů nebudou zobrazeny.

1. Zadejte název výpočetního prostředku.

1. Vyberte **Run** (Spustit).

    ![Nastavit cíl pro výpočet](./media/aml-ui-create-training-compute/set-compute.png)

    Prostředek COMPUTE se teď vytvoří. Zobrazte stav v pravém horním rohu experimentu. 

    > [!NOTE]
    > Vytvoření výpočetního prostředku trvá přibližně 5 minut. Po vytvoření prostředku ho můžete znovu použít a tuto dobu čekání přeskočit pro budoucí spuštění.
    >
    > Výpočetní prostředek se automaticky škáluje na 0 uzlů, pokud je nečinný, aby se ušetřily náklady.  Když ho znovu použijete po uplynutí určité prodlevy, můžete znovu narazit přibližně 5 minut čekací doby, než se škáluje.
