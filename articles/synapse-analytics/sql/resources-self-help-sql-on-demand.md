---
title: SQL on-demand Preview) svépomoc
description: Tato část obsahuje informace, které vám pomohou vyřešit problémy s SQL na vyžádání (náhled).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424829"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Svépomoc pro SQL na vyžádání (náhled)

Tento článek obsahuje informace o tom, jak řešit nejčastější problémy s SQL na vyžádání (preview) v Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand je šedě v Synapse Studio

Pokud Synapse Studio nemůže navázat připojení k SQL na vyžádání, zjistíte, že SQL on-demand je šedě nebo zobrazuje stav "Offline". Obvykle k tomuto problému dochází, když dojde k jednomu z následujících případů:

1) Vaše síť zabraňuje komunikaci s back-endem Azure Synapse. Nejčastějším případem je, že port 1443 je blokován. Chcete-li získat SQL na vyžádání pracovat odblokovat tento port. Další informace by mohly zabránit tomu, aby sql on-demand fungoval, [navštivte úplný průvodce odstraňováním potíží](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nemáte oprávnění k přihlášení k SQL na vyžádání. Chcete-li získat přístup, jeden ze správců pracovního prostoru Azure Synapse by měl přidat do role správce pracovního prostoru nebo správce SQL. [Další informace naleznete v úplném průvodci řízením přístupu](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Dotaz se nezdaří, protože soubor nelze otevřít.

Pokud se dotaz nezdaří s chybou "Soubor nelze otevřít, protože neexistuje nebo je používán jiným procesem" a jste si jisti, že oba soubory existují a není používán jiným procesem, znamená to, že SQL na vyžádání nemá přístup k souboru. K tomuto problému obvykle dochází, protože vaše identita Služby Azure Active Directory nemá práva pro přístup k souboru. Ve výchozím nastavení se sql na vyžádání pokouší o přístup k souboru pomocí identity služby Azure Active Directory. Chcete-li tento problém vyřešit, musíte mít správná práva pro přístup k souboru. Nejjednodušší způsob je udělit si roli přispěvatele dat objektů blob úložiště na účtu úložiště, na který se pokoušíte dotazovat. [Další informace najdete v úplném průvodci řízením přístupu služby Azure Active Directory.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Dotaz se nezdaří, protože jej nelze spustit z důvodu omezení aktuálního prostředku. 

Pokud se dotaz nezdaří s chybovou zprávou "Tento dotaz nelze spustit z důvodu aktuálních omezení prostředků", znamená to, že SQL OD není schopen jej v tuto chvíli spustit z důvodu omezení prostředků: 

- Zkontrolujte, zda jsou použity datové typy přiměřených velikostí. Také zadejte schéma pro parketové soubory pro sloupce řetězce, protože budou ve výchozím nastavení VARCHAR(8000). 

- Pokud se dotaz zaměřuje na soubory CSV, zvažte [vytvoření statistiky](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Chcete-li optimalizovat dotaz, navštivte [doporučené postupy výkonu pro SQL na vyžádání.](best-practices-sql-on-demand.md)  

## <a name="next-steps"></a>Další kroky

Další informace o použití sql na vyžádání naleznete v následujících článcích:

- [Dotaz na jeden soubor CSV](query-single-csv-file.md)

- [Složky dotazů a více souborů CSV](query-folders-multiple-csv-files.md)

- [Soubory specifické pro dotaz](query-specific-files.md)

- [Soubory parket dotazu](query-parquet-files.md)

- [Typy vnořených par dotazů](query-parquet-nested-types.md)

- [Dotaz na soubory JSON](query-json-files.md)

- [Vytváření a používání zobrazení](create-use-views.md)

- [Vytváření a používání externích tabulek](create-use-external-tables.md)

- [Uložit výsledky dotazů do úložiště](create-external-table-as-select.md)
