---
title: Analýza dat pomocí Azure Machine Learning | Dokumentace Microsoftu
description: Pomocí Azure Machine Learning sestavte prediktivní model Machine Learning založený na datech uložených v datovém skladu SQL Azure.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 4324b1ac343a0e2b77c21d7834beffae08403953
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247522"
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

## <a name="1-get-the-data"></a>1. Získání dat
Data jsou v zobrazení dbo.vTargetMail v databázi AdventureWorksDW. Postup načtení těchto dat:

1. Přihlaste se k [Azure Machine Learning Studio][Azure Machine Learning studio] a klikněte na Moje experimenty.
2. Klikněte na **+NOVÝ** a vyberte **Prázdný experiment**.
3. Zadejte název svého experimentu: Cílený marketing.
4. Přetáhněte modul **Reader** z podokna modulů na plátno.
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

## <a name="2-clean-the-data"></a>2. Vymazání dat
Pro vymazání dat odstraňte některé sloupce, které nejsou pro model důležité. Použijte následující postup:

1. Přetáhněte na plátno modul **Project Columns** (Sloupce projektu).
2. V podokně vlastností klikněte na **Launch column selector** (Spustit selektor sloupců) a určete sloupce, které se mají odstranit.
   ![Project Columns][4]
3. Vylučte dva sloupce: CustomerAlternateKey a GeographyKey.
   ![Odebrání nepotřebných sloupců][5]

## <a name="3-build-the-model"></a>3. Sestavení modelu
Data rozdělíme v poměru 80:20 – 80 % pro učení v rámci modelu strojového učení a 20 % pro otestování modelu. Pro tento problém binární klasifikace budeme používat algoritmy Two-Class.

1. Přetáhněte na plátno modul **Split**.
2. Nastavte podíl řádků v první výstupní sadě dat v podokně vlastností na 0,8.
   ![Rozdělení dat na sadu učení a testovací sadu][6]
3. Přetáhněte na plátno modul **Two-Class Boosted Decision Tree**.
4. Přetáhněte na plátno modul **Train Model** a určete vstupy. V podokně vlastností pak klikněte na **Launch column selector** (Spustit selektor sloupců).
   * První vstup: Algoritmus strojového učení
   * Druhý vstup: Data pro učení algoritmu
     ![Připojení modulu Train Model][7]
5. Jako sloupec pro předpověď vyberte sloupec **BikeBuyer**.
   ![Vyberte sloupec pro předpověď][8]

## <a name="4-score-the-model"></a>4. Ohodnocení modelu
Teď otestujeme, jaký je výkon modelu při použití testovacích dat. Porovnáme námi zvolený algoritmus s jiným algoritmem, abychom zjistili, který z nich vrací lepší výsledky.

1. Přetáhněte na plátno modul **Score Model**.
    První vstup: Train model Druhý vstup: Test data ![Modul Score Model][9]
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

## <a name="next-steps"></a>Další postup
Další informace o vytváření prediktivních modelů strojového učení najdete v tématu [Úvod do strojového učení na platformě Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
