---
title: Řešení potíží
description: Řešení potíží s Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1b841c7532e975f9a1ad928ffd0559231207fb2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350008"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Poradce při potížích se službou SQL Analytics v Azure Synapse
V tomto článku jsou uvedeny běžné otázky týkající se řešení potíží.

## <a name="connecting"></a>Připojení
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Přihlášení uživatele NT AUTHORITY\ANONYMOUS LOGON selhalo. (Microsoft SQL Server, Chyba: 18456) | K této chybě dochází, když se uživatel AAD pokusí připojit k hlavní databázi, ale nemá v hlavní databázi žádného uživatele.  Chcete-li tento problém opravit, zadejte fond SQL, ke kterému se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další informace najdete v článku [Přehled zabezpečení.](sql-data-warehouse-overview-manage-security.md) |
| Objekt zabezpečení serveru MyUserName nemůže v aktuálním kontextu zabezpečení získat přístup k databázi master. Výchozí databázi uživatele nelze otevřít. Přihlášení se nezdařilo. Nezdařilo se přihlášení pro uživatele myUserName. (Microsoft SQL Server, Chyba: 916) | K této chybě dochází, když se uživatel AAD pokusí připojit k hlavní databázi, ale nemá v hlavní databázi žádného uživatele.  Chcete-li tento problém opravit, zadejte fond SQL, ke kterému se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další informace najdete v článku [Přehled zabezpečení.](sql-data-warehouse-overview-manage-security.md) |
| Chyba CTAIP                                                  | K této chybě může dojít, pokud bylo vytvořeno přihlášení v hlavní databázi serveru SQL, ale nikoli v databázi SQL.  Pokud narazíte na tuto chybu, podívejte se na přehled [zabezpečení](sql-data-warehouse-overview-manage-security.md) článku.  Tento článek vysvětluje, jak vytvořit přihlášení a uživatele na hlavní a potom jak vytvořit uživatele v databázi SQL. |
| Blokováno bránou firewall                                          | Fondy SQL jsou chráněny bránami firewall, aby bylo zajištěno, že k databázi mají přístup pouze známé adresy IP. Brány firewall jsou ve výchozím nastavení zabezpečené, což znamená, že před připojením je nutné explicitně povolit adresu IP nebo rozsah adres.  Chcete-li nakonfigurovat bránu firewall pro přístup, postupujte podle pokynů v části [Konfigurace přístupu brány firewall serveru pro ip adresu klienta](create-data-warehouse-portal.md) v [pokynech pro zřizování](create-data-warehouse-portal.md). |
| Nelze se připojit pomocí nástroje nebo ovladače.                           | SQL Analytics doporučuje používat [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT pro Visual Studio](sql-data-warehouse-install-visual-studio.md)nebo [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) k dotazování na data. Další informace o ovladačích a připojení k Azure Synapse najdete [v tématu ovladače pro Azure Synapse](sql-data-warehouse-connection-strings.md) a [připojení k Azure Synapse](sql-data-warehouse-connect-overview.md) články. |

## <a name="tools"></a>Nástroje
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Průzkumník objektů Visual Studio postrádá uživatele AAD           | Jedná se o známý problém.  Jako zástupné řešení zobrazíte uživatele v [souboru sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Další informace o používání Služby Azure Active Directory pomocí SQL Analytics najdete v článku [Ověřování do Azure Synapse.](sql-data-warehouse-authentication.md) |
| Ruční skriptování, použití průvodce skriptováním nebo připojení pomocí ssms je pomalé, neodpovídá nebo vytváří chyby | Ujistěte se, že uživatelé byli vytvořeni v hlavní databázi. V možnostech skriptování se také ujistěte, že edice motoru je nastavena jako "Microsoft Azure SQL Data Warehouse Edition" a typ motoru je "Microsoft Azure SQL Database". |
| Generování skriptů se nezdaří v SSMS                               | Generování skriptu pro sql analytics se nezdaří, pokud je možnost "Generovat skript pro závislé objekty" nastavena na hodnotu True. Jako řešení musí uživatelé ručně přejít na **Tools -> Options ->SQL Server Object Explorer -> Generovat skript pro závislé možnosti a nastavit na hodnotu false.** |

## <a name="performance"></a>Výkon
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Poradce při potížích s výkonem dotazu                            | Pokud se pokoušíte vyřešit konkrétní dotaz, začněte s [informacemi o tom, jak sledovat dotazy](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problémy s prostorem TempDB | Monitorování využití prostoru [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Běžné příčiny vyčerpání prostoru TempDB jsou:<br>- Není dostatek prostředků přidělených dotazu, což způsobuje, že data se přelijí do databáze TempDB.  Viz [Správa pracovních vytížení](resource-classes-for-workload-management.md) <br>- Statistiky chybí nebo jsou zastaralé, což způsobuje nadměrný pohyb dat.  Podrobnosti o vytváření statistik y naleznete v [tématu Udržování statistik tabulky.](sql-data-warehouse-tables-statistics.md)<br>- Místo tempdb je přiděleno na úroveň služby.  [Škálování fondu SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) na vyšší nastavení DWU přiděluje více prostoru TempDB.|
| Nízký výkon dotazu a plány jsou často výsledkem chybějících statistik | Nejčastější příčinou nízkého výkonu je nedostatek statistik v tabulkách.  Podrobnosti o tom, jak vytvářet statistiky a proč jsou důležité pro váš výkon, najdete v tématu [Udržování statistiky tabulky.](sql-data-warehouse-tables-statistics.md) |
| Nízká souběžnost / dotazy zařazeny do fronty                             | Pochopení [správy úloh](resource-classes-for-workload-management.md) je důležité pro pochopení, jak vyvážit přidělení paměti s souběžnosti. |
| Jak implementovat osvědčené postupy                              | Nejlepším místem, kde začít se učit způsoby, jak zlepšit výkon dotazů, je článek [osvědčených postupů sql analytics.](sql-data-warehouse-best-practices.md) |
| Jak zlepšit výkon pomocí škálování                      | Někdy je řešením pro zlepšení výkonu jednoduše přidat více výpočetního výkonu do dotazů [škálováním fondu SQL](sql-data-warehouse-manage-compute-overview.md). |
| Nízký výkon dotazu v důsledku špatné kvality indexu     | Někdy dotazy mohou zpomalit z důvodu [kvality indexu špatné columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Další informace a obnovení indexů za [účelem zlepšení kvality segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)naleznete v tomto článku . |

## <a name="system-management"></a>Správa systému
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nelze provést operaci, protože server by překročil povolenou kvótu jednotky transakcí databáze 45000. | Zmenšete [dwu](what-is-a-data-warehouse-unit-dwu-cdwu.md) databáze, kterou se pokoušíte vytvořit, nebo [požádejte o zvýšení kvóty](sql-data-warehouse-get-started-create-support-ticket.md). |
| Zkoumání využití prostoru                              | Informace o využití místa v systému naleznete v tématu [Velikosti tabulek.]( ../../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) |
| Pomoc se správou tabulek                                    | Nápovědu ke správě tabulek najdete v článku [Přehled tabulky.](sql-data-warehouse-tables-overview.md)  Tento článek také obsahuje odkazy na podrobnější témata, jako [jsou datové typy tabulky](sql-data-warehouse-tables-data-types.md), Distribuce [tabulky](sql-data-warehouse-tables-distribute.md), [Indexování tabulky](sql-data-warehouse-tables-index.md), Rozdělení tabulky do [oddílů](sql-data-warehouse-tables-partition.md), [Udržování statistik tabulky](sql-data-warehouse-tables-statistics.md) a [Dočasné tabulky](sql-data-warehouse-tables-temporary.md). |
| Panel průběhu transparentního šifrování dat (TDE) se na webu Azure Portal neaktualizuje | Stav TDE můžete zobrazit pomocí [prostředí powershell .](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |


## <a name="differences-from-sql-database"></a>Rozdíly od databáze SQL
| Problém                                 | Řešení                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nepodporované funkce databáze SQL     | Viz [Nepodporované funkce tabulky](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nepodporované datové typy databáze SQL   | Viz [Nepodporované datové typy](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Omezení odstranit a aktualizovat         | Viz [UPDATE zástupná řešení](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), ODSTRANĚNÍ [zástupná řešení](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) a použití [CTAS obejít nepodporované UPDATE a DELETE syntaxe](sql-data-warehouse-develop-ctas.md). |
| Příkaz MERGE není podporován.      | Viz [sloučení řešení](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Omezení uložené procedury          | Viz [uložená procedura omezení](sql-data-warehouse-develop-stored-procedures.md#limitations) pochopit některá omezení uložené procedury. |
| UDF nepodporují příkazy SELECT | Toto je aktuální omezení našich UDFs.  Syntaxi, kterou podporujeme, naleznete v tématu [CREATE FUNCTION.](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) |

## <a name="next-steps"></a>Další kroky
Další pomoc při hledání řešení vašeho problému, zde jsou některé další zdroje, které můžete vyzkoušet.

* [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Požadavky na funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
