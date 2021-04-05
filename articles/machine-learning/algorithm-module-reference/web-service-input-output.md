---
title: 'Vstup/výstup webové služby: odkaz na modul'
description: Naučte se používat moduly webové služby v Návrháři Azure Machine Learning ke správě vstupů a výstupů.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421120"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Vstupní moduly webové služby a výstupní moduly webové služby

Tento článek popisuje vstupní moduly webové služby a výstupní moduly webové služby v Návrháři Azure Machine Learning.

Vstupní modul webové služby se může připojit pouze ke vstupnímu portu s typem **DataFrameDirectory**. Výstupní modul webové služby lze připojit pouze z výstupního portu s typem **DataFrameDirectory**. Tyto dva moduly můžete najít ve stromu modulu, a to v kategorii **webové služby** . 

Vstupní modul webové služby indikuje, kde data uživatele vstoupí do kanálu. Výstupní modul webové služby označuje, kde se vrátí uživatelská data v kanálu odvození v reálném čase.

## <a name="how-to-use-web-service-input-and-output"></a>Použití vstupu a výstupu webové služby

Při [vytváření kanálu odvození v reálném čase](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) ze školicího kanálu budou automaticky přidány výstupní moduly webové služby a webové služby, které ukazují, kde data uživatele vstoupí do kanálu a kde se vrátí data. 

> [!NOTE]
> Automatické generování kanálu odvození v reálném čase je proces, který je založený na pravidle. Není zaručena správná schopnost. 

Můžete ručně přidat nebo odebrat výstupní moduly webové služby a webové služby, aby vyhovovaly vašim požadavkům. Ujistěte se, že kanál pro odvození v reálném čase má alespoň jeden vstupní modul webové služby a jeden výstupní modul webové služby. Pokud máte více vstupních modulů webové služby nebo výstupní moduly webové služby, ujistěte se, že mají jedinečné názvy. Název můžete zadat do pravého panelu modulu.

Kanál pro odvození v reálném čase můžete také vytvořit tak, že přidáte vstupní moduly webové služby a výstupní moduly webové služby do neodeslaného kanálu.

> [!NOTE]
> Typ kanálu se určí při prvním odeslání. Před prvním odesláním nezapomeňte přidat výstupní moduly webové služby a výstupu webové služby.

Následující příklad ukazuje, jak ručně vytvořit kanál odvození v reálném čase z modulu spuštění skriptu Pythonu. 

![Příklad](media/module/web-service-input-output-example.png)
   
Po úspěšném odeslání kanálu a dokončení běhu můžete koncový bod nasadit v reálném čase.
   
> [!NOTE]
>  V předchozím příkladu **Zadejte data ručně** do schématu dat pro vstup webové služby a je nezbytné pro nasazení koncového bodu v reálném čase. Obecně byste měli vždy připojit modul nebo datovou sadu k portu, kde je **vstup webové služby** připojen k poskytnutí schématu dat.
   
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nasazení koncového bodu v reálném čase](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.