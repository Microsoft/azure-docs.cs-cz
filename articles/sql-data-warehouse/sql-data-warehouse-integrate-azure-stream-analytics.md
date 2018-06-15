---
title: Použití služby Azure Stream Analytics s SQL Data Warehouse | Microsoft Docs
description: Tipy pro používání Azure Stream Analytics s datovým skladem SQL Azure pro vývoj řešení.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 76cbbddca70d3bc8091dbea383213446adac533e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600304"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Použití služby Azure Stream Analytics s SQL Data Warehouse
Azure Stream Analytics je plně spravovaná služba poskytuje nízkou latencí, vysoce dostupných, škálovatelných komplexní zpracování událostí přes streamování dat v cloudu. Seznámíte se základy načtením [Úvod do služby Azure Stream Analytics][Introduction to Azure Stream Analytics]. Můžete pak zjistíte, jak vytvořit-komplexní řešení pomocí služby Stream Analytics podle [začít používat Azure Stream Analytics] [ Get started using Azure Stream Analytics] kurzu.

V tomto článku se dozvíte, jak používat databázi Azure SQL Data Warehouse jako výstupní jímku pro datový proud Analytics úlohy.

## <a name="prerequisites"></a>Požadavky
Nejprve spusťte pomocí následujících kroků v [začít používat Azure Stream Analytics] [ Get started using Azure Stream Analytics] kurzu.  

1. Vytvoření centra událostí vstup
2. Nakonfigurujte a spusťte aplikaci generátor událostí
3. Zřídit úloha Stream Analytics
4. Zadejte vstup úlohy a dotaz

Pak vytvořte databázi Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Zadejte výstup úlohy: databáze Azure SQL Data Warehouse
### <a name="step-1"></a>Krok 1
V úloze Stream Analytics klikněte na tlačítko **výstup** z horní části stránky a pak klikněte na tlačítko **přidat výstup**.

### <a name="step-2"></a>Krok 2
Vyberte databázi SQL a klikněte na tlačítko Další.

![][add-output]

### <a name="step-3"></a>Krok 3
Na další stránce zadejte následující hodnoty:

* *Výstup Alias*: Zadejte popisný název pro tento výstup úlohy.
* *Předplatné*:
  * Pokud vaše databáze SQL Data Warehouse je ve stejném předplatném jako úlohu služby Stream Analytics, vyberte použít databázi SQL z aktuálního předplatného.
  * Pokud vaše databáze je v jiném předplatném, vyberte použít SQL Database z jiné předplatné.
* *Databáze*: Zadejte název cílové databáze.
* *Název serveru*: Zadejte název serveru pro databázi, který jste právě určili. Na portálu Azure můžete použít k vyhledání to.

![][server-name]

* *Uživatelské jméno*: Zadejte uživatelské jméno účtu, který má oprávnění k zápisu pro databázi.
* *Heslo*: Zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: Zadejte název cílové tabulky v databázi.

![][add-database]

### <a name="step-4"></a>Krok 4
Klikněte na tlačítko zaškrtnutí chcete přidat tento výstup úlohy a ověřte, zda Stream Analytics můžete úspěšně připojit k databázi.

![][test-connection]

Pokud je připojení k databázi úspěšná, zobrazí se oznámení v dolní části portálu. Kliknutí na tlačítko Testovat připojení v dolní části, abyste otestovali připojení k databázi.

## <a name="next-steps"></a>Další postup
Přehled integrace najdete v tématu [Přehled integrace SQL Data Warehouse][SQL Data Warehouse integration overview].

Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
