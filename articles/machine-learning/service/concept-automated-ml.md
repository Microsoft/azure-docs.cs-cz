---
title: Automatizované výběr algoritmů ML a laděním
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak služba Azure Machine Learning můžete automaticky vyberte algoritmus pro vás a generovat model z něj šetřit čas pomocí parametrů a kritéria, které poskytnete vybrat nejlepší algoritmus pro model.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 09d4ef0b6a3455728ddab0484db4c701c6764214
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270922"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované machine learning je proces trvá trénovacích dat pomocí funkce definovanému cíli a provede iterace přes kombinace algoritmy a výběry funkcí automaticky vybrat nejlepší model pro data podle skóre školení. Tradiční strojového učení proces vývoje modelu je velmi náročná a vyžaduje investice významné domény znalostní báze a čas ke spuštění a porovnávat výsledky desítky modely. Automatizované strojového učení zjednodušuje tento proces vygenerováním modelů, která je vyladěná z cíle a omezení, které jste definovali pro experiment, jako je čas potřebný pro experiment ke spuštění nebo které modelů blokovaných.

## <a name="how-it-works"></a>Jak to funguje

1. Nakonfigurujete typ problému machine learning, který se snažíte vyřešit. Kategorie učení se podporují:
   + Klasifikace
   + Regrese
   + Prognózování

   Při automatizované machine learning je obecně dostupná, **prognóz funkce je stále ve verzi public preview.**

   Zobrazit [seznamu modelů](how-to-configure-auto-train.md#select-your-experiment-type) Azure Machine Learning můžete zkusit při cvičení.

1. Můžete zadat zdroj a formát pro trénovací data. Data musí být označeny a mohou být uloženy ve svém vývojovém prostředí nebo ve službě Azure Blob Storage. Pokud jsou data uložená ve svém vývojovém prostředí, musí být ve stejném adresáři jako trénovací skripty. Tento adresář je zkopírován do cílové výpočetní prostředí, které vyberete pro vzdělávání.

    Ve skriptu školení lze data načíst do Numpy pole nebo Pandas dataframe.

    Můžete provést konfiguraci možností rozdělení pro výběr školení a ověření dat, nebo můžete zadat samostatné školení a ověřování datových sad.

1. Konfigurace [cílové výpočetní prostředí](how-to-set-up-training-targets.md) , který se používá pro trénování modelu.

1. Nakonfigurujte konfiguraci automatizované machine learning. Tato volba určuje parametry používané jako Azure Machine Learning Iteruje přes různé modely hyperparameter nastavení, a které metriky se podívat na při určení nejvhodnějšího modelu 

1. Odešlete školení spustit.

Při školení, vytvoří službu Azure Machine Learning počet kanálů, které se pokoušejí různé algoritmy a parametry. Zastaví se po volání počtu iterací, které poskytnete, nebo když dosáhne cílové hodnoty pro metriku, kterou zadáte.

[ ![Automatizované Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Můžete si prohlédnout protokolu spuštění informace, které obsahuje metriky shromážděné za běhu. Spuštění trénovací také vytvoří objekt serializován Python (`.pkl` souboru), který obsahuje model a předzpracování dat.

## <a name="model-explainability"></a>Model explainability

Běžné trávení automatizované machine Learning je nemožnost zobrazíte-celým procesem. Azure Machine Learning umožňuje zobrazit podrobné informace o modelech pro zvýšení transparentnosti do co běží na back endu. Výstup ukazuje celkový funkce význam při ladění modelu řazení výsledků podle funkce vliv na váš model na maximum. Pro klasifikaci problémy můžete navíc zobrazit význam funkce třídy a hodnocení.

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o vytváření modelů pomocí automatizované Machine Learning:

+ [Kurz: Automaticky vyškolíme model klasifikace pomocí automatizované Azure Machine Learning](tutorial-auto-train-models.md)

+ [Konfigurace nastavení pro automatické školení](how-to-configure-auto-train.md)

+ [Použít automatické školení na vzdálený prostředek](how-to-auto-train-remote.md) 
