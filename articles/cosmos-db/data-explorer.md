---
title: Správa dat pomocí průzkumníka Azure Cosmos DB
description: Průzkumník Azure Cosmos DB je samostatné webové rozhraní, které umožňuje zobrazit a spravovat data uložená v Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096815"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Práce s daty s využitím průzkumníka Azure Cosmos 

Průzkumník Azure Cosmos DB je samostatné webové rozhraní, které umožňuje zobrazit a spravovat data uložená v Azure Cosmos DB. Průzkumník Azure Cosmos DB je ekvivalentní existující kartě **Průzkumníka dat,** která je dostupná na webu Azure Portal při vytváření účtu Azure Cosmos DB. Hlavní výhody průzkumníka Azure Cosmos DB oproti existujícímu průzkumníku dat jsou:

* Máte celou obrazovku real-estate pro zobrazení dat, spouštění dotazů, uložené procedury, aktivační události a zobrazení jejich výsledků.  

* Dočasný nebo trvalý přístup k databázovému účtu a jeho kolekcím můžete poskytnout ostatním uživatelům, kteří nemají přístup k portálu Azure nebo předplatnému.  

* Výsledky dotazu můžete sdílet s ostatními uživateli, kteří nemají přístup k portálu Azure nebo předplatnému.  

## <a name="access-azure-cosmos-db-explorer"></a>Přístup k průzkumníku Azure Cosmos DB

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/). 

2. V **části Všechny prostředky**najděte a přejděte na svůj účet Azure Cosmos DB, vyberte klíče a zkopírujte primární **připojovací řetězec**.  

3. Přejděte https://cosmos.azure.com/na , vložte připojovací řetězec a vyberte **Připojit**. Pomocí připojovacího řetězce můžete přistupovat k průzkumníku Azure Cosmos DB bez časového omezení.  

   Pokud chcete ostatním uživatelům poskytnout dočasný přístup k vašemu účtu Azure Cosmos DB, můžete tak učinit pomocí adres URL pro čtení a zápis a přístup ke čtení. 

4. Otevřete okno **Průzkumníka dat** a vyberte **Otevřít celou obrazovku**. V rozbalovacím dialogovém okně můžete zobrazit dvě adresy URL přístupu – **Čtení a zápis** a **čtení**. Tyto adresy URL umožňují dočasně sdílet váš účet Azure Cosmos DB s ostatními uživateli. Přístup k účtu vyprší za 24 hodin, po které se můžete znovu připojit pomocí nové přístupové adresy URL nebo připojovacího řetězce. 

   **Čtení a zápis** – při sdílení adresy URL pro čtení a zápis s ostatními uživateli mohou zobrazit a upravit databáze, kolekce, dotazy a další prostředky přidružené k tomuto konkrétnímu účtu.

   **Čtení** – když sdílíte adresu URL jen pro čtení s ostatními uživateli, mohou zobrazit databáze, kolekce, dotazy a další prostředky přidružené k tomuto konkrétnímu účtu. Pokud například chcete sdílet výsledky dotazu se svými spoluhráči, kteří nemají přístup k portálu Azure nebo k vašemu účtu Azure Cosmos DB, můžete jim poskytnout tuto adresu URL.

   Vyberte typ přístupu, pomocí kterých chcete účet otevřít, a klikněte na **Otevřít**. Po otevření průzkumníka je prostředí stejné, jako jste měli na kartě Průzkumník dat na webu Azure Portal.   

   ![Otevření průzkumníku Db Služby Azure Cosmos](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Známé problémy

V současné době prostředí **Otevřít celou obrazovku,** které umožňuje sdílet dočasný přístup pro čtení nebo čtení, ještě není podporováno pro účty Azure Cosmos DB Gremlin a Table API. Stále můžete zobrazit vaše účty Gremlin a Table API předáním připojovacířetězec do Aplikace Azure Cosmos DB Explorer. 

V současné době zobrazení dokumentů, které obsahují UUID není podporovánv Průzkumníku dat. To nemá vliv na načítání kolekcí, pouze zobrazení jednotlivých dokumentů nebo dotazů, které obsahují tyto dokumenty. Chcete-li tyto dokumenty zobrazit a spravovat, měli by uživatelé nadále používat nástroj, který byl původně použit k vytvoření těchto dokumentů.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak začít s Průzkumníkem Azure Cosmos DB pro správu dat, můžete dále:

* Začněte definovat [dotazy](sql-api-query-reference.md) pomocí syntaxe SQL a provádějte [programování na straně serveru](stored-procedures-triggers-udfs.md) pomocí uložených procedur, ufl, aktivačních událostí. 
