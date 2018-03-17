---
title: "Řešení potíží s Azure SQL Data Warehouse | Microsoft Docs"
description: "Řešení potíží s datovým skladem Azure SQL."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: kevin;barbkess
ms.openlocfilehash: 0829d448e8b925d0dcc032ed143d8fff42ab1b69
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Řešení potíží s datovým skladem Azure SQL
Toto téma uvádí některé z běžnějších řešení potíží otázky, které jsme od vás odpověď od našich zákazníků.

## <a name="connecting"></a>Připojování
| Problém | Řešení |
|:--- |:--- |
| Přihlášení uživatele 'Přihlášení NT AUTHORITY\ANONYMOUS' se nezdařilo. (Microsoft SQL Server, Error: 18456) |K této chybě dojde, když uživatel s AAD pokusu o připojení k hlavní databázi, ale nemá uživatel v předloze.  Chcete-li tento problém, buď zadejte SQL Data Warehouse, které chcete připojit v době připojení nebo přidejte uživatele do hlavní databáze.  V tématu [Přehled zabezpečení] [ Security overview] další podrobnosti najdete v článku. |
| Server hlavní "uživatelské_jméno" není možné získat přístup k databázi "hlavní" v aktuálním kontextu zabezpečení. Výchozí databázi uživatele nelze otevřít. Přihlášení se nezdařilo. Přihlášení uživatele 'Uživatelské_jméno' se nezdařilo. (Microsoft SQL Server, Error: 916) |K této chybě dojde, když uživatel s AAD pokusu o připojení k hlavní databázi, ale nemá uživatel v předloze.  Chcete-li tento problém, buď zadejte SQL Data Warehouse, které chcete připojit v době připojení nebo přidejte uživatele do hlavní databáze.  V tématu [Přehled zabezpečení] [ Security overview] další podrobnosti najdete v článku. |
| Chyba CTAIP |Této chybě může dojít při přihlášení byla vytvořena v hlavní databázi SQL serveru, ale není v databázi SQL Data Warehouse.  Pokud dojde k této chybě, podívejte se na [Přehled zabezpečení] [ Security overview] článku.  Tento článek vysvětluje, jak vytvořit přihlášení a uživatele na serveru a postup vytvoření uživatele v databázi SQL Data Warehouse. |
| Blokováno bránou Firewall |Databáze Azure SQL jsou chráněny server a databáze úrovně brány firewall, aby známé pouze IP adresy, které mají přístup k databázi. Bránu firewall jsou ve výchozím nastavení, což znamená, že je potřeba explicitně povolit a IP adresu nebo rozsah adres zabezpečené před připojením.  Při konfiguraci brány firewall pro přístup, postupujte podle kroků v [konfigurovat přístup k serveru brány firewall pro vaše IP adresa klienta] [ Configure server firewall access for your client IP] v [zřizování pokyny][Provisioning instructions]. |
| Nelze se připojit s nástrojem pro nebo ovladače |SQL Data Warehouse doporučuje použít [SSMS][SSMS], [SSDT pro Visual Studio][SSDT for Visual Studio], nebo [sqlcmd] [ sqlcmd] k dotazování na data. Další informace o ovladače a připojení k SQL Data Warehouse najdete v tématu [ovladače pro Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] a [připojit k Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] články. |

## <a name="tools"></a>Nástroje
| Problém | Řešení |
|:--- |:--- |
| Průzkumník objektů systému Visual Studio chybí uživatele AAD |Jedná se o známý problém.  Jako alternativní řešení, zobrazit uživateli v [sys.database_principals][sys.database_principals].  V tématu [ověřování do Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] Další informace o používání služby Azure Active Directory s SQL Data Warehouse. |
|Ručně pomocí Průvodce skriptování, skriptování nebo připojení přes SSMS je pomalé, "zamrzlých" nebo vytváření chyb| Ujistěte se, že uživatelé byla vytvořena v hlavní databázi. V možnosti skriptování Ujistěte se také, že edice modul je nastaven jako "Microsoft Azure SQL Data Warehouse Edition" a "Microsoft Azure SQL Database" je typ modulu.|

## <a name="performance"></a>Výkon
| Problém | Řešení |
|:--- |:--- |
| Řešení potíží s výkonem dotazu |Pokud se pokoušíte vyřešit konkrétní dotaz, začínat [naučit, jak monitorovat své dotazy][Learning how to monitor your queries]. |
| Dotaz nízký výkon a plány často je výsledkem chybějící statistiky |Nejběžnější příčina nízký výkon je nedostatek statistik na tabulky.  V tématu [zachování statistiky tabulky] [ Statistics] podrobné informace o tom, jak vytvořit statistiku a proč jsou důležité pro výkon. |
| Nízkou souběžnosti / ve frontě dotazů |Principy [úlohy správy] [ Workload management] je důležité, aby bylo možné pochopit, jak se souběžností vyvážit přidělení paměti. |
| Postupy pro implementaci doporučené postupy |Je nejlepší místo k spustit další způsoby, jak zlepšit výkon dotazů [osvědčené postupy pro SQL Data Warehouse] [ SQL Data Warehouse best practices] článku. |
| Jak zvýšit výkon při změně měřítka |Někdy se řešení pro zlepšení výkonu je jednoduše přidat více výpočetního výkonu pro vaše dotazy pomocí [škálování SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Dotaz nízký výkon v důsledku nízký index kvality |Z důvodu může zpomalit některé časy dotazy [kvality indexu columnstore nízký][Poor columnstore index quality].  Najdete v tomto článku Další informace a postup [opětovné sestavení indexů ke zlepšení kvality segment][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Správa systému
| Problém | Řešení |
|:--- |:--- |
| Msg 40847: Nelze operaci provést, protože server by překročil povolenou kvótu jednotky transakcí databáze, která 45000. |Snižte [DWU] [ DWU] databáze, který se pokoušíte vytvořit nebo [požádat o zvýšení kvóty][request a quota increase]. |
| Zkoumání využití místa |V tématu [tabulky velikosti] [ Table sizes] pochopit využití místa systému. |
| Usnadnit správu tabulek |Najdete v článku [tabulky přehled] [ Overview] článku Další informace o správě tabulek.  Tento článek také obsahuje odkazy na podrobnější témata jako [tabulky datové typy][Data types], [distribuci tabulku][Distribute], [indexování tabulku][Index], [vytváření oddílů tabulky][Partition], [zachování statistiky tabulky] [ Statistics] a [dočasných tabulek][Temporary]. |
|Indikátor průběhu šifrování (TDE) transparentní dat není aktualizace na portálu Azure|Můžete zobrazit stav šifrování TDE prostřednictvím [prostředí powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>Polybase
| Problém | Řešení |
|:--- |:--- |
| Zatížení se nezdaří z důvodu velkého řádků |Podpora velké řádek aktuálně není k dispozici pro Polybase.  To znamená, že pokud tabulka obsahuje VARCHAR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX), externí tabulky nelze použít se načíst data.  Načítání velkých řádků je momentálně podporována pouze prostřednictvím Azure Data Factory (pomocí BCP), Azure Stream Analytics, SSIS, BCP nebo třídě .NET SQLBulkCopy. PolyBase podporu pro velké řádky budou přidány v budoucí verzi. |
| selhává zatížení BCP tabulky s maximální datový typ |Je známý problém, který vyžaduje, že na konci v tabulce v některých scénářích umístit VARCHAR(MAX), NVARCHAR(MAX) nebo VARBINARY(MAX).  Zkuste přesunout maximální počet sloupců na konec tabulky. |

## <a name="differences-from-sql-database"></a>Rozdíly z databáze SQL
| Problém | Řešení |
|:--- |:--- |
| Nepodporované funkce databáze SQL |V tématu [nepodporované funkce tabulky][Unsupported table features]. |
| Nepodporované typy dat databáze SQL |V tématu [nepodporované datové typy][Unsupported data types]. |
| ODSTRANĚNÍ a omezení aktualizace |V tématu [alternativní řešení aktualizace][UPDATE workarounds], [odstranění řešení] [ DELETE workarounds] a [pomocí funkce CTAS obejít nepodporovaný aktualizace a odstranění syntaxe][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Příkaz MERGE není podporován. |V tématu [SLOUČENÍ řešení][MERGE workarounds]. |
| Omezení uložené procedury |V tématu [uložené procedury omezení] [ Stored procedure limitations] pochopit některá omezení uložených procedur. |
| Funkce UDF nepodporují příkazy SELECT |Toto je aktuální omezení naše UDF.  V tématu [CREATE FUNCTION] [ CREATE FUNCTION] syntaxe podporujeme. |

## <a name="next-steps"></a>Další postup
Další pomoc při hledání řešení vašeho problému tady jsou některé prostředky, můžete to zkusit.

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
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Vytvoření lístku podpory]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy týmu CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
