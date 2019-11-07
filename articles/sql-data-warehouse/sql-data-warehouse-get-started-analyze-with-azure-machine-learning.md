---
title: Analýza dat pomocí Azure Machine Learning
description: Pomocí Azure Machine Learning sestavte prediktivní model Machine Learning založený na datech uložených v datovém skladu SQL Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f91960eaac92047e76275e63b1feaf471de3bac3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692803"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analýza dat pomocí Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Tento kurz používá Azure Machine Learning k sestavení prediktivního modelu Machine Learning založeného na datech uložených v datovém skladu SQL Azure. Konkrétně tento postup sestaví cílenou marketingovou kampaň společnosti Adventure Works, prodejny kol, odhadnutím toho, jaká je pravděpodobnost, že si zákazník koupí kolo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* SQL Data Warehouse s předem načtenými vzorovými daty AdventureWorksDW. Ke zřízení této konfigurace použijte článek [Vytvoření SQL Data Warehouse][Create a SQL Data Warehouse] a zvolte načtení ukázkových dat. Pokud už Data Warehouse máte, ale nemáte ukázková data, můžete [ukázková data načíst ručně][load sample data manually].

## <a name="1-get-the-data"></a>1. získání dat
Data jsou v zobrazení dbo.vTargetMail v databázi AdventureWorksDW. Postup načtení těchto dat:

1. Přihlaste se k [Azure Machine Learning Studio][Azure Machine Learning studio] a klikněte na Moje experimenty.
2. V levé dolní části obrazovky klikněte na **+ Nový** a vyberte **prázdný experiment**.
3. Zadejte název svého experimentu: Cílený marketing.
4. Přetáhněte modul **Import dat** do pole **vstup a výstup dat** z podokna moduly na plátno.
5. V podokně vlastností zadejte podrobné informace o vaší databázi SQL Data Warehouse.
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
![Spusťte experiment.][1]

Po úspěšném dokončení experimentu klikněte v dolní části modulu Reader na výstupní port a výběrem možnosti **Visualize** (Vizualizovat) zobrazte naimportovaná data.
![Zobrazení naimportovaných dat][3]

## <a name="2-clean-the-data"></a>2. Vyčistěte data.
Pro vymazání dat odstraňte některé sloupce, které nejsou pro model důležité. Použijte následující postup:

1. Přetáhněte modul **Výběr sloupců v datové sadě** pod **transformaci dat < manipulaci** s plátnem. Připojte tento modul k modulu **Import dat** .
2. V podokně vlastností klikněte na **Launch column selector** (Spustit selektor sloupců) a určete sloupce, které se mají odstranit.
   ![Project Columns][4]
3. Vylučte dva sloupce: CustomerAlternateKey a GeographyKey.
   ![Odebrání nepotřebných sloupců][5]

## <a name="3-build-the-model"></a>3. Sestavte model
Data rozdělíme v poměru 80:20 – 80 % pro učení v rámci modelu strojového učení a 20 % pro otestování modelu. Pro tento problém binární klasifikace budeme používat algoritmy Two-Class.

1. Přetáhněte na plátno modul **Split**.
2. V podokně Vlastnosti zadejte 0,8 pro zlomek řádků v první výstupní sadě dat.
   ![Rozdělení dat na sadu učení a testovací sadu][6]
3. Přetáhněte na plátno modul **Two-Class Boosted Decision Tree**.
4. Přetáhněte modul **vlakového modelu** na plátno a určete vstupy tím, že je propojíte s posíleným **rozhodovacím Stromum se dvěma třídami** (ml Algorithm) a **rozdělíte** (data pro výuku algoritmu v) moduly. 
     ![Připojení modulu Train Model][7]
5. V podokně vlastností pak klikněte na **Launch column selector** (Spustit selektor sloupců). Jako sloupec pro předpověď vyberte sloupec **BikeBuyer**.
   ![Vyberte sloupec pro předpověď][8]

## <a name="4-score-the-model"></a>4. určení skóre modelu
Teď otestujeme, jaký je výkon modelu při použití testovacích dat. Porovnáme námi zvolený algoritmus s jiným algoritmem, abychom zjistili, který z nich vrací lepší výsledky.

1. Přetáhněte na plátno modul **bodového modelu** a připojte ho ke **výukovým modelům** a **rozděleným datovým** modulům.
   ![skóre modelu][9]
2. Na plátno experimentu přetáhněte **Two-Class Bayes Point Machine**. Porovnáme výsledky tohoto algoritmu s rozhodovacím stromem Two-Class Boosted Decision Tree.
3. Zkopírujte na plátno moduly Train Model a Score Model.
4. Přetáhněte modul **Evaluate Model** na plátno pro porovnání obou algoritmů.
5. **Spusťte** experiment.
   ![Spusťte experiment.][10]
6. Klikněte na výstupní port v dolní části modulu Evaluate Model a klikněte na Visualize (Vizualizovat).
   ![Vizualizace výsledků vyhodnocení][11]

Dostupné jsou tyto metriky: křivka ROC, diagram přesnosti a úplnosti a křivka navýšení. Když se na tyto metriky podíváme, vidíme, že první model má lepší výsledky než druhý. Podívejte se na to, co předpověděl první model, klikněte na výstupní port modulu Score Model a klikněte na Visualize (Vizualizovat).
![Vizualizace výsledků skóre][12]

Uvidíte, že se do testovací datové sady přidaly další dva sloupce.

* Scored Probabilities (Vyhodnocené pravděpodobnosti): Pravděpodobnost, že si zákazník koupí kolo.
* Scored Labels (Popisky vyhodnocení): Klasifikace prováděná modelem – kupující (1) nebo nekupující (0) kolo. Tato prahová hodnota pravděpodobnosti pro popisky je nastavena na 50 % a je možné ji upravit.

Při porovnání sloupce BikeBuyer (skutečnost) s popisky vyhodnocení (předpověď) můžete vidět, jaká byla úspěšnost modelu. V dalších krocích můžete pomocí tohoto modelu provádět předpovědi nových zákazníků a publikovat tento model jako webovou službu nebo výsledky zapsat zpět do SQL Data Warehouse.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření prediktivních modelů strojového učení najdete v tématu [Úvod do strojového učení na platformě Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
