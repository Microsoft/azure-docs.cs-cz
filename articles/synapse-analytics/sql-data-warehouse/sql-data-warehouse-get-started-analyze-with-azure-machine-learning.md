---
title: Analýza dat pomocí Azure Machine Learning
description: Pomocí Azure Machine Learning můžete vytvořit prediktivní model strojového učení založený na datech uložených v Azure synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678655"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analýza dat pomocí Azure Machine Learning

V tomto kurzu se používá [Azure Machine Learning Designer](../../machine-learning/concept-designer.md) k sestavení prediktivního modelu strojového učení. Model je založený na datech uložených v Azure synapse. Scénářem tohoto kurzu je předpovědět, jestli si zákazník může koupit kolo, nebo ne, aby to Adventure Works, prodejna kol mohl vytvořit cílovou marketingovou kampaň.

## <a name="prerequisites"></a>Požadavky

Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* fond SQL předem načtený pomocí ukázkových dat AdventureWorksDW. Pokud chcete zřídit tento fond SQL, přečtěte si téma [Vytvoření fondu SQL](create-data-warehouse-portal.md) a výběr načtení ukázkových dat. Pokud již máte datový sklad, ale nemáte ukázková data, můžete [ukázková data načíst ručně](./load-data-from-azure-blob-storage-using-copy.md).
* pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit nový, postupujte podle [tohoto kurzu](../../machine-learning/how-to-manage-workspace.md) .

## <a name="get-the-data"></a>Získání dat

Použitá data jsou v zobrazení dbo. vTargetMail v AdventureWorksDW. Pokud chcete v tomto kurzu použít úložiště dat, nejdřív se data exportují do Azure Data Lake Storage účtu, protože Azure synapse v současné době nepodporuje datové sady. Azure Data Factory lze použít k exportu dat z datového skladu do Azure Data Lake Storage pomocí [aktivity kopírování](../../data-factory/copy-activity-overview.md). Pro import použijte následující dotaz:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Jakmile jsou data v Azure Data Lake Storage k dispozici, úložiště dat v Azure Machine Learning se použijí pro [připojení ke službám úložiště Azure](../../machine-learning/how-to-access-data.md). Pomocí následujících kroků vytvořte úložiště dat a odpovídající datovou sadu:

1. Spusťte Azure Machine Learning Studio buď z Azure Portal nebo se přihlaste v [Azure Machine Learning Studiu](https://ml.azure.com/).

1. V levém podokně v části **Správa** klikněte na **úložiště dat** a pak klikněte na **nové úložiště dat**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Snímek obrazovky s levým podoknem Azure Machine Learning rozhraní":::

1. Zadejte název úložiště dat, vyberte typ jako Azure Blob Storage, zadejte umístění a přihlašovací údaje. Potom klikněte na **Vytvořit**.

1. Potom v levém podokně v části **assety** klikněte na **datové sady** . Vyberte **vytvořit datovou sadu** s možností **z úložiště dat**.

1. Zadejte název datové sady a vyberte typ, který bude **tabelární**. Potom kliknutím na tlačítko **Další** přesunete vpřed.

1. V **části vybrat nebo vytvořit úložiště dat** vyberte možnost **dříve vytvořené úložiště dat**. Vyberte úložiště dat, které bylo dříve vytvořeno. Klikněte na tlačítko Další a zadejte cestu a nastavení souboru. Nezapomeňte zadat záhlaví sloupce, pokud soubory obsahují.

1. Nakonec kliknutím na **vytvořit** Vytvořte datovou sadu.

## <a name="configure-designer-experiment"></a>Konfigurovat experiment návrháře

Dále postupujte podle následujících kroků pro konfiguraci návrháře:

1. V levém podokně v části **Author** klikněte na kartu **Návrhář** .

1. Vyberte **snadno sestavené moduly** pro vytvoření nového kanálu.

1. V podokně nastavení na pravé straně zadejte název kanálu.

1. Také vyberte cílový cluster COMPUTE pro celý experiment v nastavení tlačítko pro dříve zřízený cluster. Zavřete podokno nastavení.

## <a name="import-the-data"></a>Import dat

1. V levém podokně pod vyhledávacím polem vyberte subtab **sady dat** .

1. Přetáhněte datovou sadu, kterou jste vytvořili dříve, do plátna.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Snímek obrazovky modulu DataSet na plátně":::

## <a name="clean-the-data"></a>Vymazání dat

Chcete-li data vyčistit, vyřaďte sloupce, které pro model nejsou relevantní. Postupujte následovně:

1. V levém podokně vyberte **moduly** subtab.

1. Přetáhněte modul **Výběr sloupců v datové sadě** pod **transformaci dat < manipulaci** s plátnem. Připojte tento modul k modulu **DataSet** .

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Snímek obrazovky modulu výběru sloupců na plátně" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Kliknutím na modul otevřete podokno Vlastnosti. Klikněte na tlačítko Upravit sloupec a určete, které sloupce chcete vyřadit.

1. Vylučte dva sloupce: CustomerAlternateKey a GeographyKey. Klikněte na **Uložit**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Snímek obrazovky znázorňující sloupce, které jsou vyřazeny":::

## <a name="build-the-model"></a>Vytvoření modelu

Data jsou rozdělená 80-20:80%, aby bylo možné vytvořit model strojového učení a 20% pro testování modelu. V tomto problému binární klasifikace se používají algoritmy se dvěma třídami.

1. Přetáhněte na plátno modul **rozdělit data** .

1. V podokně Vlastnosti zadejte 0,8 pro **zlomek řádků v první výstupní sadě dat**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Snímek obrazovky znázorňující poměr rozdělení 0,8.":::

1. Přetáhněte na plátno modul **Two-Class Boosted Decision Tree**.

1. Přetáhněte modul **vlakového modelu** do plátna. Určete vstupy tím, že je propojíte s posíleným **rozhodovacím stromem se dvěma třídami** (algoritmus ml) a **rozdělíte data** (data pro výuku algoritmu v modulech).

1. V části model modelu výuky v možnosti **sloupec popisku** v podokně Vlastnosti vyberte upravit sloupec. Vyberte sloupec **BikeBuyer** jako sloupec, který chcete předpovědět, a vyberte **Uložit**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Snímek obrazovky zobrazující sloupec popisku, BikeBuyer, vybraný.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Snímek obrazovky s modulem výukového modelu připojeným k Two-Class zvýšili a rozdělili moduly pro rozhodovací strom a rozdělení dat":::

## <a name="score-the-model"></a>Určení skóre modelu

Nyní testujte, jak model provádí na testovacích datech. Budou porovnány dva různé algoritmy, abyste viděli, která z nich je lepší. Postupujte následovně:

1. Přetáhněte na plátno modul **bodového modelu** a připojte ho ke **výukovým modelům** a **rozděleným datovým** modulům.

1. Přetáhněte **Bayes průměrnou Perceptron** na plátno experimentu. Porovnáte se s tím, jak tento algoritmus provádí v porovnání s Two-Class zesíleným rozhodovacím stromu.

1. Zkopírujte a vložte do plátna model **výuky** a model **skóre** .

1. Přetáhněte modul **Evaluate Model** na plátno pro porovnání obou algoritmů.

1. Klikněte na **Odeslat** a nastavte spuštění kanálu.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Snímek obrazovky se všemi zbývajícími moduly na plátně" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Po dokončení spuštění klikněte pravým tlačítkem na modul **vyhodnotit model** a klikněte na **vizualizovat výsledky vyhodnocení**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Snímek obrazovky s výsledky.":::

K dispozici jsou tyto metriky: křivka ROC, diagram přesnosti odvolání a křivka zvednutí. Podívejte se na tyto metriky a podívejte se, že první model je lepší než druhý. Pokud se chcete podívat, co je to první model, klikněte pravým tlačítkem na modul určení skóre modelu a kliknutím na vizualizovat výslednou datovou sadu Zobrazte předpovězené výsledky.

Zobrazí se další dva sloupce, které jsou přidány do datové sady testů.

* Scored Probabilities (Vyhodnocené pravděpodobnosti): Pravděpodobnost, že si zákazník koupí kolo.
* Scored Labels (Popisky vyhodnocení): Klasifikace prováděná modelem – kupující (1) nebo nekupující (0) kolo. Tato prahová hodnota pravděpodobnosti pro popisky je nastavena na 50 % a je možné ji upravit.

Porovnejte sloupec BikeBuyer (aktuální) se štítky s skóre (předpovědi), abyste viděli, jak dobře byl model proveden. Dále můžete pomocí tohoto modelu vytvořit předpovědi pro nové zákazníky. [Tento model můžete publikovat jako webovou službu](../../machine-learning/tutorial-designer-automobile-price-deploy.md) nebo výsledky zapsat zpátky do Azure synapse.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Machine Learning najdete [v tématu Úvod do Machine Learning v Azure](../../machine-learning/overview-what-is-azure-ml.md).

Seznamte se s předdefinovaným bodování v datovém skladu [tady](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).