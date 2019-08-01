---
title: Řešení potíží s Azure SQL Data Warehouse | Microsoft Docs
description: Řešení potíží s Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 7/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 04d63b2c1583228a274c0ba21c87df08886f5cdb
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619063"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Řešení potíží s Azure SQL Data Warehouse
V tomto článku jsou uvedené běžné otázky týkající se řešení potíží.

## <a name="connecting"></a>Připojování
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Přihlášení uživatele "NT AUTHORITY\ANONYMOUS LOGON" se nezdařilo. (Microsoft SQL Server, chyba: 18456) | K této chybě dojde, když se uživatel AAD pokusí připojit k hlavní databázi, ale nemá v hlavní větvi žádného uživatele.  Chcete-li tento problém vyřešit, zadejte SQL Data Warehouse, ke kterým se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další podrobnosti najdete v článku [Přehled zabezpečení][Security overview] . |
| Objekt zabezpečení serveru "MyUserName" nemůže získat přístup k databázi "Master" v aktuálním kontextu zabezpečení. Výchozí databázi uživatele nelze otevřít. Přihlášení se nezdařilo. Přihlášení uživatele ' MyUserName ' se nezdařilo. (Microsoft SQL Server, chyba: 916) | K této chybě dojde, když se uživatel AAD pokusí připojit k hlavní databázi, ale nemá v hlavní větvi žádného uživatele.  Chcete-li tento problém vyřešit, zadejte SQL Data Warehouse, ke kterým se chcete připojit v době připojení, nebo přidejte uživatele do hlavní databáze.  Další podrobnosti najdete v článku [Přehled zabezpečení][Security overview] . |
| Chyba CTAIP                                                  | K této chybě může dojít, když bylo vytvořeno přihlášení na hlavní databázi systému SQL Server, ale ne v databázi SQL Data Warehouse.  Pokud se zobrazí tato chyba, přečtěte si článek [Přehled zabezpečení][Security overview] .  Tento článek vysvětluje, jak vytvořit přihlašovací údaje a uživatele v hlavní větvi a jak vytvořit uživatele v databázi SQL Data Warehouse. |
| Blokováno bránou firewall                                          | Databáze Azure SQL se chrání pomocí bran firewall na úrovni serveru a databáze, aby bylo zajištěno, že přístup k databázi bude mít jenom známé IP adresy. Brány firewall jsou ve výchozím nastavení zabezpečené, což znamená, že před připojením musíte explicitně povolit a IP adresu nebo rozsah adres.  Pokud chcete bránu firewall nakonfigurovat pro přístup, postupujte podle pokynů v tématu [Konfigurace přístupu k bráně firewall serveru pro IP adresu vašeho klienta][Configure server firewall access for your client IP] v pokynech k [zřizování][Provisioning instructions]. |
| Nejde se připojit pomocí nástroje nebo ovladače.                           | SQL Data Warehouse doporučuje používat [SSMS][SSMS], [SSDT pro Visual Studio][SSDT for Visual Studio]nebo [Sqlcmd][sqlcmd] k dotazování na data. Další informace o ovladačích a připojení k SQL Data Warehouse najdete v tématu [ovladače pro Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] a [připojení k Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] článkům. |

## <a name="tools"></a>Nástroje
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| V Průzkumníkovi objektů Visual studia chybí uživatelé AAD.           | Jedná se o známý problém.  Alternativním řešením je zobrazit uživatele v zobrazení [Sys. database_principals][sys.database_principals].  Další informace o použití Azure Active Directory s SQL Data Warehouse najdete v tématu [ověřování pro Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] . |
| Ruční skriptování, použití Průvodce skriptováním nebo připojení prostřednictvím SSMS je pomalé, nereaguje nebo produkuje chyby. | Zajistěte, aby byl uživatel vytvořen v hlavní databázi. V možnostech skriptování se ujistěte také, že je edice stroje nastavená na Microsoft Azure SQL Data Warehouse Edition a typ modulu je "Microsoft Azure SQL Database". |
| Generování skriptů v SSMS se nezdařilo                               | Generování skriptu pro SQL Data Warehouse se nepovede, pokud je možnost vygenerovat skript pro závislé objekty nastavená na true. Alternativním řešením je, že uživatelé musí ručně přejít na nástroje-> Možnosti-> Průzkumník objektů systému SQL Server – > vygenerovat skript pro závislé možnosti a nastavit na hodnotu NEPRAVDA. |

## <a name="performance"></a>Výkon
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Řešení potíží s výkonem dotazů                            | Pokud se snažíte řešit problémy konkrétního dotazu, začněte s [učením, jak sledovat vaše dotazy][Learning how to monitor your queries]. |
| Nekvalitní výkon a plány dotazů často vyplývají z důvodu chybějících statistik. | Nejběžnější příčinou špatného výkonu je nedostatek statistik v tabulkách.  Podrobnosti o tom, jak vytvořit statistiku a proč jsou důležité pro váš výkon, najdete v článku [udržování statistik tabulek][Statistics] . |
| Nízká souběžnost/dotazy zařazené do fronty                             | Pochopení [správy úloh][Workload management] je důležité, aby bylo možné pochopit, jak vyrovnávat přidělování paměti s souběžnou souběžnou. |
| Jak implementovat osvědčené postupy                              | Nejlepším způsobem, jak začít se naučit, jak vylepšit výkon dotazů, je SQL Data Warehouse článku o [osvědčených postupech][SQL Data Warehouse best practices] . |
| Zvýšení výkonu pomocí škálování                      | Řešení pro zlepšení výkonu někdy znamená jednoduše přidat k vašim dotazům větší výpočetní výkon, a to [škálováním SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Nízký výkon dotazů v důsledku špatné kvality indexu     | Občas se můžou dotazy zpomalit kvůli [špatné kvalitě indexu columnstore][Poor columnstore index quality].  V tomto článku najdete další informace a postup [sestavení indexů pro zlepšení kvality segmentů][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Správa systému
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Operaci nelze provést, protože server by překročil povolenou kvótu jednotky databázové transakce 45000. | Buď snižte [DWU][DWU] databáze, kterou se pokoušíte vytvořit, nebo požádejte o [zvýšení kvóty][request a quota increase]. |
| Zkoumání využití prostoru                              | Informace o využití prostoru v systému najdete v tématu [velikosti tabulek][Table sizes] . |
| Pomáhat se správou tabulek                                    | Nápovědu ke správě tabulek najdete v článku [Přehled tabulky][Overview] .  Tento článek také obsahuje odkazy na podrobnější témata, jako jsou [typy dat tabulky][Data types], [distribuce tabulky][Distribute], [indexování tabulky][Index], [dělení tabulky][Partition], [Údržba statistik tabulek][Statistics] a [dočasné tabulky][Temporary]. |
| Neprobíhá aktualizace indikátoru průběhu transparentního šifrování dat (TDE) v Azure Portal | Stav TDE můžete zobrazit přes [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Rozdíly od SQL Database
| Problém                                 | Řešení                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nepodporované funkce SQL Database     | Viz [nepodporované funkce tabulky][Unsupported table features]. |
| Nepodporované datové typy SQL Database   | Zobrazit [nepodporované datové typy][Unsupported data types].        |
| ODSTRANĚNÍ a aktualizace – omezení         | Pokud [chcete pracovat s nepodporovanou syntaxí aktualizace a odstranění][Using CTAS to work around unsupported UPDATE and DELETE syntax], přečtěte si téma [aktualizace řešení][UPDATE workarounds], [odstranění alternativních řešení][DELETE workarounds] a použití CTAS. |
| Příkaz MERGE není podporován.      | Viz [alternativní řešení sloučení][MERGE workarounds].                  |
| Omezení uložených procedur          | V tématu [omezení uložených procedur][Stored procedure limitations] můžete pochopit některá omezení uložených procedur. |
| UDF nepodporují příkazy SELECT | Toto je aktuální omezení našich Udfů.  Podrobnější informace najdete v tématu věnovaném [Vytvoření funkce][CREATE FUNCTION] . |

## <a name="next-steps"></a>Další kroky
Další nápovědu při hledání řešení problému najdete v tématu Další zdroje, které můžete vyzkoušet.

* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy týmu CAT]
* [Vytvoření lístku podpory]
* [Fórum MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Vytvoření lístku podpory]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy týmu CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
