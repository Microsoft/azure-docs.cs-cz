---
title: Ukázkové kanály návrhářů
titleSuffix: Azure Machine Learning
description: Pomocí ukázek v návrháři Azure Machine Learning můžete přejít na kanály strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389337"
---
# <a name="designer-sample-pipelines"></a>Ukázkové kanály návrháře

Pomocí předdefinovaných příkladů v návrháři Azure Machine Learning můžete rychle začít vytvářet vlastní kanály strojového učení. Úložiště [GitHub](https://github.com/Azure/MachineLearningDesigner) návrháře Azure Machine Learning obsahuje podrobnou dokumentaci, která vám pomůže pochopit některé běžné scénáře strojového učení.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).
* Pracovní prostor Azure Machine Learning s podnikovou skladovou položkou.


## <a name="how-to-use-sample-pipelines"></a>Použití ukázkových kanálů

Návrhář uloží kopii ukázkových kanálů do pracovního prostoru studia. Můžete upravit kanál přizpůsobit svým potřebám a uložit jej jako svůj vlastní. Použijte je jako výchozí bod pro jumpstart své projekty.

### <a name="open-a-sample-pipeline"></a>Otevření ukázkového kanálu

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Vyberte **návrháře**.

1. V části Nový **kanál** vyberte ukázkový kanál.

    Vyberte **Zobrazit další ukázky** pro úplný seznam ukázek.

### <a name="submit-a-pipeline-run"></a>Odeslání spuštění kanálu

Chcete-li spustit kanál, musíte nejprve nastavit výchozí výpočetní cíl pro spuštění kanálu.

1. V podokně **Nastavení** napravo od plátna vyberte **Vybrat cíl výpočetního výkonu**.

1. V zobrazeném dialogovém okně vyberte existující výpočetní cíl nebo vytvořte nový. Vyberte **Uložit**.

1. Vyberte **Odeslat** v horní části plátna, abyste odeslali spuštění kanálu.

V závislosti na ukázkové kanálu a výpočetní nastavení spuštění může trvat nějakou dobu k dokončení. Výchozí nastavení výpočetních prostředků mají minimální velikost uzlu 0, což znamená, že návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky jsou již přiděleny. Kromě toho návrhář používá výsledky uložené v mezipaměti pro každý modul k dalšímu zlepšení efektivity.


### <a name="review-the-results"></a>Kontrola výsledků

Po dokončení spuštění kanálu můžete zkontrolovat kanál a zobrazit výstup pro každý modul další informace.

K zobrazení výstupů modulu použijte následující kroky:

1. Vyberte modul na plátně.

1. V podokně podrobností modulu vpravo od plátna vyberte **Výstupy + protokoly**. Vyberte ikonu ![vizualizovat ikonu](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) grafu a zobrazte výsledky každého modulu. 

Použijte ukázky jako výchozí body pro některé z nejběžnějších scénářů strojového učení.

## <a name="regression-samples"></a>Regresní vzorky

Další informace o předdefinovaných regresních ukázkách.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 1: Regrese - Predikce cen automobilů (základní)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Předvídejte ceny aut pomocí lineární regrese. |
| [Ukázka 2: Regrese - Predikce cen automobilů (Advanced)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Předvídejte ceny automobilů pomocí rozhodovacího lesa a posílených regresorů rozhodovacího stromu. Porovnejte modely najít nejlepší algoritmus.

## <a name="classification-samples"></a>Klasifikační vzorky

Přečtěte si další informace o předdefinovaných ukázkách klasifikace. Můžete se dozvědět více o ukázky bez dokumentace odkazy otevřením ukázky a zobrazení komentáře modulu místo.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 3: Binární klasifikace s výběrem funkce - predikce příjmů](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Předpovědět příjem jako vysoké nebo nízké, pomocí dvoutřídy posílený rozhodovací strom. Pomocí Pearsonovy korelace vyberte funkce.
| [Ukázka 4: Binární klasifikace s vlastním skriptem Pythonu - Predikce úvěrového rizika](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasifikovat úvěrové aplikace jako vysoké nebo nízké riziko. Pomocí modulu Execute Python Script můžete data vážit.
| [Ukázka 5: Binární klasifikace - Predikce vztahu se zákazníky](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Předpovědět konve zákazníků pomocí dvoutřídních posílené rozhodovací stromy. Pomocí SMOTE můžete vzorkovat neobjektivní data.
| [Ukázka 7: Klasifikace textu – Dataset Wikipedie SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasifikujte typy společností z článků wikipedie s vícetřídní logistickou regresí. |
| Ukázka 12: Vícetřídní klasifikace - rozpoznávání písmen | Vytvořte soubor binárnítřídění klasifikovat psané dopisy. |

## <a name="recommender-samples"></a>Doporučující vzorky

Přečtěte si další informace o předdefinovaných ukázkách doporučení. Můžete se dozvědět více o ukázky bez dokumentace odkazy otevřením ukázky a zobrazení komentáře modulu místo.

| Ukázkový název | Popis | 
| --- | --- |
| Ukázka 10: Doporučení - Film Hodnocení Tweets | Vytvořte video doporučující modul z filmových titulů a hodnocení. |

## <a name="utility-samples"></a>Utility vzorky

Přečtěte si další informace o ukázkách, které demonstrují nástroje a funkce strojového učení. Můžete se dozvědět více o ukázky bez dokumentace odkazy otevřením ukázky a zobrazení komentáře modulu místo.

| Ukázkový název | Popis | 
| --- | --- |
| [Ukázka 6: Použití vlastního skriptu R – predikce zpoždění letu](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Ukázka 8: Křížová validace pro binární klasifikaci - Predikce příjmu dospělých | Pomocí křížového ověření vytvořte binární třídění pro příjem dospělých.
| Vzorek 9: Důležitost funkce permutace | Použití důležitosti funkce permutace k výpočtu skóre důležitosti pro testovací datovou sadu. 
| Ukázka 11: Naladit parametry pro binární klasifikace - predikce příjmu dospělých | Pomocí hyperparametrů Tune Model vyhledejte optimální hyperparametry pro vytvoření binárního třídění. |

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Naučte se vytvářet a nasazovat modely strojového učení pomocí [kurzu: Předvídejte cenu automobilu s návrhářem](tutorial-designer-automobile-price-train-score.md)
