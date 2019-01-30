---
title: Řešení potíží s Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Řešení potíží s Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d94d434f83e1a8507b1a98660e8169ff6be4ee24
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244577"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Řešení potíží s Azure SQL Data Warehouse
Tento článek uvádí běžné otázka Poradce při potížích.

## <a name="connecting"></a>Připojování
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Přihlášení uživatele "NT AUTHORITY\ANONYMOUS přihlásit" se nezdařilo. (Microsoft SQL Server, Error: 18456) | K této chybě dochází, když uživatel AAD pokusí připojit k hlavní databázi, ale nemá žádné uživatele v hlavní větvi.  Chcete-li tento problém vyřešit, buď zadejte SQL Data Warehouse, kterou chcete připojit v době připojení nebo přidejte uživatele k hlavní databázi.  Zobrazit [Přehled zabezpečení] [ Security overview] , kde najdete další podrobnosti. |
| Server instančního objektu "Moje_uživatelské_jméno" není přístup k databázi "master" v aktuálním kontextu zabezpečení. Výchozí databázi uživatele nelze otevřít. Přihlášení se nezdařilo. Přihlašovací jméno uživatele "Moje_uživatelské_jméno" se nezdařilo. (Microsoft SQL Server, Error: 916) | K této chybě dochází, když uživatel AAD pokusí připojit k hlavní databázi, ale nemá žádné uživatele v hlavní větvi.  Chcete-li tento problém vyřešit, buď zadejte SQL Data Warehouse, kterou chcete připojit v době připojení nebo přidejte uživatele k hlavní databázi.  Zobrazit [Přehled zabezpečení] [ Security overview] , kde najdete další podrobnosti. |
| Chyba CTAIP                                                  | Této chybě může dojít po vytvoření přihlášení v hlavní databázi systému SQL server, ale ne v databázi SQL Data Warehouse.  Pokud dojde k této chybě, podívejte se na [Přehled zabezpečení] [ Security overview] článku.  Tento článek vysvětluje, jak vytvořit přihlášení a uživatele na hlavním serveru a tom, jak vytvořit uživatele v databázi SQL Data Warehouse. |
| Blokovaná bránou Firewall                                          | Azure SQL Database jsou chráněny serveru a databáze úrovně brány firewall, aby pouze známé IP adresy, které mají přístup k databázi. Bránu firewall se ve výchozím nastavení, což znamená, že je potřeba explicitně povolit a IP adresu nebo rozsah adres zabezpečený před připojením.  Pokud chcete nakonfigurovat bránu firewall pro přístup, postupujte podle kroků v [konfigurovat přístup k serveru brány firewall pro IP adresu vašeho klienta] [ Configure server firewall access for your client IP] v [zřizování pokyny] [Provisioning instructions]. |
| Nelze se připojit pomocí nástroje nebo ovladače                           | SQL Data Warehouse se doporučuje používat [SSMS][SSMS], [SSDT pro Visual Studio][SSDT for Visual Studio], nebo [sqlcmd] [ sqlcmd] k dotazování na data. Další informace o ovladače a připojení k SQL Data Warehouse najdete v tématu [ovladače pro Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] a [připojení k Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] článků. |

## <a name="tools"></a>Nástroje
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Průzkumník objektů systému Visual Studio chybí uživatel AAD           | Jedná se o známý problém.  Jako alternativní řešení, zobrazení uživatelů v [sys.database_principals][sys.database_principals].  Zobrazit [ověřování do služby Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] Další informace o používání služby Azure Active Directory se službou SQL Data Warehouse. |
| Příručka skriptování, pomocí Průvodce skriptovací nebo připojení přes SSMS je pomalá, ukončování "zamrzlých" nebo vytváření chyb | Ujistěte se, že uživatelé byly vytvořeny v hlavní databázi. V možnosti skriptování Ujistěte se také, že edice modulu a je nastaven jako "Microsoft Azure SQL Data Warehouse edici" modul typu "Microsoft Azure SQL Database". |
| Generovat skripty selže v aplikaci SSMS                             | Generování skriptu pro SQL data warehouse se nezdaří, pokud je možnost "Vygenerovat skript pro závislé objekty" nastavena na hodnotu "True". Jako alternativní řešení, musí uživatelé ručně přejděte na Nástroje -> Možnosti -> Průzkumník objektů systému SQL Server -> Generovat skript pro závislé možnosti a nastavení na hodnotu false |

## <a name="performance"></a>Výkon
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Řešení potíží s výkonem dotazů                            | Pokud se snažíte řešit konkrétní dotaz, začněte tématem [učit, jak monitorovat vaše dotazy][Learning how to monitor your queries]. |
| Špatnému výkonu a plány, často je výsledkem chybějící statistiky | Nejběžnější příčina nízký výkon je nedostatek statistik tabulek.  Zobrazit [Správa statistik tabulek] [ Statistics] podrobné informace o tom, jak vytvořit statistiky a proč jsou důležité pro výkon. |
| Nízká souběžnosti / ve frontě dotazů                             | Principy [správu úloh] [ Workload management] je důležité, aby bylo možné pochopit, jak vyvážit přidělení paměti se souběžností. |
| Jak implementovat osvědčené postupy                              | Je nejlepší místo pro další způsoby, jak vylepšit výkon dotazů [osvědčené postupy SQL Data Warehouse] [ SQL Data Warehouse best practices] článku. |
| Jak zvýšit výkon při změně měřítka                      | Někdy řešení ke zlepšení výkonu, je jednoduše přidat další výpočetní výkon pro dotazy podle [škálování služby SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Dotaz nízký výkon v důsledku nízký index kvality     | Dotazy v některých případech může zpomalit z důvodu [kvalitu indexu columnstore nízký][Poor columnstore index quality].  Najdete v tomto článku pro další informace a postupy [provést nové sestavení indexů ke zlepšení kvality segmentů][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Správa systému
| Problém                                                        | Řešení                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Operaci nejde provést, protože server by překročil povolenou kvótu Database Transaction Unit 45000. | Snižte [DWU] [ DWU] databáze se pokoušíte vytvořit nebo [požádat o zvýšení kvóty][request a quota increase]. |
| Prozkoumat využití místa                              | Zobrazit [tabulky velikostí] [ Table sizes] porozumět využití místa systému. |
| Pomoci se správou tabulky                                    | Zobrazit [Přehled tabulek] [ Overview] článku pomoci se správou vaší tabulky.  Tento článek obsahuje také odkazy na podrobnější témata zahrnují třeba [typy tabulkových dat][Data types], [distribuce tabulky][Distribute], [Indexování tabulky][Index], [dělení tabulky][Partition], [Správa statistik tabulek] [ Statistics] a [dočasné tabulky][Temporary]. |
| Indikátor průběhu dat transparentní šifrování (TDE) není aktualizován na webu Azure Portal | Můžete zobrazit stav transparentní šifrování dat prostřednictvím [powershellu](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>Polybase
| Problém                                           | Řešení                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Zatížení se nezdaří z důvodu velkých řádků                | Podpora velký řádek aktuálně není k dispozici pro Polybase.  To znamená, že pokud tabulka obsahuje VARCHAR(MAX) či NVARCHAR(MAX) nebo VARBINARY(MAX), externí tabulky nelze načíst data.  Načítání velkých řádků je aktuálně podporuje jenom prostřednictvím Azure Data Factory (data pomocí BCP), Azure Stream Analytics, služby SSIS, BCP nebo třídy .NET SQLBulkCopy. PolyBase podpora pro velké řádky bude přidána v budoucí verzi. |
| selhání BCP zatížení tabulky s maximální datový typ | Existuje známý problém, který vyžaduje, VARCHAR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX) umístit na konci v tabulce v některých scénářích.  Zkuste maximální počet sloupců na konec tabulky. |

## <a name="differences-from-sql-database"></a>Rozdíl oproti SQL Database
| Problém                                 | Řešení                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nepodporované funkce služby SQL Database     | Zobrazit [nepodporované funkce tabulky][Unsupported table features]. |
| Nepodporované datové typy SQL Database   | Zobrazit [datové typy nepodporované][Unsupported data types].        |
| ODSTRANĚNÍ a aktualizace omezení         | Naleznete v tématu [alternativní řešení aktualizace][UPDATE workarounds], [odstranit řešení] [ DELETE workarounds] a [aktualizace nepodporované použití příkazu CTAS obejít a Odstranit syntaxe][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Příkaz MERGE se nepodporuje.      | Zobrazit [SLOUČENÍ řešení][MERGE workarounds].                  |
| Omezení uložené procedury          | Zobrazit [uložené procedury omezení] [ Stored procedure limitations] pochopit některá omezení uložené procedury. |
| UDF nepodporují příkazy SELECT | Toto je aktuální omezení naše UDF.  Zobrazit [CREATE FUNCTION] [ CREATE FUNCTION] jsme podporu syntaxe. |

## <a name="next-steps"></a>Další postup
Pro další pomoc při hledání řešení problému tady jsou některé další prostředky, můžete to zkusit.

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
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
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
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
