---
title: Řešení potíží s synapse SQL
description: Řešení potíží s synapse SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bce5e14db133ad55a2035c0c8074486ed1b8a6ec
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460503"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>Řešení potíží s synapse SQL ve službě Azure synapse Analytics

V tomto článku jsou uvedené běžné problémy s odstraňováním problémů v synapse SQL.

## <a name="connecting"></a>Připojovaného

| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Přihlášení uživatele NT AUTHORITY\ANONYMOUS LOGON selhalo. (Microsoft SQL Server, chyba: 18456) | K této chybě dojde, když se uživatel Azure AD pokusí připojit k hlavní databázi, ale nemá v hlavní větvi žádného uživatele.  Chcete-li tento problém vyřešit, zadejte buď fond SQL, ke kterému se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další podrobnosti najdete v článku [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md) . |
| Objekt zabezpečení serveru MyUserName nemůže v aktuálním kontextu zabezpečení získat přístup k databázi master. Výchozí databázi uživatele nelze otevřít. Přihlášení se nezdařilo. Nezdařilo se přihlášení pro uživatele myUserName. (Microsoft SQL Server, chyba: 916) | K této chybě dojde, když se uživatel Azure AD pokusí připojit k hlavní databázi, ale nemá v hlavní větvi žádného uživatele.  Chcete-li tento problém vyřešit, zadejte buď fond SQL, ke kterému se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další podrobnosti najdete v článku [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md) . |
| Chyba CTAIP                                                  | K této chybě může dojít, když bylo vytvořeno přihlášení v hlavní databázi SQL Database, ale ne v konkrétní databázi SQL.  Pokud se zobrazí tato chyba, přečtěte si článek [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md) .  Tento článek vysvětluje, jak vytvořit přihlašovací jméno a uživatele v hlavní databázi a jak vytvořit uživatele v databázi SQL. |
| Blokováno bránou firewall                                          | Fondy SQL jsou chráněné branami firewall, aby se zajistil přístup k databázi jenom známým IP adresám. Brány firewall jsou ve výchozím nastavení zabezpečené, což znamená, že před připojením musíte explicitně povolit a IP adresu nebo rozsah adres.  Pokud chcete bránu firewall nakonfigurovat pro přístup, postupujte podle pokynů v tématu [Konfigurace přístupu k bráně firewall serveru pro IP adresu vašeho klienta](create-data-warehouse-portal.md) v [pokynech k zřizování](create-data-warehouse-portal.md). |
| Nejde se připojit pomocí nástroje nebo ovladače.                           | Synapse fond SQL doporučuje používat [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT pro Visual Studio](sql-data-warehouse-install-visual-studio.md)nebo [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) k dotazování na data. Další informace o ovladačích a připojení k Azure synapse najdete v tématu [ovladače pro Azure synapse](sql-data-warehouse-connection-strings.md) a [připojení k Azure synapse](sql-data-warehouse-connect-overview.md) articles. |

## <a name="tools"></a>Nástroje

| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Průzkumník objektů Visual studia nemá žádné uživatele Azure AD.           | Jedná se o známý problém.  Alternativním řešením je zobrazit uživatele v zobrazení [Sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Další informace o použití Azure Active Directory s fondem SQL synapse najdete v tématu [ověřování do Azure synapse](sql-data-warehouse-authentication.md) . |
| Ruční skriptování, použití Průvodce skriptováním nebo připojení prostřednictvím SSMS je pomalé, nereaguje nebo produkuje chyby. | Zajistěte, aby byl uživatel vytvořen v hlavní databázi. V možnostech skriptování se ujistěte také, že je edice stroje nastavená na Microsoft Azure synapse Analytics Edition a typ modulu je "Microsoft Azure SQL Database". |
| Generování skriptů v SSMS se nezdařilo                               | Generování skriptu pro synapse fond SQL se nepovede, pokud je možnost "vygenerovat skript pro závislé objekty" nastavená na hodnotu "true". Alternativním řešením je, že uživatelé musí ručně přejít na **nástroje-> možnosti->Průzkumník objektů systému SQL Server – > vygenerovat skript pro závislé možnosti a nastavit na hodnotu NEPRAVDA** . |

## <a name="performance"></a>Výkon

| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Řešení potíží s výkonem dotazů                            | Pokud se snažíte řešit problémy konkrétního dotazu, začněte s [učením, jak sledovat vaše dotazy](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problémy s místem v databázi TempDB | [Monitorujte](sql-data-warehouse-manage-monitor.md#monitor-tempdb) využití prostoru tempdb.  Mezi běžné příčiny, proč dochází místo v databázi TempDB, patří:<br>-Nedostatek prostředků přidělených pro dotaz způsobilo přelití dat do databáze TempDB.  Viz [Správa úloh](resource-classes-for-workload-management.md) . <br>-Statistika chybí nebo je neaktuální, což způsobuje nadměrné přesun dat.  Podrobnosti o tom, jak vytvořit statistiku, najdete v článku [udržování statistik tabulek](sql-data-warehouse-tables-statistics.md) .<br>– Pro každou úroveň služby se přiděluje prostor TempDB.  [Škálování fondu SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) na vyšší nastavení DWU přiděluje více místa v databázi tempdb.|
| Nekvalitní výkon a plány dotazů často vyplývají z důvodu chybějících statistik. | Nejběžnější příčinou špatného výkonu je nedostatek statistik v tabulkách.  Podrobnosti o tom, jak vytvořit statistiku a proč jsou důležité pro váš výkon, najdete v článku [udržování statistik tabulek](sql-data-warehouse-tables-statistics.md) . |
| Nízká souběžnost/dotazy zařazené do fronty                             | Pochopení [správy úloh](resource-classes-for-workload-management.md) je důležité, aby bylo možné pochopit, jak vyrovnávat přidělování paměti s souběžnou souběžnou. |
| Jak implementovat osvědčené postupy                              | Nejlepším způsobem, jak začít se naučit, jak zlepšit výkon dotazů, je synapse článek o [osvědčených postupech pro fond SQL](sql-data-warehouse-best-practices.md) . |
| Zvýšení výkonu pomocí škálování                      | Řešení pro zlepšení výkonu někdy znamená jednoduše přidat k vašim dotazům větší výpočetní výkon, a to tak, že [váš fond SQL škáluje](sql-data-warehouse-manage-compute-overview.md). |
| Nízký výkon dotazů v důsledku špatné kvality indexu     | Občas se můžou dotazy zpomalit kvůli [špatné kvalitě indexu columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  V tomto článku najdete další informace a postup [sestavení indexů pro zlepšení kvality segmentů](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Správa systému

| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: operaci nelze provést, protože server by překročil povolenou kvótu jednotky transakce databáze 45000. | Buď snižte [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) databáze, kterou se pokoušíte vytvořit, nebo [požádejte o zvýšení kvóty](sql-data-warehouse-get-started-create-support-ticket.md). |
| Zkoumání využití prostoru                              | Informace o využití prostoru v systému najdete v tématu [velikosti tabulek](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Pomáhat se správou tabulek                                    | Nápovědu ke správě tabulek najdete v článku [Přehled tabulky](sql-data-warehouse-tables-overview.md) .  Tento článek také obsahuje odkazy na podrobnější témata, jako jsou [typy dat tabulky](sql-data-warehouse-tables-data-types.md), [distribuce tabulky](sql-data-warehouse-tables-distribute.md), [indexování tabulky](sql-data-warehouse-tables-index.md),  [dělení tabulky](sql-data-warehouse-tables-partition.md), [Údržba statistik tabulek](sql-data-warehouse-tables-statistics.md) a [dočasné tabulky](sql-data-warehouse-tables-temporary.md). |
| Neprobíhá aktualizace indikátoru průběhu transparentního šifrování dat (TDE) v Azure Portal | Stav TDE můžete zobrazit přes [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Rozdíly od SQL Database

| Problém                                 | Řešení                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nepodporované funkce SQL Database     | Viz [nepodporované funkce tabulky](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nepodporované datové typy SQL Database   | Zobrazit [nepodporované datové typy](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| ODSTRANĚNÍ a aktualizace – omezení         | Pokud [chcete pracovat s nepodporovanou syntaxí aktualizace a odstranění](sql-data-warehouse-develop-ctas.md), přečtěte si téma [aktualizace řešení](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [odstranění alternativních řešení](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) a použití CTAS. |
| Příkaz MERGE není podporován.      | Viz [alternativní řešení sloučení](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Omezení uložených procedur          | V tématu [omezení uložených procedur](sql-data-warehouse-develop-stored-procedures.md#limitations) můžete pochopit některá omezení uložených procedur. |
| UDF nepodporují příkazy SELECT | Toto je aktuální omezení našich Udfů.  Podrobnější informace najdete v tématu věnovaném [Vytvoření funkce](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . |

## <a name="next-steps"></a>Další kroky

Další nápovědu při hledání řešení problému najdete v tématu Další zdroje, které můžete vyzkoušet.

* [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Žádosti o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
* [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
