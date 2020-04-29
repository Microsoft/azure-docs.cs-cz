---
title: Vstup/výstup webové služby
description: Přečtěte si o modulech webové služby v Návrháři Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462084"
---
# <a name="web-service-inputoutput"></a>Vstup/výstup webové služby

Tento článek popisuje **Vstupní modul webové služby** a **výstupní modul webové služby** v Návrháři Azure Machine Learning (Preview).

**Vstupní modul webové služby** se může připojit pouze ke vstupnímu portu typu **DataFrameDirectory**. A **výstupní modul webové služby** lze připojit pouze z výstupního portu s typem **DataFrameDirectory**. Dva moduly najdete ve stromu modulu v části kategorie **webové služby** . 

**Vstupní modul webové služby** se používá k označení, kde data o uživateli vstupují do kanálu a **výstupní modul webové služby** se používá k označení, kde se vrátí uživatelská data v kanálu odvození v reálném čase.

## <a name="how-to-use-web-service-inputoutput"></a>Použití vstupu/výstupu webové služby

- Když vytvoříte kanál pro odvození v reálném čase ze školicího kanálu, automaticky se přidá **Vstupní modul webové služby** a **výstupní modul webové služby** , který zobrazí, kam data do kanálu vstoupí uživatel a kde se vrátí data. 

    Přečtěte si další informace o [vytvoření kanálu odvození v reálném čase](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatické generování kanálu odvození v reálném čase je osvědčeným procesem, který je založený na pravidlech, ale není zaručená správná úroveň. Můžete ručně přidat nebo odebrat **vstupní/výstupní moduly webové služby** , aby vyhovovaly vašim požadavkům. Ujistěte se, že existuje alespoň jeden modul **vstupu webové služby** a jeden **výstupní modul webové služby** v kanálu odvození v reálném čase. Pokud máte více **vstupních modulů webové služby** nebo **výstupní moduly webové služby** , ujistěte se, že mají jedinečné názvy, které můžete zadat v pravém panelu modulu.

- Kanál pro odvození v reálném čase můžete také vytvořit tak, že přidáte **vstupní moduly webové služby** a **výstupní moduly webové služby** do neodeslaného kanálu.

    > [!NOTE]
    >  Typ kanálu se určí při prvním odeslání. Proto nezapomeňte přidat modul výstupu **webové služby** a **výstupní modul webové služby** před odesláním poprvé, pokud chcete vytvořit kanál odvození v reálném čase.

   Následující příklad ukazuje, jak ručně vytvořit kanál odvození v reálném čase z **spuštění modulu skriptu Pythonu** . 

   ![Příklad](media/module/web-service-input-output-example.png)
   
   Po odeslání kanálu a spuštění se úspěšně dokončí, budete moct nasadit koncový bod v reálném čase.
   
   > [!NOTE]
   >  Ve výše uvedeném příkladu **Zadejte data ručně** . poskytuje schéma dat pro vstup webové služby a je nezbytné pro nasazení koncového bodu v reálném čase. Obecně byste měli vždy připojit modul nebo datovou sadu k portu, ke kterému je **vstup webové služby** připojen, a poskytnout tak schéma dat.
   
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nasazení koncového bodu v reálném čase](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.