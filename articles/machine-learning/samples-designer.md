---
title: Příklady kanálů návrháře
titleSuffix: Azure Machine Learning
description: Použití ukázek v Návrháři Azure Machine Learning k přechodu na začátek kanálů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80389337"
---
# <a name="designer-sample-pipelines"></a>Ukázkové kanály designeru

Pomocí vestavěných příkladů v Návrháři Azure Machine Learning můžete rychle začít vytvářet vlastní kanály strojového učení. [Úložiště GitHub](https://github.com/Azure/MachineLearningDesigner) návrháře Azure Machine Learning obsahuje podrobnou dokumentaci, která vám pomůže pochopit některé běžné scénáře strojového učení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).
* Pracovní prostor Azure Machine Learning s SKU Enterprise.


## <a name="how-to-use-sample-pipelines"></a>Používání ukázkových kanálů

Návrhář uloží kopii ukázkových kanálů do pracovního prostoru studia. Kanál můžete upravit, abyste ho přizpůsobili vašim potřebám a ušetřili si ho jako vlastní. Použijte je jako výchozí bod pro rychlé zprovozněníí vašich projektů.

### <a name="open-a-sample-pipeline"></a>Otevření ukázkového kanálu

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **Návrhář**.

1. V části **Nový kanál** vyberte vzorový kanál.

    Pro úplný seznam ukázek vyberte **Zobrazit další ukázky** .

### <a name="submit-a-pipeline-run"></a>Odeslání spuštění kanálu

Pokud chcete spustit kanál, musíte nejdřív nastavit výchozí výpočetní cíl pro spuštění kanálu.

1. V podokně **Nastavení** napravo od plátna vyberte **vybrat cíl výpočtů**.

1. V dialogovém okně, které se zobrazí, vyberte existující cíl služby COMPUTE nebo vytvořte nový. Vyberte **Uložit**.

1. Kliknutím na **Odeslat** v horní části plátna odešlete spuštění kanálu.

V závislosti na ukázkových kanálech a výpočetních nastaveních může dokončení spuštění trvat delší dobu. Výchozí nastavení COMPUTE mají minimální velikost uzlu 0, což znamená, že Návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky už jsou přidělené. Kromě toho Návrhář používá výsledky v mezipaměti pro každý modul k dalšímu zvýšení efektivity.


### <a name="review-the-results"></a>Kontrola výsledků

Až se kanál dokončí, můžete si projít kanál a zobrazit výstup pro každý modul a získat další informace.

Výstupy modulu můžete zobrazit pomocí následujících kroků:

1. Vyberte modul na plátně.

1. V podokně podrobností modulu napravo od plátna vyberte **výstupy + protokoly**. Pokud chcete zobrazit výsledky ![každého modulu,](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) vyberte ikonu vizualizace ikony grafu. 

Použijte ukázky jako výchozí body pro některé z nejběžnějších scénářů strojového učení.

## <a name="regression-samples"></a>Ukázky regrese

Přečtěte si další informace o vestavěných regresních ukázkách.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 1: regrese-automobilová předpověď ceny (základní)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Předpovědět ceny aut pomocí lineární regrese. |
| [Ukázka 2: regrese – předpověď ceny automobilu (rozšířené)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Předpovědět ceny aut pomocí rozhodovací doménové struktury a dalších zesílených rozhodovacích stromů. Porovnejte modely a Najděte nejlepší algoritmus.

## <a name="classification-samples"></a>Ukázky klasifikace

Přečtěte si další informace o předdefinovaných ukázkách klasifikace. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 3: binární klasifikace s výběrem funkce – předpověď příjmů](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Předpovídání výnosů jako vysoké nebo nízké, pomocí rozstředního rozhodovacího stromu se dvěma třídami K výběru funkcí použijte korelaci Pearsonova.
| [Ukázka 4: binární klasifikace pomocí vlastního skriptu Pythonu – předpověď úvěrového rizika](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasifikujte úvěrové aplikace jako vysoké nebo nízké riziko. K vážení dat použijte modul spouštění skriptu Pythonu.
| [Ukázka 5: klasifikace binárních hodnot – předpověď vztahů se zákazníky](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Předpověď změn zákazníků pomocí dvou vyrovnaných rozhodovacích stromů se dvěma třídami Pomocí SMOTE můžete vzorkovat data s posunutím.
| [Ukázka 7: klasifikace textu – Wikipedii SP 500 – datová sada](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasifikujte typy společností z článků Wikipedii s využitím více tříd logistické regrese. |
| Ukázka 12: více tříd – rozpoznávání písmen | Vytvořte soubor se souborem sady binárních klasifikátorů pro klasifikaci psaných písmen. |

## <a name="recommender-samples"></a>Ukázky doporučení

Přečtěte si další informace o předdefinovaných doporučených ukázkách. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| Ukázka 10: doporučení – hodnocení filmu tweety | Sestavte modul doporučený pro film z nadpisů a hodnocení filmů. |

## <a name="utility-samples"></a>Ukázky nástrojů

Přečtěte si další informace o ukázkách, které předvádějí nástroje a funkce machine learningu. Další informace o ukázkách se dozvíte bez odkazů na dokumentaci, a to tak, že otevřete ukázky a místo toho zobrazíte komentáře modulu.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 6: použití vlastního skriptu R – předpověď zpoždění letů](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Ukázka 8: křížové ověřování pro předpověď v případě binární klasifikace – pro příjem | Pro vytvoření binárního klasifikátoru pro příjem z dospělého použijte vzájemné ověřování.
| Ukázka 9: důležitost funkce permutace | Použijte funkci permutace důležitost k výpočtu skóre důležitosti pro testovací datovou sadu. 
| Ukázka 11: ladění parametrů pro binární klasifikaci – předpověď příjmů za dospělé | K nalezení optimálních parametrů pro sestavení binárního klasifikátoru použijte parametry ladění modelu. |

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Naučte se vytvářet a nasazovat modely strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)
