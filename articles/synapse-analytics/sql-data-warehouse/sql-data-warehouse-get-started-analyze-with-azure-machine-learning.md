---
title: Analýza dat pomocí Azure Machine Learning
description: Azure Machine Learning slouží k vytvoření prediktivního modelu strojového učení založeného na datech uložených v Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416171"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analýza dat pomocí Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Tento kurz používá Azure Machine Learning k vytvoření prediktivního modelu strojového učení založeného na datech uložených v Azure Synapse. Konkrétně tento postup sestaví cílenou marketingovou kampaň společnosti Adventure Works, prodejny kol, odhadnutím toho, jaká je pravděpodobnost, že si zákazník koupí kolo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* Fond SQL předem načtený ukázkovými daty AdventureWorksDW. Chcete-li to zřídit, [přečtěte si téma Vytvoření fondu SQL](create-data-warehouse-portal.md) a zvolte načtení ukázkových dat. Pokud už Data Warehouse máte, ale nemáte ukázková data, můžete [ukázková data načíst ručně](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Získejte data
Data jsou v zobrazení dbo.vTargetMail v databázi AdventureWorksDW. Postup načtení těchto dat:

1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/) a klikněte na Moje experimenty.
2. V levém dolním rohu obrazovky klepněte na tlačítko **+NOVÝ** a vyberte **možnost Prázdný experiment**.
3. Zadejte název svého experimentu: Cílený marketing.
4. Přetáhněte modul **Importovat data** v části **Vstup dat a výstup** z podokna modulů na plátno.
5. Zadejte podrobnosti o fondu SQL v podokně Vlastnosti.
6. Zadejte databázový **dotaz** pro načtení data, která vás zajímají.

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

Pod plátnem experimentu klikněte na **Run** (Spustit), aby se experiment spustil.

![Spusťte experiment.](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Po úspěšném dokončení experimentu klikněte v dolní části modulu Reader na výstupní port a výběrem možnosti **Visualize** (Vizualizovat) zobrazte naimportovaná data.

![Zobrazení naimportovaných dat](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Vyčistěte data
Pro vymazání dat odstraňte některé sloupce, které nejsou pro model důležité. Použijte následující postup:

1. Přetáhněte modul **Vybrat sloupce v datové sadě v** části Transformace dat < **Manipulace** na plátno. Připojte tento modul k modulu **Import dat.**
2. V podokně vlastností klikněte na **Launch column selector** (Spustit selektor sloupců) a určete sloupce, které se mají odstranit.

   ![Project Columns](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Vylučte dva sloupce: CustomerAlternateKey a GeographyKey.

   ![Odebrání nepotřebných sloupců](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Sestavte model
Data rozdělíme v poměru 80:20 – 80 % pro učení v rámci modelu strojového učení a 20 % pro otestování modelu. Budeme využívat "Two-Class" algoritmy pro tento problém binární klasifikace.

1. Přetáhněte na plátno modul **Split**.
2. V podokně vlastností zadejte 0,8 pro zlomek řádků v první výstupní datové sadě.

   ![Rozdělení dat na sadu učení a testovací sadu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Přetáhněte na plátno modul **Two-Class Boosted Decision Tree**.
4. Přetáhněte modul **Model vlaku** do plátna a určete vstupy připojením k modulům **Dvoutřídového posíleného rozhodovacího stromu** (algoritmus ML) a **Split** (data pro trénování algoritmu) . 

     ![Připojení modulu Train Model](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. V podokně vlastností pak klikněte na **Launch column selector** (Spustit selektor sloupců). Jako sloupec pro předpověď vyberte sloupec **BikeBuyer**.

   ![Vyberte sloupec pro předpověď](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Skóre model
Teď otestujeme, jaký je výkon modelu při použití testovacích dat. Porovnáme námi zvolený algoritmus s jiným algoritmem, abychom zjistili, který z nich vrací lepší výsledky.

1. Přetáhněte modul **Modelu skóre** na plátno a připojte jej k modulům **Train Model** a **Split Data.**

   ![Ohodnocení modelu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Na plátno experimentu přetáhněte **Two-Class Bayes Point Machine**. Porovnáme výsledky tohoto algoritmu s rozhodovacím stromem Two-Class Boosted Decision Tree.
3. Zkopírujte na plátno moduly Train Model a Score Model.
4. Přetáhněte modul **Evaluate Model** na plátno pro porovnání obou algoritmů.
5. **Spusťte** experiment.

   ![Spusťte experiment.](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Klikněte na výstupní port v dolní části modulu Evaluate Model a klikněte na Visualize (Vizualizovat).

   ![Vizualizace výsledků vyhodnocení](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

Poskytované metriky jsou křivka ROC, diagram přesnosti odvolání a křivka zdvihu. Když se na tyto metriky podíváme, vidíme, že první model má lepší výsledky než druhý. Chcete-li se podívat na to, co předpověděl první model, klikněte na výstupní port modelu skóre a klikněte na Vizualizovat.

![Vizualizace výsledků skóre](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Uvidíte, že se do testovací datové sady přidaly další dva sloupce.

* Scored Probabilities (Vyhodnocené pravděpodobnosti): Pravděpodobnost, že si zákazník koupí kolo.
* Scored Labels (Popisky vyhodnocení): Klasifikace prováděná modelem – kupující (1) nebo nekupující (0) kolo. Tato prahová hodnota pravděpodobnosti pro popisky je nastavena na 50 % a je možné ji upravit.

Při porovnání sloupce BikeBuyer (skutečnost) s popisky vyhodnocení (předpověď) můžete vidět, jaká byla úspěšnost modelu. Dále můžete tento model provést předpovědi pro nové zákazníky a publikovat tento model jako webovou službu nebo zapsat výsledky zpět do Azure Synapse.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření prediktivních modelů strojového učení najdete v tématu [Úvod do strojového učení na platformě Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).