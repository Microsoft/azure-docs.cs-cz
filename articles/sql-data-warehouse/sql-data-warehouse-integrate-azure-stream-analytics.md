---
title: Použít Azure Stream Analytics
description: Tipy pro použití Azure Stream Analytics s Azure SQL Data Warehouse pro vývoj řešení
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
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685720"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Použití Azure Stream Analytics s SQL Data Warehouse
Azure Stream Analytics je plně spravovaná služba, která poskytuje vysoce dostupná a škálovatelná komplexní zpracování událostí přes streamovaná data v cloudu s nízkou latencí. Základní informace najdete v tématu [Úvod do Azure Stream Analytics][Introduction to Azure Stream Analytics]. Pak můžete zjistit, jak vytvořit ucelené řešení pomocí Stream Analytics pomocí kurzu Začínáme s [Azure Stream Analytics][Get started using Azure Stream Analytics] .

V tomto článku se naučíte, jak používat svou databázi Azure SQL Data Warehouse jako výstupní jímku pro úlohy Stream Analytics.

## <a name="prerequisites"></a>Požadavky
Nejprve proveďte následující kroky v kurzu [Začínáme s Azure Stream Analytics][Get started using Azure Stream Analytics] .  

1. Vytvoření vstupu centra událostí
2. Konfigurace a spuštění aplikace generátoru událostí
3. Zřízení Stream Analytics úlohy
4. Zadat vstup a dotaz úlohy

Pak vytvořte databázi Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Zadejte výstup úlohy: Azure SQL Data Warehouse databázi
### <a name="step-1"></a>Krok 1
V úloze Stream Analytics klikněte na **výstup** v horní části stránky a pak klikněte na **Přidat**.

### <a name="step-2"></a>Krok 2
Vyberte SQL Database.

### <a name="step-3"></a>Krok 3
Na další stránce zadejte následující hodnoty:

* *Alias pro výstup*: zadejte popisný název pro tento výstup úlohy.
* *Předplatné:*
  * Pokud je vaše databáze SQL Data Warehouse ve stejném předplatném jako Stream Analytics úloha, vyberte použít SQL Database z aktuálního předplatného.
  * Pokud je vaše databáze v jiném předplatném, vyberte použít SQL Database z jiného předplatného.
* *Databáze*: zadejte název cílové databáze.
* *Název serveru*: zadejte název serveru pro databázi, kterou jste právě zadali. Tuto možnost můžete najít pomocí Azure Portal.

![][server-name]

* *Uživatelské jméno*: zadejte uživatelské jméno účtu, který má pro databázi oprávnění k zápisu.
* *Heslo*: zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: zadejte název cílové tabulky v databázi.

![][add-database]

### <a name="step-4"></a>Krok 4
Kliknutím na tlačítko se změnami přidáte tento výstup úlohy a ověříte, že se Stream Analytics může úspěšně připojit k databázi.

Po úspěšném připojení k databázi se na portálu zobrazí oznámení. Kliknutím na test můžete otestovat připojení k databázi.

## <a name="next-steps"></a>Další kroky
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
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
