---
title: Umožňuje spravovat vaše data pomocí Průzkumníka služby Azure Cosmos DB
description: Průzkumník služby Azure Cosmos DB je samostatné webové rozhraní, které vám umožní zobrazit a spravovat data uložená ve službě Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096815"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Práce s daty s využitím průzkumníka Azure Cosmos 

Průzkumník služby Azure Cosmos DB je samostatné webové rozhraní, které vám umožní zobrazit a spravovat data uložená ve službě Azure Cosmos DB. Azure Cosmos DB Explorer je ekvivalentní existující kartě **Průzkumník dat** , která je k dispozici v Azure Portal při vytváření účtu Azure Cosmos DB. Klíčové výhody služby Azure Cosmos DB explorer přes existující Průzkumník dat jsou:

* Máte zobrazení na celé obrazovce využil její plochu naplno k zobrazení dat, spouštění dotazů, uložených procedur, triggerů a zobrazit jejich výsledky.  

* Váš účet databáze a jeho kolekcích ostatním uživatelům, kteří nemají přístup k webu Azure portal nebo předplatného, můžete zadat přístup pro čtení a zápis nebo čtení dočasné nebo trvalé.  

* Výsledky dotazu můžete sdílet s ostatními uživateli, kteří nemají přístup k webu Azure portal nebo předplatné.  

## <a name="access-azure-cosmos-db-explorer"></a>Přístup služby Azure Cosmos DB explorer

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 

2. V části **všechny prostředky**vyhledejte a přejděte na účet Azure Cosmos DB, vyberte klíče a zkopírujte **primární připojovací řetězec**.  

3. Otevřete https://cosmos.azure.com/, vložte připojovací řetězec a vyberte **připojit**. S použitím připojovacího řetězce, dostanete v Průzkumníku služby Azure Cosmos DB bez jakékoli časového omezení.  

   Pokud chcete ostatním uživatelům poskytnout dočasný přístup k účtu služby Azure Cosmos DB, můžete tak provést pomocí čtení i zápis a čtení adresy URL. 

4. Otevřete okno **Průzkumník dat** vyberte otevřít na **celé obrazovce**. V místním dialogovém okně můžete zobrazit dvě adresy URL pro přístup – čtení i **zápis** a **čtení**. Tyto adresy URL umožňují dočasně sdílet účtu služby Azure Cosmos DB s dalšími uživateli. Přístup k účtu vyprší za 24 hodin, po jejichž uplynutí se můžete připojit pomocí nové adresy URL přístupu nebo připojovací řetězec. 

   **Čtení i zápis** – když sdílíte adresu URL pro čtení i zápis s ostatními uživateli, můžou si zobrazit a upravit databáze, kolekce, dotazy a další prostředky, které jsou přidružené k tomuto konkrétnímu účtu.

   **Číst** – když sdílíte adresu URL jen pro čtení s ostatními uživateli, můžou si Zobrazit databáze, kolekce, dotazy a další prostředky, které jsou přidružené k tomuto konkrétnímu účtu. Například pokud chcete sdílet s ostatními členy týmu, kteří nemají přístup k webu Azure portal nebo účtu služby Azure Cosmos DB výsledky dotazu, můžete poskytnout jim tuto adresu URL.

   Vyberte typ přístupu, se kterým chcete účet otevřít, a klikněte na **otevřít**. Po otevření Průzkumníka zkušenosti se shoduje s jste měli na kartě Průzkumník dat na webu Azure portal.   

   ![Otevřete Průzkumníka služby Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Známé problémy

Aktuálně otevřené prostředí na **celé obrazovce** , které vám umožní sdílet dočasný přístup pro čtení i zápis nebo čtení, se zatím nepodporuje pro účty Azure Cosmos DB Gremlin a rozhraní API pro tabulky. Vaše účty Gremlin a rozhraní API tabulky můžete zobrazit stále předáním připojovací řetězec do Průzkumníka služby Azure Cosmos DB. 

V současné době není v Průzkumník dat podporováno zobrazení dokumentů, které obsahují identifikátor UUID. To nemá vliv na načítání kolekcí, zobrazení pouze jednotlivých dokumentů nebo dotazů, které obsahují tyto dokumenty. Chcete-li zobrazit a spravovat tyto dokumenty, měli by uživatelé nadále používat nástroj, který byl původně použit k vytvoření těchto dokumentů.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak začít pracovat pomocí Průzkumníka služby Azure Cosmos DB a spravovat vaše data, dále můžete:

* Začněte s definováním [dotazů](sql-api-query-reference.md) pomocí syntaxe SQL a provádějte [programování na straně serveru](stored-procedures-triggers-udfs.md) pomocí uložených procedur, UDF a triggerů. 
