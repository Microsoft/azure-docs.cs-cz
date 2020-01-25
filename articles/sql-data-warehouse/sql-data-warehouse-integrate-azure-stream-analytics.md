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
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721196"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Použití Azure Stream Analytics se službou Azure synapse Analytics
Azure Stream Analytics je plně spravovaná služba, která poskytuje vysoce dostupná a škálovatelná komplexní zpracování událostí přes streamovaná data v cloudu s nízkou latencí. Základní informace najdete v tématu [Úvod do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Pak můžete zjistit, jak vytvořit ucelené řešení pomocí Stream Analytics pomocí kurzu Začínáme s [Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

V tomto článku se naučíte, jak používat svou databázi datového skladu jako výstupní jímku pro vaše Stream Analytics úlohy.

## <a name="prerequisites"></a>Požadavky
Nejprve proveďte následující kroky v kurzu [Začínáme s Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

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

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Uživatelské jméno*: zadejte uživatelské jméno účtu, který má pro databázi oprávnění k zápisu.
* *Heslo*: zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: zadejte název cílové tabulky v databázi.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Krok 4
Kliknutím na tlačítko se změnami přidáte tento výstup úlohy a ověříte, že se Stream Analytics může úspěšně připojit k databázi.

Po úspěšném připojení k databázi se na portálu zobrazí oznámení. Kliknutím na test můžete otestovat připojení k databázi.

## <a name="next-steps"></a>Další kroky
Přehled integrace najdete v tématu [integrace dalších služeb](sql-data-warehouse-overview-integrate.md).
Další tipy pro vývoj najdete v tématu věnovaném [rozhodování o návrhu a technikům kódování pro datové sklady](sql-data-warehouse-overview-develop.md).

