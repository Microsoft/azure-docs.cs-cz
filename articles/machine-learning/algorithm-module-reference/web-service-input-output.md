---
title: Vstup a výstup webové služby
description: Další informace o modulech webových služeb v návrháři Azure Machine Learning (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462084"
---
# <a name="web-service-inputoutput"></a>Vstup a výstup webové služby

Tento článek popisuje vstupní modul **webové služby** a modul **Výstup webové služby** v návrháři Azure Machine Learning (preview).

Vstupní modul **webové služby** se může připojit pouze ke vstupnímu portu s typem **DataFrameDirectory**. A **webový servis Výstupní** modul lze připojit pouze z výstupního portu s typem **DataFrameDirectory**. Tyto dva moduly lze nalézt ve stromu modulu v kategorii **Webová služba.** 

Vstupní modul **webové služby** se používá k označení, kam uživatelská data zadávají kanál, a modul **Výstup webové služby** se používá k označení, kde jsou vrácena uživatelská data v kanálu odvození v reálném čase.

## <a name="how-to-use-web-service-inputoutput"></a>Použití vstupu a výstupu webové služby

- Když vytvoříte kanál odvození v reálném čase z trénovacího kanálu, bude automaticky přidán modul **Vstup webové služby** a **Výstup webové služby,** který zobrazí, kam uživatelská data zadá kanál a kde jsou vrácena data. 

    Přečtěte si další informace [o vytvoření kanálu odvození v reálném čase](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatické generování kanálu odvození v reálném čase je proces nejlepšího úsilí založený na pravidlech, neexistuje žádná záruka správnosti. Můžete ručně přidat nebo odebrat vstupní a výstupní moduly **webové služby,** abyste vyhověli vašim požadavkům. Ujistěte se, že v kanálu odvození v reálném čase je alespoň jeden vstupní modul **webové služby** a jeden výstupní modul **webové služby.** Pokud máte více modulů **pro vstup webové služby** nebo **výstup webové služby,** ujistěte se, že mají jedinečné názvy, které můžete zadat do pravého panelu modulu.

- Můžete také ručně vytvořit kanál odvození v reálném čase přidáním modulů **vstup webové služby** a **výstupní webové služby** do neodeslané kanálu.

    > [!NOTE]
    >  Typ kanálu bude určen při prvním odeslání. Proto nezapomeňte před prvním odesláním přidat modul **Vstup webové služby** a **Výstup webové služby,** pokud chcete vytvořit kanál odvození v reálném čase.

   Níže příklad ukazuje, jak ručně vytvořit kanál odvození v reálném čase z **modulu Spustit skript Pythonu.** 

   ![Příklad](media/module/web-service-input-output-example.png)
   
   Po odeslání kanálu a úspěšné dokončení spuštění budete moci nasadit koncový bod v reálném čase.
   
   > [!NOTE]
   >  Ve výše uvedeném příkladu **zadejte data ručně** poskytuje schéma dat pro vstup webové služby a je nezbytné pro nasazení koncového bodu v reálném čase. Obecně byste měli vždy připojit modul nebo datovou sadu k portu, který je připojen **input webové služby,** aby bylo zajištěno schéma dat.
   
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace [o nasazení koncového bodu v reálném čase](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.