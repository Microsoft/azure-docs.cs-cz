---
title: Pomocí Azure Stream Analytics s využitím SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro používání Azure Stream Analytics s využitím Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 6e9a6e9c7407939ea9e76cad569e870d578b37f9
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307357"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Pomocí Azure Stream Analytics s využitím SQL Data Warehouse
Azure Stream Analytics je plně spravovaná služba poskytuje nízkou latenci, vysoce dostupná a škálovatelná komplexní zpracování událostí přes streamovaných dat v cloudu. Seznámíte se základy načtením [Úvod do služby Azure Stream Analytics][Introduction to Azure Stream Analytics]. Můžete pak zjistěte, jak vytvořit-ucelené řešení Stream Analytics pomocí následujících [začít používat Azure Stream Analytics] [ Get started using Azure Stream Analytics] kurzu.

V tomto článku se dozvíte, jak používat databázi Azure SQL Data Warehouse jako výstupní jímku pro úlohy Stream Analytics.

## <a name="prerequisites"></a>Požadavky
Nejprve spusťte následující kroky v [začít používat Azure Stream Analytics] [ Get started using Azure Stream Analytics] kurzu.  

1. Vytvoření centra událostí vstup
2. Nakonfigurujte a spusťte aplikaci generátoru událostí
3. Vytvořit úlohu Stream Analytics
4. Zadejte vstup úlohy a dotaz

Vytvořte databázi Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Zadejte výstup úlohy: databázi Azure SQL Data Warehouse
### <a name="step-1"></a>Krok 1
V úloze Stream Analytics klikněte na tlačítko **výstup** z horní části stránky a pak klikněte na tlačítko **přidat výstup**.

### <a name="step-2"></a>Krok 2
Vyberte databázi SQL a klikněte na tlačítko Další.

![][add-output]

### <a name="step-3"></a>Krok 3
Na další stránce zadejte následující hodnoty:

* *Výstup Alias*: Zadejte popisný název pro tento výstup úlohy.
* *Předplatné*:
  * Pokud vaši databázi SQL Data Warehouse je ve stejném předplatném jako úloha Stream Analytics, vyberte z aktuálního předplatného použijte SQL Database.
  * Pokud vaše databáze je v jiném předplatném, vyberte použít databázi SQL z jiného předplatného.
* *Databáze*: Zadejte název cílové databáze.
* *Název serveru*: Zadejte název serveru pro databázi, které jste zadali. Na webu Azure portal můžete použít ten zjistíte.

![][server-name]

* *Uživatelské jméno*: Zadejte uživatelské jméno účtu, který má oprávnění k zápisu pro databázi.
* *Heslo*: Zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: Zadejte název cílové tabulky v databázi.

![][add-database]

### <a name="step-4"></a>Krok 4
Klikněte na tlačítko se symbolem zaškrtnutí, chcete-li přidat tento výstup úlohy a ověřte, jestli Stream Analytics můžete úspěšně připojit k databázi.

![][test-connection]

Při připojení k databázi úspěšný, zobrazí se oznámení v dolní části portálu. Můžete kliknout na Test připojení v dolní části a otestujte připojení k databázi.

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
