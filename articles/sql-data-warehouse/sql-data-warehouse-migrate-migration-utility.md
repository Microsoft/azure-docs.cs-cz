---
title: "Migrace: Datového skladu nástroje Migrace | Microsoft Docs"
description: Migrace do SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Nástroj migrace data Warehouse (Preview)
> [!div class="op_single_selector"]
> * [Stáhněte si nástroj pro migraci][Download Migration Utility]
> 
> 

Nástroj datového skladu migrace je nástroj sloužící k migraci schéma a data ze systému SQL Server a databáze SQL Azure do Azure SQL Data Warehouse. Během migrace schématu nástroj automaticky mapuje odpovídající schématu ze zdrojového do cílového umístění. Po migraci schématu nástroje poskytuje možnost pro přesun dat se automaticky generovaných skriptů.

Kromě schéma a data migrace tento nástroj vám dává možnost k vygenerování sestavy kompatibility, které shrnují problémům s kompatibilitou mezi zdrojové a cílové instance, které by jinak znemožňovaly efektivní migrace.

## <a name="get-started"></a>Začínáme
Předpokladem pro instalaci musíte nástroj příkazového řádku BCP spouštět skripty pro migraci a Office tak, aby zobrazení sestavy kompatibility. Po spuštění spustitelného souboru, který byl stažen se zobrazí výzva k přijmout standardní smlouvy EULA předtím, než se nainstaluje nástroj.

Kromě toho, pokud chcete spustit Utiliy migrace, budete potřebovat ten databáze, která pokud chcete migrovat následující oprávnění: vytvoření databáze, ALTER ANY DATABASE nebo ANY definice zobrazení.

### <a name="launching-the-tool-and-connecting"></a>Spuštění nástroje a připojení
Spusťte nástroj kliknutím na ikonu plochy, která se zobrazí po instalaci. Po otevření nástroje, zobrazí se výzva počáteční připojení stránky, kde si můžete vybrat zdroj a cíl pro nástroj pro migraci. V současné době podporujeme systému SQL Server a Azure SQL Database jako zdroje a SQL Data Warehouse jako cíl. Po vyberete tuto možnost se zobrazí výzva k připojení k serveru zdroj naplnění v názvu serveru a ověřování a potom klikněte na "Připojit".

Po ověření, zobrazí nástroj seznam databází, které se nacházejí na serveru, které jste připojeni k. Výběrem databázi, která chcete migrovat a kliknete na 'migrací vybraná, můžete začít migrace.

## <a name="migration-report"></a>Sestavy migrace
Výběr zkontrolovat kompatibilitu databáze v nástroji bude vygenerovat sestavu shrnutí všech nekompatibility objektu v databázi, že požadujete k migraci. Širší seznam některých funkcí systému SQL Server, který se nenachází v SQL Data Warehouse najdete v našich [dokumentace k migraci][migration documentation]. Po vygenerování sestavy budou moci uložit a otevřete sestavu v aplikaci Excel.

Pamatujte, že při generování schéma migrace identifikovány jako 'Objekt' se upraví, aby bylo možné povolit okamžitou migraci dat většiny problémů. Zkontrolujte změny Ujistěte se, že nechcete provádět další úpravy před použitím schématu.

## <a name="migrate-schema"></a>Migrace schématu
Po připojení, vygeneruje výběr 'migraci schématu skript pro migraci schématu pro vybrané tabulky. Tento skript porty struktura tabulky, nekompatibilní data mapy typy do více kompatibilní formulářů a vytvoří zabezpečovací přihlašovací údaje a schéma, pokud je to uvedeno uživatelem v nastavení migrace. Tento kód můžete spustit proti cílové instanci SQL Data Warehouse, uloží do souboru, zkopírovat do schránky nebo ani upravit v řádku před provedením další akce.  

Jak je uvedeno výše, při migraci schématu zkontrolujte migrace změny nástroj udělal aby se zajistilo, že plně rozumíte jim.  

## <a name="migrate-data"></a>Migrace dat
Kliknutím na možnost "migrace dat může generovat BCP skripty, které bude přesunutí dat nejprve do plochých souborů na serveru, a pak přímo do SQL Data Warehouse. Doporučujeme tento proces pro přesun malé množství dat a jako opakování nejsou předdefinované a selhání může dojít, pokud dojde ke ztrátě síťové připojení. Aby bylo možné ji spustit, musíte mít nainstalovaný nástroj příkazového řádku BCP a schéma pro data musí již byly vytvořeny.

Po vyplnění výše uvedené parametry jednoduše muset kliknout na spuštění migrace a sadu dva balíčky se budou generovat do zadaného umístění. Spusťte soubor exportu aby bylo možné exportovat data ze zdroje migrace do plochých souborů a spusťte soubor importu Chcete-li importovat data do SQL Data Warehouse.

## <a name="next-steps"></a>Další kroky
Teď, když jste migrovali některá data, podívejte se na postup [vyvíjet][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
