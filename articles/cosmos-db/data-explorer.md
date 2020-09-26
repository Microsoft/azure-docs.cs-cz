---
title: Použití aplikace Azure Cosmos DB Explorer ke správě dat
description: Azure Cosmos DB Explorer je samostatné webové rozhraní, které umožňuje zobrazit a spravovat data uložená v Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: ebfb175de67d7bb8ea011ac340b57f5d62d9e223
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318802"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Práce s daty s využitím průzkumníka Azure Cosmos 

Azure Cosmos DB Explorer je samostatné webové rozhraní, které umožňuje zobrazit a spravovat data uložená v Azure Cosmos DB. Azure Cosmos DB Explorer je ekvivalentní existující kartě **Průzkumník dat** , která je k dispozici v Azure Portal při vytváření účtu Azure Cosmos DB. Klíčovými výhodami pro Azure Cosmos DB Explorer v rámci stávajících Průzkumníku dat jsou tyto:

* Máte celou obrazovku, ve které můžete zobrazit vaše data, spouštět dotazy, uložené procedury, triggery a zobrazovat jejich výsledky.  

* K vašemu databázovému účtu a kolekcím můžete poskytnout dočasný nebo trvalý přístup pro čtení nebo čtení a zápis jiným uživatelům, kteří nemají přístup k Azure Portal nebo předplatnému.  

* Výsledky dotazu můžete sdílet s jinými uživateli, kteří nemají přístup k Azure Portal nebo předplatnému.  

## <a name="access-azure-cosmos-db-explorer"></a>Přístup k Azure Cosmos DB Exploreru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 

2. V části **všechny prostředky**vyhledejte a přejděte na účet Azure Cosmos DB, vyberte klíče a zkopírujte **primární připojovací řetězec**.  

3. Přejít na https://cosmos.azure.com/ , vložte připojovací řetězec a vyberte **připojit**. Pomocí připojovacího řetězce můžete získat přístup k Průzkumníku Azure Cosmos DB bez jakýchkoli časových omezení.  

   Pokud chcete jiným uživatelům poskytnout dočasný přístup k vašemu Azure Cosmos DB účtu, můžete k tomu použít adresy URL pro čtení i zápis a přístup pro čtení. 

4. Otevřete okno **Průzkumník dat** vyberte otevřít na **celé obrazovce**. V místním dialogovém okně můžete zobrazit dvě adresy URL pro přístup – čtení i **zápis** a **čtení**. Tyto adresy URL umožňují dočasně sdílet Azure Cosmos DB účet s ostatními uživateli. Přístup k účtu vyprší během 24 hodin, po kterém se můžete znovu připojit pomocí nové adresy URL pro přístup nebo připojovacího řetězce. 

   **Čtení i zápis** – když sdílíte adresu URL pro čtení i zápis s ostatními uživateli, můžou si zobrazit a upravit databáze, kolekce, dotazy a další prostředky, které jsou přidružené k tomuto konkrétnímu účtu.

   **Číst** – když sdílíte adresu URL jen pro čtení s ostatními uživateli, můžou si Zobrazit databáze, kolekce, dotazy a další prostředky, které jsou přidružené k tomuto konkrétnímu účtu. Pokud třeba chcete sdílet výsledky dotazu s ostatními týmu, kteří nemají přístup k Azure Portal nebo vašemu účtu Azure Cosmos DB, můžete je zadat pomocí této adresy URL.

   Vyberte typ přístupu, se kterým chcete účet otevřít, a klikněte na **otevřít**. Po otevření Průzkumníka je prostředí stejné jako při použití karty Průzkumník dat v Azure Portal.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Otevřít Azure Cosmos DB Explorer":::

## <a name="known-issues"></a>Známé problémy

Aktuálně otevřené prostředí na **celé obrazovce** , které vám umožní sdílet dočasný přístup pro čtení i zápis nebo čtení, se zatím nepodporuje pro účty Azure Cosmos DB Gremlin a rozhraní API pro tabulky. I nadále můžete zobrazit účty Gremlin a rozhraní API pro tabulky předáním připojovacího řetězce do Průzkumníka Azure Cosmos DB. 

V současné době není v Průzkumník dat podporováno zobrazení dokumentů, které obsahují identifikátor UUID. To nemá vliv na načítání kolekcí, zobrazení pouze jednotlivých dokumentů nebo dotazů, které obsahují tyto dokumenty. Chcete-li zobrazit a spravovat tyto dokumenty, měli by uživatelé nadále používat nástroj, který byl původně použit k vytvoření těchto dokumentů.

Zákazníci, kteří získají chyby HTTP-401, můžou být kvůli nedostatečným oprávněním RBAC pro účet Azure zákazníka, zejména v případě, že má účet vlastní roli RBAC. `Microsoft.DocumentDB/databaseAccounts/listKeys/*`Pokud se přihlašujete pomocí přihlašovacích údajů Azure Active Directory, musí mít všechny vlastní role akci pro použití Průzkumník dat.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít s Azure Cosmos DB Explorer pro správu vašich dat, můžete následující:

* Začněte s definováním [dotazů](sql-api-query-reference.md) pomocí syntaxe SQL a provádějte [programování na straně serveru](stored-procedures-triggers-udfs.md) pomocí uložených procedur, UDF a triggerů. 
