---
title: Umožňuje spravovat vaše data pomocí Průzkumníka služby Azure Cosmos DB
description: Průzkumník služby Azure Cosmos DB je samostatné webové rozhraní, které vám umožní zobrazit a spravovat data uložená ve službě Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: dech
ms.openlocfilehash: df0b031e432c91897ff61e2cd9fcadc86bb54ee2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036864"
---
# <a name="use-azure-cosmos-db-explorer-to-manage-your-data"></a>Umožňuje spravovat vaše data pomocí Průzkumníka služby Azure Cosmos DB 

Průzkumník služby Azure Cosmos DB je samostatné webové rozhraní, které vám umožní zobrazit a spravovat data uložená ve službě Azure Cosmos DB. Průzkumník služby Azure Cosmos DB je ekvivalentní ke stávající **Průzkumník dat** kartu, která je k dispozici na webu Azure portal, při vytváření účtu služby Azure Cosmos DB. Klíčové výhody služby Azure Cosmos DB explorer přes existující Průzkumník dat jsou:

* Máte zobrazení na celé obrazovce využil její plochu naplno k zobrazení dat, spouštění dotazů, uložených procedur, triggerů a zobrazit jejich výsledky.  

* Váš účet databáze a jeho kolekcích ostatním uživatelům, kteří nemají přístup k webu Azure portal nebo předplatného, můžete zadat přístup pro čtení a zápis nebo čtení dočasné nebo trvalé.  

* Výsledky dotazu můžete sdílet s ostatními uživateli, kteří nemají přístup k webu Azure portal nebo předplatné.  

## <a name="access-azure-cosmos-db-explorer"></a>Přístup služby Azure Cosmos DB explorer

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com/). 

2. Z **všechny prostředky**, najít a přejděte do vašeho účtu služby Azure Cosmos DB, vyberte klíče a zkopírujte **primární připojovací řetězec**.  

3. Přejděte na https://cosmos.azure.com/, vložte připojovací řetězec a vyberte **připojit**. S použitím připojovacího řetězce, dostanete v Průzkumníku služby Azure Cosmos DB bez jakékoli časového omezení.  

   Pokud chcete ostatním uživatelům poskytnout dočasný přístup k účtu služby Azure Cosmos DB, můžete tak provést pomocí čtení i zápis a čtení adresy URL. 

4. Otevřít **Průzkumník dat** okně vyberte **otevřít zobrazení na celé obrazovce**. V místním dialogovém okně můžete zobrazit dvě přístup k adresám URL – **čtení a zápis** a **čtení**. Tyto adresy URL umožňují dočasně sdílet účtu služby Azure Cosmos DB s dalšími uživateli. Přístup k účtu vyprší za 24 hodin, po jejichž uplynutí se můžete připojit pomocí nové adresy URL přístupu nebo připojovací řetězec. 

   **Čtení a zápis** – když sdílíte s ostatními uživateli, adresu URL pro čtení i zápis, můžete zobrazit a upravit databází, kolekcí, dotazů a další prostředky spojené s tímto účtem konkrétní.

   **Čtení** – když sdílíte s ostatními uživateli, adresa URL jen pro čtení mohou prohlížet databází, kolekcí, dotazů a další prostředky spojené s tímto účtem konkrétní. Například pokud chcete sdílet s ostatními členy týmu, kteří nemají přístup k webu Azure portal nebo účtu služby Azure Cosmos DB výsledky dotazu, můžete poskytnout jim tuto adresu URL.

   Zvolte typ přístupu, které chcete otevřít účet pomocí a klikněte na tlačítko **otevřete**. Po otevření Průzkumníka zkušenosti se shoduje s jste měli na kartě Průzkumník dat na webu Azure portal.   

   ![Otevřete Průzkumníka služby Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Známé problémy

Aktuálně **otevřít zobrazení na celé obrazovce** prostředí, které vám umožní sdílet dočasné čtení a zápis nebo přístup pro čtení pro účty Azure Cosmos DB Gremlin a Table API ještě nepodporuje. Vaše účty Gremlin a rozhraní API tabulky můžete zobrazit stále předáním připojovací řetězec do Průzkumníka služby Azure Cosmos DB. 

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak začít pracovat pomocí Průzkumníka služby Azure Cosmos DB a spravovat vaše data, dále můžete:

* Začít definovat [dotazy](sql-api-query-reference.md) pomocí syntaxe SQL a provedení [programování na straně serveru](stored-procedures-triggers-udfs.md) pomocí uložených procedur, funkcí UDF, aktivuje. 
