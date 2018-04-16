---
title: "SQL Data Warehouse pomocí Azure Machine Learning | Microsoft Docs"
description: "Kurz pro používání Azure Machine Learning s Azure SQL Data Warehousem pro vývoj řešení."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: c19860c6b5b1c15d1e29ddc67f9cf9ad4618725b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>SQL Data Warehouse pomocí Azure Machine Learning
Azure Machine Learning je plně spravovaná prediktivní analýzy služba, která můžete použít k vytvoření prediktivní modely pro vaše data v SQL Data Warehouse a pak publikovat jako připravené využívají webové služby. Můžete seznámíte se základy prediktivní analýzy a strojového učení načtením [Úvod do strojového učení na platformě Azure][Introduction to Machine Learning on Azure].  Můžete pak zjistíte, jak k vytváření, trénování, stanovení skóre a otestování machine learning pomocí modelu [vytvořit experimentu kurzu][Create experiment tutorial].

V tomto článku se dozvíte, jak to provést pomocí následujících [Azure Machine Learning Studio][Azure Machine Learning Studio]:

* Čtení dat z databáze k vytváření, trénování a stanovení skóre prediktivního modelu
* Zapsat data do databáze

## <a name="read-data-from-sql-data-warehouse"></a>Čtení dat z SQL Data Warehouse
Jsme bude číst data z produktu tabulky v databázi AdventureWorksDW.

### <a name="step-1"></a>Krok 1
Začněte nový experiment kliknutím na + nové v dolní části okna Machine Learning Studio vyberte EXPERIMENT a pak vyberte prázdný Experiment. Vyberte výchozí název v horní části na plátno experimentu a přejmenujte jej na něco smysluplného, například předpověď ceny jízdních kol.

### <a name="step-2"></a>Krok 2
Vyhledejte modul čtečky v paleta datových sad a modulů nalevo od plátna experimentu. Přetáhněte na plátno experimentu modul.
![][drag_reader]

### <a name="step-3"></a>Krok 3
Vyberte modul čtečky a vyplňte v podokně Vlastnosti.

1. Jako zdroj dat, vyberte databázi Azure SQL.
2. Název databázového serveru: Zadejte název serveru. Můžete použít [portál Azure] [ Azure portal] najít to.

![][server_name]

1. Název databáze: Zadejte název databáze na serveru, který jste právě určili.
2. Název uživatelského účtu serveru: Zadejte uživatelské jméno účtu, který má přístupová oprávnění k databázi.
3. Heslo uživatelského účtu serveru: Zadejte heslo pro zadaný uživatelský účet.
4. Přijměte všechny certifikát serveru: pomocí této možnosti (méně bezpečné), pokud chcete nechat přeskočit kontrola certifikát lokality před číst vaše data.
5. Databázový dotaz: Zadejte příkaz SQL, který popisuje data chcete číst. V takovém případě jsme bude číst data z tabulky produktu pomocí následujícího dotazu.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Krok 4
1. Spusťte experiment kliknutím na tlačítko spustit pod plátnem experimentu.
2. Až se experiment dokončí, bude mít modulu Reader zelená značka zaškrtnutí označující, zda byla úspěšně dokončena. Všimněte si také dokončeno, stav spuštění v pravém horním rohu.

![][run]

1. Chcete-li zobrazte naimportovaná data, klikněte na výstupní port v dolní části datové sady automobilů a vyberte vizualizovat.

## <a name="create-train-and-score-a-model"></a>Vytváření, trénování a stanovení skóre modelu
Teď můžete použít tuto datovou sadu, která:

* Vytvoření modelu: zpracování dat a definice funkcí
* Trénování modelu: volba a použití algoritmu učení
* Stanovení skóre a otestování modelu: předpovědi nová jízdních kol cena

![][model]

Další informace o tom, jak vytvořit, trénování, stanovení skóre a otestování machine learning použití modelu [vytvořit experimentu kurzu][Create experiment tutorial].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Zapsat data do Azure SQL Data Warehouse
Zapíše jsme sadu výsledků do ProductPriceForecast tabulky v databázi AdventureWorksDW.

### <a name="step-1"></a>Krok 1
Vyhledejte modul zapisovače v paleta datových sad a modulů nalevo od plátna experimentu. Přetáhněte na plátno experimentu modul.

![][drag_writer]

### <a name="step-2"></a>Krok 2
Vyberte modul, zapisovače a vyplňte v podokně Vlastnosti.

1. Vyberte databázi Azure SQL jako cíl Data.
2. Název databázového serveru: Zadejte název serveru. Můžete použít [portál Azure] [ Azure portal] najít to.
3. Název databáze: Zadejte název databáze na serveru, který jste právě určili.
4. Název uživatelského účtu serveru: Zadejte uživatelské jméno účtu, který má oprávnění k zápisu pro databázi.
5. Heslo uživatelského účtu serveru: Zadejte heslo pro zadaný uživatelský účet.
6. Přijměte všechny certifikát serveru (nezabezpečené): tuto možnost vyberte, pokud nechcete zobrazit certifikát.
7. Čárkami oddělený seznam sloupce, které chcete uložit: Zadejte seznam datové sady nebo výsledek sloupce, které chcete výstup.
8. Název tabulky dat: Zadejte název tabulky data.
9. Seznam oddělený čárkami datatable sloupců: Zadejte názvy sloupců pro použití v nové tabulce. Názvy sloupců se může lišit od těch v datové sadě zdroje, ale musí seznam stejný počet sloupců sem, kterou definujete pro výstupní tabulku.
10. Počet řádků zapsaných za operace SQL Azure: můžete konfigurovat počet řádků, které jsou zapsány do databáze SQL v rámci jedné operace.

![][writer_properties]

### <a name="step-3"></a>Krok 3
1. Spusťte experiment kliknutím na tlačítko spustit pod plátnem experimentu.
2. Až se experiment dokončí, bude mít všechny moduly zelená značka zaškrtnutí označující, že se úspěšně dokončila.

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
