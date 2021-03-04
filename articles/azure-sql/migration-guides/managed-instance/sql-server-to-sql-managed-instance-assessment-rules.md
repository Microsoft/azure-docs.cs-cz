---
title: Pravidla vyhodnocení pro SQL Server migraci spravované instance SQL
description: Pravidla pro vyhodnocení k identifikaci problémů se zdrojovou instancí SQL Server, která se musí řešit před migrací do spravované instance Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054571"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Pravidla vyhodnocení pro SQL Server migraci spravované instance SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Nástroje pro migraci ověřují vaši zdrojovou instanci SQL Server spuštěním několika pravidel pro vyhodnocení a identifikují problémy, které je potřeba vyřešit před migrací databáze SQL Server do spravované instance Azure SQL. 

Tento článek poskytuje seznam pravidel používaných k vyhodnocení proveditelnosti migrace databáze SQL Server do spravované instance Azure SQL. 

## <a name="analysiscommand-job"></a>Úloha AnalysisCommand<a id="AnalysisCommandJob"></a>

**Title: krok AnalysisCommand úlohy není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: upozornění   


**Název**   
Jedná se o krok úlohy, který spouští příkaz Analysis Services. Krok úlohy AnalysisCommand není ve spravované instanci SQL Azure podporován.

**Základě**     
V části s ovlivněnými objekty v Azure Migrate můžete zobrazit všechny úlohy pomocí kroku úloha služby Analysis Service a vyhodnotit, jestli je možné odebrat krok úlohy nebo ovlivněný objekt. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Úloha AnalysisQuery<a id="AnalysisQueryJob"></a>

**Title: krok AnalysisQuery úlohy není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: upozornění   

**Název**   
Jedná se o krok úlohy, který spustí dotaz Analysis Services. Krok úlohy AnalysisQuery není ve spravované instanci SQL Azure podporován.


**Základě**   
V části ovlivněné objekty v Azure Migrate můžete zobrazit všechny úlohy pomocí kroku úloha dotazu služby Analysis Service a vyhodnotit, jestli je možné odebrat krok úlohy nebo ovlivněný objekt. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Sestavení ze souboru<a id="AssemblyFromFile"></a>

**Title: možnost vytvořit sestavení a změnit sestavení s parametrem souboru není ve spravované instanci SQL Azure podporována.**   
**Kategorie**: upozornění   

**Název**   
Spravovaná instance Azure SQL nemůže přistupovat ke sdíleným složkám souborů nebo složkám systému Windows. V části "ovlivněné objekty" najdete konkrétní použití BULK INSERTch příkazů, které neodkazují na objekt blob Azure. Objekty s ' BULK INSERT ', kde zdroj není úložiště objektů BLOB v Azure, nebudou po migraci do spravované instance Azure SQL fungovat.


**Základě**   
Při migraci na spravovanou instanci SQL Azure budete muset převést příkazy BULK INSERT, které používají místní soubory nebo sdílené složky pro místo toho použití souborů z Azure Blob Storage. Případně migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [rozdíly CLR ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Hromadné vložení (Bulk insert)<a id="BulkInsert"></a>

**Title: BULK INSERT se zdrojem dat objektu BLOB mimo Azure není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: problém   

**Název**   
Spravovaná instance Azure SQL nemůže přistupovat ke sdíleným složkám souborů nebo složkám systému Windows. V části "ovlivněné objekty" najdete konkrétní použití BULK INSERTch příkazů, které neodkazují na objekt blob Azure. Objekty s ' BULK INSERT ', kde zdroj není úložiště objektů BLOB v Azure, nebudou po migraci do spravované instance Azure SQL fungovat.


**Základě**   
Při migraci na spravovanou instanci SQL Azure budete muset převést příkazy BULK INSERT, které používají místní soubory nebo sdílené složky pro místo toho použití souborů z Azure Blob Storage.

Další informace: [rozdíly v hromadném vložení a OPENROWSET ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Zabezpečení CLR<a id="ClrStrictSecurity"></a>

**Title: sestavení CLR označená jako bezpečná nebo EXTERNAL_ACCESS se považují za nebezpečná.**   
**Kategorie**: problém   

**Název**   
Ve spravované instanci Azure SQL se vynutil striktní režim zabezpečení CLR. Tento režim je ve výchozím nastavení povolený a zavádí zásadní změny pro databáze obsahující uživatelsky definovaná sestavení CLR označená buď jako bezpečné, nebo EXTERNAL_ACCESS.


**Základě**   
Modul CLR používá zabezpečení přístupu kódu (CAS) v .NET Framework, který již není podporován jako hranice zabezpečení. Počínaje databázovým strojem SQL Server 2017 (14. x) se `sp_configure` zavádí možnost s názvem CLR Strict Security, která zvyšuje zabezpečení sestavení CLR. Striktní zabezpečení CLR je ve výchozím nastavení povoleno a jsou zpracovávána ZABEZPEČENá a EXTERNAL_ACCESS sestavení CLR, jako kdyby byla označena jako nebezpečná. Pokud je striktní zabezpečení CLR zakázané, sestavení CLR vytvořené pomocí PERMISSION_SET = bezpečné může být schopné získat přístup k prostředkům externích prostředků, volat nespravovaný kód a získat oprávnění sysadmin. Po povolení striktního zabezpečení se nezdaří načtení všech sestavení, která nejsou podepsána. Kromě toho, pokud má databáze bezpečná nebo EXTERNAL_ACCESS sestavení, lze příkazy Restore nebo ATTACH DATABASE dokončit, ale sestavení se nemusí zdařit načíst. Chcete-li načíst sestavení, je nutné buď změnit nebo vyřadit a znovu vytvořit každé sestavení, aby byla podepsána certifikátem nebo asymetrickým klíčem, který má odpovídající přihlašovací údaje s oprávněním nebezpečného sestavení na serveru.

Další informace: [CLR Strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Klauzule COMPUTE<a id="ComputeClause"></a>

**Title: klauzule COMPUTE je zastavená a byla odebrána.**   
**Kategorie**: upozornění   

**Název**   
Klauzule COMPUTE generuje součty, které se zobrazí jako další souhrnné sloupce na konci sady výsledků dotazu. Tato klauzule ale už není ve spravované instanci SQL Azure podporovaná.



**Základě**   
Modul T-SQL musí být přepsán místo toho pomocí operátoru ROLLUP. Následující kód ukazuje, jak je možné výpočetní výkon nahradit SOUHRNem: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Zprostředkovatel kryptografických služeb<a id="CryptographicProvider"></a>

**Title: bylo nalezeno použití zprostředkovatele KRYPTOGRAFICKÝch služeb pro vytváření nebo změnu zprostředkovatele KRYPTOGRAFICKÝch služeb, který není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: problém   

**Název**   
Spravovaná instance Azure SQL nepodporuje příkazy zprostředkovatele KRYPTOGRAFICKÝch služeb, protože nemá přístup k souborům. Konkrétní použití příkazů zprostředkovatele KRYPTOGRAFICKÝch služeb najdete v části ovlivněné objekty. Objekty s možnost vytvořit zprostředkovatele KRYPTOGRAFICKÝch služeb nebo změnit zprostředkovatele KRYPTOGRAFICKÝch služeb nebudou po migraci do spravované instance Azure SQL fungovat správně.


**Základě**   
Projděte si objekty pomocí příkazu "vytvořit zprostředkovatele KRYPTOGRAFICKÝch služeb" nebo "ALTER CRYPTOGRAPHIC PROVIDER". U všech takových objektů, které jsou požadovány, odeberte použití těchto funkcí. Případně migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [rozdíly v zprostředkovateli kryptografických služeb ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Kompatibilita databáze<a id="DbCompatLevelLowerThan100"></a>

**Title: úroveň kompatibility databáze nižší než 100 není podporovaná.**   
**Kategorie**: upozornění   

**Název**   
Úroveň kompatibility databáze je cenným nástrojem, který vám pomůže při modernizaci databází, a to díky tomu, že je možné upgradovat SQL Server databázovému stroji a zároveň udržet připojení funkčního stavu aplikace tím, že udržuje stejnou úroveň kompatibility databáze před upgradem. Spravovaná instance Azure SQL nepodporuje úrovně kompatibility nižší než 100. Když se v Azure SQL Managed instance obnoví databáze s úrovní kompatibility nižší než 100, úroveň kompatibility se upgraduje na 100. 


**Doporučení**... Vyhodnoťte, jestli jsou funkce aplikace nedotčeny, když je úroveň kompatibility databáze upgradována na 100 na spravované instanci Azure SQL. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [Podporované úrovně kompatibility ve spravované instanci SQL Azure ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias objektu zabezpečení databáze<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES je ukončený a byl odebraný.**   
**Kategorie**: problém   

**Název**   
Tabulce. DATABASE_PRINCIPAL_ALIASES se ukončil a v Azure SQL Managed instance se odebralo.


**Základě**   
Místo aliasů použijte role.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Možnost DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Title: možnost SET DISABLE_DEF_CNST_CHK je přerušená a byla odebrána.**   
**Kategorie**: problém   

**Název**   
Možnost nastavit DISABLE_DEF_CNST_CHK byla přerušena a byla odebrána ve spravované instanci Azure SQL.


Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Pomocný parametr FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: pomocný parametr dotazu FASTFIRSTROW je zastavený a byl odebrán.**   
**Kategorie**: upozornění   

**Název**   
Pomocný parametr dotazu FASTFIRSTROW je zastavený a byl odebraný ve spravované instanci Azure SQL.


**Základě**   
Místo pomocného parametru dotazu FASTFIRSTROW použijte možnost (FAST n).

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Souborem<a id="FileStream"></a>

**Title: FileStream a FileTable se v Azure SQL Managed instance nepodporují.**   
**Kategorie**: problém   

**Název**   
Funkce FILESTREAM, která umožňuje ukládat nestrukturovaná data, jako jsou textové dokumenty, obrázky a videa v systému souborů NTFS, není ve spravované instanci SQL Azure podporováno. **Tuto databázi nejde migrovat, protože ve spravované instanci SQL Azure nejde obnovit zálohu, která obsahuje skupiny souborů FILESTREAM.**


**Základě**   
Nahrajte nestrukturované soubory do úložiště objektů BLOB v Azure a uložte metadata týkající se těchto souborů (název, typ, umístění adresy URL, klíč úložiště atd.) ve spravované instanci Azure SQL. Je možné, že budete muset aplikaci znovu analyzovat, aby se povolilo streamování objektů blob do a ze spravované instance Azure SQL. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [streamování objektů blob do a z SQL Azure blogu](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogenní koordinátor MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Title: na spravované instanci Azure SQL se nepovedlo spustit DISTRIBUOVANou transakci s jiným než SQL Server vzdáleným serverem.**   
**Kategorie**: problém   

**Název**   
Distribuovaná transakce spuštěná v Transact SQL BEGIN DISTRIBUTed TRANSACTIONed a spravovaná službou Microsoft DTC (Distributed Transaction Coordinator) (MS DTC) není ve spravované instanci SQL Azure podporovaná, pokud vzdálený server není SQL Server. 


**Základě**   
Pokud chcete zobrazit všechny objekty pomocí distribuovaných transakce, přečtěte si část ovlivněné objekty v Azure Migrate. Zvažte možnost migrovat databáze účastníků do spravované instance Azure SQL, kde jsou podporované distribuované transakce napříč několika instancemi (aktuálně ve verzi Preview). Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [transakce napříč několika servery pro spravovanou instanci SQL Azure ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Homogenní koordinátor MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Title: začátek DISTRIBUOVANÉ transakce je podporován napříč více servery pro spravovanou instanci Azure SQL.**   
**Kategorie**: problém   

**Název**   
Distribuovaná transakce spuštěná v Transact SQL začátek DISTRIBUOVANÉ transakce a spravovaná službou Microsoft DTC (Distributed Transaction Coordinator) (MS DTC) je podporovaná na více serverech pro spravovanou instanci Azure SQL.


**Základě**   
Pokud chcete zobrazit všechny objekty pomocí distribuovaných transakce, přečtěte si část ovlivněné objekty v Azure Migrate. Zvažte možnost migrovat databáze účastníků do spravované instance Azure SQL, kde jsou podporované distribuované transakce napříč několika instancemi (aktuálně ve verzi Preview). Případně migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [transakce napříč několika servery pro spravovanou instanci SQL Azure](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Odkazovaný server (poskytovatel jiného typu než SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Title: odkazovaný server s poskytovatelem bez SQL Server není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: problém   

**Název**   
Propojené servery umožňují databázovému stroji SQL Server provádět příkazy proti OLE DB zdrojům dat mimo instanci SQL Server. Odkazovaný server s poskytovatelem bez SQL Server není ve spravované instanci SQL Azure podporován. 


**Základě**   
Spravovaná instance Azure SQL nepodporuje funkci propojeného serveru, pokud je zprostředkovatel vzdáleného serveru neSQL Server jako Oracle, Sybase atd. 

Pro odstranění potřeby odkazovaných serverů se doporučuje následující akce: 
- Identifikujte závislé databáze ze vzdálených serverů mimo SQL Server a zvažte jejich přesun do migrované databáze. 
- Migrujte závislé databáze na podporované cíle, jako je například spravovaná instance SQL, SQL Database, Azure synapse SQL a SQL Server instance. 
- Zvažte vytvoření propojeného serveru mezi spravovanou instancí Azure SQL a SQL Server na virtuálním počítači Azure (SQL VM).  Pak z virtuálního počítače SQL vytvořte odkazovaný server pro Oracle, Sybase atd. Tento přístup zahrnuje dva segmenty směrování, ale dá se použít jako dočasné řešení.  
- Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [rozdíly v propojených serverech ve spravované instanci Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Úloha sloučení<a id="MergeJob"></a>

**Title: krok úlohy sloučení není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: upozornění   

**Název**   
Je to krok úlohy, který aktivuje agenta sloučení replikace. Agent sloučení replikace je spustitelný soubor nástroje, který používá počáteční snímek uložený v databázových tabulkách pro předplatitele. Slučuje také přírůstkové změny dat, k nimž došlo na vydavateli po vytvoření počátečního snímku, a slučuje konflikty buď podle pravidel, která nakonfigurujete, nebo pomocí vlastního překladače, který vytvoříte. Krok úlohy sloučení není ve spravované instanci SQL Azure podporován.


**Základě**   
V části ovlivněné objekty v Azure Migrate se zobrazí všechny úlohy pomocí kroku sloučit úlohu a vyhodnotí, jestli je možné odebrat krok úlohy nebo ovlivněný objekt. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Velikost databáze MI<a id="MIDatabaseSize<"></a>

**Název: spravovaná instance Azure SQL nepodporuje velikost databáze větší než 8 TB.**   
**Kategorie**: problém   

**Název**   
Velikost databáze je větší než maximální rezervované úložiště instance. **Tuto databázi nelze vybrat pro migraci, protože velikost překročila povolený limit.**


**Základě**   
Vyhodnotit, jestli se data dají archivovat komprimované nebo horizontálně dělené do více databází. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [charakteristiky generování hardwaru spravované instance Azure SQL ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Velikost instance MI<a id="MIInstanceSize<"></a>

**Title: maximální velikost úložiště instancí ve spravované instanci SQL Azure nemůže být větší než 8 TB.**   
**Kategorie**: upozornění   

**Název**   
Velikost všech databází je větší než maximální rezervované úložiště instance.  


**Základě**   
Zvažte možnost migrace databází do různých spravovaných instancí Azure SQL nebo SQL Server na virtuálním počítači Azure, pokud všechny databáze musí existovat ve stejné instanci. 

Další informace: [charakteristiky generování hardwaru spravované instance Azure SQL ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Více souborů protokolu<a id="MultipleLogFiles<"></a>

**Název: spravovaná instance Azure SQL nepodporuje více souborů protokolu.**   
**Kategorie**: problém   

**Název**   
SQL Server umožňuje databázi protokolovat do více souborů. Tato databáze obsahuje několik souborů protokolu, které se ve spravované instanci Azure SQL nepodporují. * * Tuto databázi nejde migrovat, protože záloha se nedá obnovit ve spravované instanci Azure SQL. 
**

**Základě**   
Spravovaná instance Azure SQL podporuje pro každou databázi jenom jeden protokol. Před migrací této databáze do Azure je nutné odstranit všechny soubory protokolu kromě jednoho. 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Další informace: [nepodporované možnosti databáze ve spravované instanci Azure SQL  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Další sloupec<a id="NextColumn"></a>

**Title: tabulky a sloupce s názvem NEXT budou vést k chybě ve spravované instanci Azure SQL.**   
**Kategorie**: problém   

**Název**   
Byly zjištěny tabulky nebo sloupce s názvem NEXT. Sekvence, představené v Microsoft SQL Server, používají funkci standard ANSI NEXT VALUE FOR. Pokud je tabulka nebo sloupec pojmenována NEXT a sloupec má alias jako hodnotu a pokud je standard ANSI jako vynechán, výsledný příkaz může způsobit chybu.


**Základě**   
Přepište příkazy, aby při vytváření aliasů tabulky nebo sloupce zahrnovaly klíčové slovo standard ANSI AS. Například když je sloupec pojmenovaný NEXT a tento sloupec má alias jako hodnotu, dotaz vybrat další hodnotu z tabulky způsobí chybu a měla by se přepsat, protože jako hodnota z tabulky vyberte Další. Podobně pokud je tabulka pojmenována NEXT a tato tabulka je jako hodnota nastavena jako hodnota, dotaz SELECT Sloupec1 z další hodnoty způsobí chybu a měla by být přepsána jako vybrat Sloupec1 z hodnoty NEXT AS.



## <a name="non-ansi-style-left-outer-join"></a>Styl jiný než ANSI: levé vnější spojení<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: styl, který není ve standardu ANSI, opustil vnější spojení a byl odebrán.**   
**Kategorie**: upozornění   

**Název**   
Styl, který není ve standardu ANSI, je vypnutý a byl odebrán ve spravované instanci Azure SQL. 


**Základě**   
Použijte syntaxi spojení ANSI.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Styl bez kódování ANSI – pravé vnější spojení<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: ve stylu, který není ANSI, je pravé vnější spojení ukončeno a bylo odebráno.**   
**Kategorie**: upozornění   

**Název**   
Ve stylu, který není ANSI, je pravé vnější spojení ukončeno a bylo odebráno ve spravované instanci Azure SQL. 



Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Základě**   
Použijte syntaxi spojení ANSI.

## <a name="databases-exceed-100"></a>Databáze překračují 100 <a id="NumDbExceeds100"></a>

**Název: spravovaná instance Azure SQL podporuje maximálně 100 databází na jednu instanci.**   
**Kategorie**: upozornění   

**Název**   
Maximální počet databází podporovaný ve spravované instanci Azure SQL je 100, pokud nebylo dosaženo limitu velikosti úložiště instancí.



**Základě**   
Zvažte možnost migrace databází do různých spravovaných instancí Azure SQL nebo SQL Server na virtuálním počítači Azure, pokud všechny databáze musí existovat ve stejné instanci. 

Další informace: [omezení prostředků spravované instance Azure SQL ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (neobjektový zdroj dat)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Název: OpenRowSet použité v hromadné operaci se zdrojem dat úložiště objektů BLOB mimo Azure není ve spravované instanci SQL Azure podporováno.**   
**Kategorie**: problém   

**Název**   
OPENROWSET podporuje hromadné operace prostřednictvím integrovaného HROMADNÉho poskytovatele, který umožňuje číst data ze souboru a vracet je jako sadu řádků. V Azure SQL Managed instance se nepodporuje OPENROWSET se zdrojem dat úložiště objektů BLOB mimo Azure. 



**Základě**   
Funkce OPENROWSET se dá použít ke spouštění dotazů jenom u instancí SQL Server (buď spravovaných, místních, nebo v Virtual Machines). Jako zprostředkovatel se podporují jenom hodnoty SQLNCLI, SQLNCLI11 a SQLOLEDB. Proto je akce doporučení určena k identifikaci závislých databází ze vzdálených serverů mimo SQL Server a zvažte jejich přesun do migrované databáze. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [rozdíly v hromadném vložení a OPENROWSET ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (poskytovatel jiného typu než SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Název: OpenRowSet s poskytovatelem, který není SQL, není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: problém   

**Název**   
Tato metoda je alternativou k přístupu k tabulkám na odkazovaném serveru a jednorázovou metodou pro připojení a přístup ke vzdáleným datům pomocí OLE DB. OpenRowSet s jiným poskytovatelem než SQL není ve spravované instanci SQL Azure podporováno. 



**Základě**   
Funkce OPENROWSET se dá použít ke spouštění dotazů jenom u instancí SQL Server (buď spravovaných, místních, nebo v Virtual Machines). Jako zprostředkovatel se podporují jenom hodnoty SQLNCLI, SQLNCLI11 a SQLOLEDB. Proto je akce doporučení určena k identifikaci závislých databází ze vzdálených serverů mimo SQL Server a zvažte jejich přesun do migrované databáze.

Další informace: [rozdíly v hromadném vložení a OPENROWSET ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Úloha PowerShellu<a id="PowerShellJob"></a>

**Title: krok úlohy PowerShellu není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: upozornění   

**Název**   
Jedná se o krok úlohy, který spouští PowerShellový skript. Krok úlohy PowerShellu není ve spravované instanci SQL Azure podporován. 



**Základě**   
Pokud chcete zobrazit všechny úlohy pomocí kroku úlohy PowerShellu a vyhodnotit, jestli je možné odebrat krok úlohy nebo ovlivněný objekt, přečtěte si část ovlivněné objekty v Azure Migrate.  Vyhodnotí, zda lze použít Azure Automation. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Úloha čtečky fronty<a id="QueueReaderJob"></a>

**Title: krok úlohy čtečky fronty není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: upozornění   

**Název**   
Je to krok úlohy, který aktivuje agenta čtečky replikační fronty. Agent čtečky fronty replikace je spustitelný soubor, který čte zprávy uložené ve frontě Microsoft SQL Server nebo frontě zpráv společnosti Microsoft a následně tyto zprávy aplikuje vydavateli. Agent čtečky fronty se používá se snímky a transakčními publikacemi, které umožňují aktualizace ve frontě. Krok úlohy čtečky fronty není ve spravované instanci SQL Azure podporován.


**Základě**   
V části ovlivněné objekty v Azure Migrate se zobrazí všechny úlohy s využitím kroku úlohy čtečky fronty a vyhodnotí, jestli je možné odebrat krok úlohy nebo ovlivněný objekt. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: volání RAISERROR ve stylu starší verze by měla být nahrazena moderními ekvivalenty.**   
**Kategorie**: upozornění   

**Název**   
Volání RAISERROR, podobně jako v následujícím příkladu, jsou označována jako starší verze stylu, protože neobsahují čárky a závorky. RAISERROR 50001 ' Toto je test '. Tato metoda volání metody RAISERROR je přerušená a odebrána ve spravované instanci Azure SQL.



**Základě**   
Přepište příkaz pomocí aktuální syntaxe příkazu RAISERROR nebo Vyhodnoťte, jestli je možné použít moderní přístup `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` .

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Služba Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Title: funkce Service Broker je částečně podporovaná ve spravované instanci Azure SQL.**   
**Kategorie**: problém   

**Název**   
SQL Server Service Broker poskytuje nativní podporu pro zasílání zpráv a aplikací z fronty v databázovém stroji SQL Server. Tato databáze má povolený Service Broker mezi instancemi, což není ve spravované instanci SQL Azure podporováno. 


**Základě**   
Spravovaná instance Azure SQL nepodporuje více instancí služby Service Broker, tj. kde adresa není místní. Před migrací této databáze do Azure je nutné zakázat Service Broker pomocí následujícího příkazu: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Kromě toho může být také nutné odebrat nebo zastavit koncový bod Service Broker, aby bylo možné zabránit doručení zpráv do instance SQL. Jakmile se databáze migruje do Azure, můžete vyhledat obecný cloudový systém zasílání zpráv místo Service Broker a implementovat tak Azure Service Bus funkcionalitu. Případně migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [Service Broker rozdíly ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>E-mail SQL<a id="SqlMail"></a>

**Název: e-mailová zpráva SQL byla zastavena.**   
**Kategorie**: upozornění   


**Název**   
V Azure SQL Managed instance se přestaly používat poštu SQL mailem a odebrali jste ji.



**Základě**   
Použijte Databázová pošta.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: byly zjištěny příkazy odkazující na odebrané systémové uložené procedury, které nejsou ve spravované instanci Azure SQL k dispozici.**   
**Kategorie**: upozornění   

**Název**   
Následující nepodporované systémové a rozšířené uložené procedury nelze použít ve spravované instanci Azure SQL,,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` a `sp_activedirectory_start` . 




**Základě**   
Odeberte odkazy na nepodporované systémové procedury, které byly odebrány ve spravované instanci Azure SQL.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Úloha Transact-SQL<a id="TransactSqlJob"></a>

**Title: krok TSQL úlohy zahrnuje nepodporované příkazy ve spravované instanci Azure SQL.**   
**Kategorie**: upozornění   


**Název**   
Jedná se o krok úlohy, který spouští skripty TSQL v naplánovaném čase. Krok úlohy TSQL zahrnuje nepodporované příkazy, které se ve spravované instanci Azure SQL nepodporují.



**Základě**   
V části s ovlivněnými objekty v Azure Migrate můžete zobrazit všechny úlohy, které zahrnují nepodporované příkazy ve spravované instanci Azure SQL, a vyhodnotit, jestli je možné krok úlohy nebo ovlivněný objekt odebrat. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Server rozdíly v agentech ve spravované instanci Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Příznaky trasování<a id="TraceFlags"></a>

**Title: ve spravované instanci Azure SQL se našly příznaky trasování, které se nepodporují.**   
**Kategorie**: upozornění   


**Název**   
Spravovaná instance Azure SQL podporuje jenom omezený počet globálních příznaků trasování. Příznaky trasování relace nejsou podporovány.



**Základě**   
V části s ovlivněnými objekty v Azure Migrate se zobrazí všechny příznaky trasování, které nejsou podporované ve spravované instanci Azure SQL, a vyhodnoťte, jestli je možné je odebrat. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [příznaky trasování](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Ověřování systému Windows<a id="WindowsAuthentication"></a>

**Title: uživatelé databáze mapování s ověřováním systému Windows (integrované zabezpečení) nejsou ve spravované instanci Azure SQL podporované.**   
**Kategorie**: upozornění   


**Název**   
Spravovaná instance Azure SQL podporuje dva typy ověřování: 
- Ověřování SQL, které používá uživatelské jméno a heslo
- Ověřování pomocí Azure Active Directory, které používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. 

Uživatelé databáze mapování s ověřováním systému Windows (integrované zabezpečení) nejsou ve spravované instanci Azure SQL podporované. 


**Základě**   
Federovat místní službu Active Directory pomocí Azure Active Directory. Identitu Windows je pak možné nahradit ekvivalentními Azure Active Directory identitami. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [Možnosti zabezpečení spravované instance SQL](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell není ve spravované instanci SQL Azure podporován.**   
**Kategorie**: problém   


**Název**   
Xp_cmdshell, který vytvoří příkazové prostředí systému Windows a předá řetězec ke spuštění, není ve spravované instanci SQL Azure podporován. 



**Základě**   
Pokud chcete zobrazit všechny objekty pomocí xp_cmdshell a vyhodnotit, jestli je možné odebrat odkaz na xp_cmdshell nebo ovlivněný objekt, přečtěte si část ovlivněné objekty v Azure Migrate. Zvažte prozkoumání Azure Automation, která poskytuje cloudovou službu pro automatizaci a konfiguraci. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [rozdíly v uložených procedurách ve spravované instanci SQL Azure](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít migrovat SQL Server do spravované instance Azure SQL, přečtěte si [Průvodce migrací SQL Server na SQL Managed instance](sql-server-to-managed-instance-guide.md).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o spravované instanci Azure SQL najdete tady:
   - [Úrovně služeb ve spravované instanci Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Rozdíly mezi SQL Server a spravovanou instancí Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).