---
title: Pravidla vyhodnocení SQL Server pro Azure SQL Database migraci
description: Pravidla pro vyhodnocení k identifikaci problémů se zdrojovou instancí SQL Server, kterou je nutné vyřešit před migrací na Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: c407b62b5f0308fbb6d9ff7223a3554ca09592db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027206"
---
# <a name="assessment-rules-for-sql-server-to-azure-sql-database-migration"></a>Pravidla vyhodnocení SQL Server pro Azure SQL Database migraci
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Nástroje pro migraci ověřují svou zdrojovou SQL Server instanci tak, že spustí několik pravidel vyhodnocení k identifikaci problémů, které je potřeba vyřešit před migrací databáze SQL Server do Azure SQL Database. 

Tento článek poskytuje seznam pravidel používaných k vyhodnocení proveditelnosti migrace databáze SQL Server do Azure SQL Database. 


## <a name="bulk-insert"></a>Hromadné vložení<a id="BulkInsert"></a>

**Title: BULK INSERT se zdrojem dat objektů BLOB mimo Azure se v Azure SQL Database nepodporuje.**   
**Kategorie**: problém   

**Název**   
Azure SQL Database nemůže získat přístup ke sdíleným složkám souborů nebo složkám systému Windows. V části "ovlivněné objekty" najdete konkrétní použití BULK INSERTch příkazů, které neodkazují na objekt blob Azure. Objekty s ' BULK INSERT ', kde zdroj není úložiště objektů BLOB v Azure, nebudou po migraci na Azure SQL Database fungovat. 


**Základě**   
Při migraci na Azure SQL Database budete muset BULK INSERT příkazy, které používají místní soubory nebo sdílené složky, použít místo toho soubory ze služby Azure Blob Storage. Případně migrujte na SQL Server na virtuálním počítači Azure.

## <a name="compute-clause"></a>Klauzule COMPUTE<a id="ComputeClause"></a>

**Title: klauzule COMPUTE je zastavená a byla odebrána.**   
**Kategorie**: upozornění   

**Název**   
Klauzule COMPUTE generuje součty, které se zobrazí jako další souhrnné sloupce na konci sady výsledků dotazu. Tato klauzule však již není v Azure SQL Database podporována. 


**Základě**   
Modul T-SQL musí být přepsán místo toho pomocí operátoru ROLLUP. Následující kód ukazuje, jak je možné výpočetní výkon nahradit SOUHRNem: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Další informace: [ukončené funkce databázového stroje v SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Change Data Capture (CDC)<a id="CDC"></a>

**Title: funkce Change Data Capture (CDC) není v Azure SQL Database podporovaná.**   
**Kategorie**: problém   


**Název**   
Funkce Change Data Capture (CDC) není v Azure SQL Database podporována. Vyhodnoťte, zda lze místo toho použít Change Tracking.  Případně migrujte na spravovanou instanci Azure SQL nebo SQL Server na Azure Virtual Machines. 


**Základě**   
Funkce Change Data Capture (CDC) není v Azure SQL Database podporována. Vyhodnoťte, jestli se místo toho dá použít Change Tracking, nebo zvažte možnost migrace na spravovanou instanci Azure SQL.

Další informace: [Povolení sledování změn ve službě Azure SQL](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Sestavení CLR<a id="ClrAssemblies"></a>

**Title: sestavení SQL CLR nejsou v Azure SQL Database podporovaná.**   
**Kategorie**: problém   


**Název**   
Azure SQL Database nepodporuje sestavení SQL CLR. 


**Základě**   
V současné době neexistuje žádný způsob, jak toho dosáhnout v Azure SQL Database. Doporučená alternativní řešení budou vyžadovat změny kódu aplikace a databáze, aby používaly pouze sestavení podporovaná Azure SQL Database. Případně migrace do spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure

Další informace: [nepodporované rozdíly v jazyce Transact-SQL v SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Zprostředkovatel kryptografických služeb<a id="CryptographicProvider"></a>

**Title: bylo nalezeno použití zprostředkovatele KRYPTOGRAFICKÝch služeb pro vytváření nebo změnu zprostředkovatele KRYPTOGRAFICKÝch služeb, který není podporován v Azure SQL Database**   
**Kategorie**: problém   

**Název**   
Azure SQL Database nepodporuje příkazy zprostředkovatele KRYPTOGRAFICKÝch služeb, protože nemá přístup k souborům. Konkrétní použití příkazů zprostředkovatele KRYPTOGRAFICKÝch služeb najdete v části ovlivněné objekty. Objekty s `CREATE CRYPTOGRAPHIC PROVIDER` nebo `ALTER CRYPTOGRAPHIC PROVIDER` nebudou po migraci na Azure SQL Database fungovat správně.  


**Základě**   
Kontrola objektů pomocí `CREATE CRYPTOGRAPHIC PROVIDER` nebo `ALTER CRYPTOGRAPHIC PROVIDER` . U všech takových objektů, které jsou požadovány, odeberte použití těchto funkcí. Případně migrujte na SQL Server na virtuálním počítači Azure.

## <a name="cross-database-references"></a>Odkazy na různé databáze<a id="CrossDataseReferences"></a>

**Title: mezidatabázové dotazy nejsou podporované v Azure SQL Database**   
**Kategorie**: problém   

**Název**   
Databáze na tomto serveru používají mezidatabázové dotazy, které nejsou podporované v Azure SQL Database. 


**Základě**   
Azure SQL Database nepodporuje dotazy mezi databázemi. Doporučují se tyto akce: 
- Migrace závislých databází do Azure SQL Database a použití funkce Elastic Database dotazů (v současnosti ve verzi Preview) k dotazování napříč databázemi SQL Azure. 
- Přesuňte závislé datové sady z jiných databází do migrované databáze. 
- Migrujte do spravované instance Azure SQL.
- Migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [ověřte Azure SQL Database dotaz elastické databáze (Preview)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Kompatibilita databáze<a id="DbCompatLevelLowerThan100"></a>

**Název: Azure SQL Database nepodporuje úrovně kompatibility nižší než 100.**   
**Kategorie**: upozornění   

**Název**   
Úroveň kompatibility databáze je cenným nástrojem, který vám pomůže při modernizaci databází, a to díky tomu, že je možné upgradovat SQL Server databázovému stroji a zároveň udržet připojení funkčního stavu aplikace tím, že udržuje stejnou úroveň kompatibility databáze před upgradem. Azure SQL Database nepodporuje úrovně kompatibility nižší než 100. 


**Základě**   
Vyhodnoťte, jestli jsou funkce aplikace nedotčeny, když je úroveň kompatibility databáze upgradována na 100 na spravované instanci Azure SQL. Případně migrujte na SQL Server na virtuálním počítači Azure.

## <a name="database-mail"></a>Databázový e-mail<a id="DatabaseMail"></a>

**Title: Databázová pošta se v Azure SQL Database nepodporuje.**   
**Kategorie**: upozornění   

**Název**   
Tento server používá funkci Databázová pošta, která není v Azure SQL Database podporovaná.


**Základě**   
Zvažte možnost migrace do spravované instance Azure SQL, která podporuje Databázová pošta.  Případně můžete také zvážit použití funkcí Azure functions a SendGrid k tomu, abyste mohli provádět funkce pošty na Azure SQL Database.

Další informace: [odesílání e-mailů z Azure SQL Database pomocí skriptu Azure Functions](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Alias objektu zabezpečení databáze<a id="DatabasePrincipalAlias"></a>

**Title: SYS. DATABASE_PRINCIPAL_ALIASES je ukončený a byl odebraný.**   
**Kategorie**: problém   

**Název**   
Tabulce. DATABASE_PRINCIPAL_ALIASES se ukončil a v Azure SQL Database byl odstraněn.  


**Základě**   
Místo aliasů použijte role.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Možnost DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Title: možnost SET DISABLE_DEF_CNST_CHK je přerušená a byla odebrána.**   
**Kategorie**: problém   

**Název**   
Možnost nastavit DISABLE_DEF_CNST_CHK byla přerušena a byla odstraněna v Azure SQL Database.  


Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Pomocný parametr FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: pomocný parametr dotazu FASTFIRSTROW je zastavený a byl odebrán.**   
**Kategorie**: upozornění   

**Název**   
Pomocný parametr dotazu FASTFIRSTROW je zastavený a byl odstraněn v Azure SQL Database.  


**Základě**   
Místo pomocného parametru dotazu FASTFIRSTROW použijte možnost (FAST n).

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Souborem<a id="FileStream"></a>

**Title: FILESTREAM není v Azure SQL Database podporovaná.**   
**Kategorie**: problém   

**Název**   
Funkce FILESTREAM, která umožňuje ukládat nestrukturovaná data, jako jsou textové dokumenty, obrázky a videa v systému souborů NTFS, není v Azure SQL Database podporována. 


**Základě**   
Nahrajte nestrukturované soubory do úložiště objektů BLOB v Azure a uložte metadata týkající se těchto souborů (název, typ, umístění adresy URL, klíč úložiště atd.) v Azure SQL Database. Je možné, že budete muset aplikaci znovu analyzovat, aby bylo možné objekty blob streamování do a z Azure SQL Database. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [streamování objektů blob do a z blogu SQL Azure](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Odkazovaný server<a id="LinkedServer"></a>

**Title: funkce propojeného serveru není v Azure SQL Database podporovaná.**   
**Kategorie**: problém   

**Název**   
Propojené servery umožňují databázovému stroji SQL Server provádět příkazy proti OLE DB zdrojům dat mimo instanci SQL Server. 


**Základě**   
Azure SQL Database nepodporuje funkce propojeného serveru. Pro odstranění potřeby odkazovaných serverů se doporučuje následující akce: 
- Identifikujte závislé datové sady ze vzdálených serverů SQL a zvažte jejich přesun do migrované databáze. 
- Migrujte závislé databáze do Azure a použijte funkci Elastic Database dotazů (Preview) k dotazování napříč databázemi v Azure SQL Database. 

Další informace: [ověřte Azure SQL Database elastický dotaz (Preview)](../../database/elastic-query-overview.md) . 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Název: počáteční DISTRIBUOVANá transakce není v Azure SQL Database podporovaná.**   
**Kategorie**: problém   

**Název**   
Distribuovaná transakce spuštěná v Transact SQL BEGIN DISTRIBUTed TRANSACTIONed a spravovaná službou Microsoft DTC (Distributed Transaction Coordinator) (MS DTC) není v Azure SQL Database podporovaná.  


**Základě**   
Pokud chcete zobrazit všechny objekty pomocí distribuovaných transakce, přečtěte si část ovlivněné objekty v Azure Migrate. Zvažte možnost migrovat databáze účastníků do spravované instance Azure SQL, kde jsou podporované distribuované transakce napříč několika instancemi (aktuálně ve verzi Preview). Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [transakce napříč několika servery pro spravovanou instanci SQL Azure ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (hromadná)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Název: OpenRowSet použité v hromadné operaci se zdrojem dat úložiště objektů BLOB mimo Azure se v Azure SQL Database nepodporuje.**   
**Kategorie**: problém   

**Popis** OPENROWSET podporuje hromadné operace prostřednictvím integrovaného HROMADNÉho poskytovatele, který umožňuje číst data ze souboru a vracet je jako sadu řádků. V Azure SQL Database se nepodporuje OPENROWSET se zdrojem dat úložiště objektů BLOB mimo Azure.


**Základě**   
Azure SQL Database nemůže získat přístup ke sdíleným složkám souborů a složkám Windows, takže soubory musí být naimportované z úložiště objektů BLOB v Azure. Ve funkci OPENROWSET je proto podporován pouze zdroj dat typu BLOB. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (poskytovatel)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Title: v Azure SQL Database není podporována podpora OpenRowSet s SQL nebo poskytovatelem nepodporujícího SQL.**   
**Kategorie**: problém   

**Název**   
OpenRowSet s SQL nebo poskytovatelem, který není SQL, je alternativou k přístupu k tabulkám na odkazovaném serveru a jednorázovým, ad hoc způsobem připojení a přístupu ke vzdáleným datům pomocí OLE DB. V Azure SQL Database se nepodporuje OpenRowSet s poskytovatelem SQL nebo jiným poskytovatelem SQL.


**Základě**   
Azure SQL Database podporuje OPENROWSET jenom pro import ze služby Azure Blob Storage. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Levé vnější spojení jiné než ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: styl, který není ve standardu ANSI, opustil vnější spojení a byl odebrán.**   
**Kategorie**: upozornění   

**Název**   
Styl, který není ve standardu ANSI, má ukončený vnější spojení a v Azure SQL Database byl odstraněn. 


**Základě**   
Použijte syntaxi spojení ANSI.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Pravé vnější spojení, které není standardem ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: ve stylu, který není ANSI, je pravé vnější spojení ukončeno a bylo odebráno.**   
**Kategorie**: upozornění   

**Název**   
Ve stylu, který není ANSI, je pravé vnější spojení ukončeno a bylo odebráno v Azure SQL Database. 


**Základě**   
Použijte syntaxi spojení ANSI.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Další sloupec<a id="NextColumn"></a>

**Title: tabulky a sloupce s názvem NEXT budou vést k chybě v Azure SQL Database.**   
**Kategorie**: problém   

**Název**   
Byly zjištěny tabulky nebo sloupce s názvem NEXT. Sekvence, představené v Microsoft SQL Server, používají funkci standard ANSI NEXT VALUE FOR. Pokud je tabulka nebo sloupec pojmenována NEXT a sloupec má alias jako hodnotu a pokud je standard ANSI jako vynechán, výsledný příkaz může způsobit chybu.  


**Základě**   
Přepište příkazy, aby při vytváření aliasů tabulky nebo sloupce zahrnovaly klíčové slovo standard ANSI AS. Například když je sloupec pojmenovaný NEXT a tento sloupec má alias jako hodnotu, dotaz `SELECT NEXT VALUE FROM TABLE` způsobí chybu a měla by být přepsána jako možnost vybrat další jako hodnotu z tabulky. Podobně platí, že pokud je tabulka pojmenována NEXT a tato tabulka je označena jako hodnota, dotaz `SELECT Col1 FROM NEXT VALUE` způsobí chybu a měla by být přepsána jako `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: volání RAISERROR ve stylu starší verze by měla být nahrazena moderními ekvivalenty.**   
**Kategorie**: upozornění   

**Název**   
Volání RAISERROR, podobně jako v následujícím příkladu, jsou označována jako starší verze stylu, protože neobsahují čárky a závorky. `RAISERROR 50001 'this is a test'`. Tato metoda volání metody RAISERROR je přerušena a odebrána v Azure SQL Database. 


**Základě**   
Přepište příkaz pomocí aktuální syntaxe příkazu RAISERROR nebo Vyhodnoťte, jestli je možné použít moderní přístup `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` .

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Audity serveru<a id="ServerAudits"></a>

**Title: použití funkcí auditu Azure SQL Database k nahrazení auditů serveru**   
**Kategorie**: upozornění   

**Název**   
Auditování serveru není v Azure SQL Database podporováno.


**Základě**   
Zvažte Azure SQL Database funkce auditu a nahraďte audity serveru.  Azure SQL podporuje audit a funkce jsou širší než SQL Server. Azure SQL Database může auditovat různé databázové akce a události, jako jsou přístup k datům, změny schématu (DDL), změny dat (DML), účty, role a oprávnění (DCL, výjimky zabezpečení. Azure SQL Database auditování zvyšuje schopnost organizace získat podrobný přehled o událostech a změnách, ke kterým dojde v rámci své databáze, včetně aktualizací a dotazů na data. Případně migrujte do spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure.

Další informace: [auditování pro Azure SQL Database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Přihlašovací údaje serveru<a id="ServerCredentials"></a>

**Title: přihlašovací údaje rozsahu serveru nejsou podporované v Azure SQL Database.**   
**Kategorie**: upozornění   

**Název**   
Přihlašovací údaje jsou záznam, který obsahuje ověřovací informace (přihlašovací údaje) vyžadované pro připojení k prostředku mimo SQL Server. Azure SQL Database podporuje přihlašovací údaje databáze, ale ne ty, které se vytvořily v oboru SQL Server.   


**Základě**   
Azure SQL Database podporuje přihlašovací údaje v oboru databáze. Převede přihlašovací údaje oboru serveru na přihlašovací údaje v oboru databáze. Případně migrace do spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure

Další informace: [vytváření přihlašovacích údajů s rozsahem databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Title: funkce Service Broker není v Azure SQL Database podporovaná.**   
**Kategorie**: problém   

**Název**   
SQL Server Service Broker poskytuje nativní podporu pro zasílání zpráv a aplikací z fronty v databázovém stroji SQL Server. Funkce Service Broker není v Azure SQL Database podporovaná.


**Základě**   
Funkce Service Broker není v Azure SQL Database podporovaná. Zvažte možnost migrace do spravované instance Azure SQL, která podporuje službu Service Broker v rámci stejné instance. Případně migrujte na SQL Server na virtuálním počítači Azure. 

## <a name="server-scoped-triggers"></a>Aktivační události v rozsahu serveru<a id="ServerScopedTriggers"></a>

**Title: aktivační událost s rozsahem serveru není v Azure SQL Database podporovaná.**   
**Kategorie**: upozornění   

**Název**   
Trigger je zvláštní druh uložené procedury, která se spouští jako odpověď na určitou akci v tabulce, jako je vkládání, odstraňování nebo aktualizace dat. Aktivační události v oboru serveru nejsou v Azure SQL Database podporovány. Azure SQL Database nepodporuje následující možnosti pro aktivační události: pro přihlášení, šifrování, s možností připojit, ne pro replikaci, externí název (neexistuje žádná podpora externích metod), všechny možnosti serveru (TRIGGER DDL), aktivovat při události přihlášení (aktivační procedura přihlášení), Azure SQL Database nepodporuje triggery CLR.


**Základě**   
Místo toho použijte Trigger na úrovni databáze. Případně migrace do spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure

Další informace: [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Úlohy agenta SQL<a id="AgentJobs"></a>

**Název: úlohy agenta SQL Server nejsou k dispozici v Azure SQL Database**   
**Kategorie**: upozornění   

**Název**   
Agent SQL Server je služba systému Microsoft Windows, která spouští naplánované úlohy správy, které se nazývají úlohy v SQL Server. SQL Server úlohy agenta nejsou v Azure SQL Database k dispozici. 


**Doporučení** Použijte elastické úlohy (Preview), což je náhrada za úlohy agenta SQL Server v Azure SQL Database. Úlohy elastické databáze pro Azure SQL Database vám umožní spolehlivě spouštět skripty T-SQL, které pokrývají více databází při automatickém opakování a poskytování záruk pro konečné dokončení. Případně zvažte možnost migrace do spravované instance Azure SQL nebo SQL Server na Azure Virtual Machines.

Další informace: [Začínáme s úlohy elastické databáze (Preview) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Velikost SQL Database<a id="SQLDBDatabaseSize"></a>

**Title: Azure SQL Database nepodporuje velikost databáze větší než 100 TB.**   
**Kategorie**: problém   

**Název**   
Velikost databáze je větší než maximální podporovaná velikost 100 TB. 


**Základě**   
Vyhodnotit, zda mohou být data archivována nebo komprimována nebo horizontálně dělené do více databází. Případně migrujte na SQL Server na virtuálním počítači Azure. 

Další informace: [omezení prostředků Vcore](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>E-mail SQL<a id="SqlMail"></a>

**Název: e-mailová zpráva SQL byla zastavena.**   
**Kategorie**: upozornění   

**Název**   
Pošta SQL byla přerušena a odebrána v Azure SQL Database.


**Základě**   
Zvažte možnost migrace do spravované instance Azure SQL nebo SQL Server v Azure Virtual Machines a použijte Databázová pošta.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: byly zjištěny příkazy odkazující na odebrané systémové uložené procedury, které nejsou v Azure SQL Database k dispozici.**   
**Kategorie**: upozornění   

**Název**   
Následující nepodporované systémové a rozšířené uložené procedury nelze použít ve službě Azure SQL Database – `sp_dboption` , `sp_addserver` , `sp_dropalias` , `sp_activedirectory_obj` , `sp_activedirectory_scp` , `sp_activedirectory_start` .


**Základě**    
Odeberte odkazy na nepodporované systémové procedury, které byly odebrány v Azure SQL Database.

Další informace: [ukončené funkce databázového stroje v SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Příznaky trasování<a id="TraceFlags"></a>

**Title: Azure SQL Database nepodporuje příznaky trasování.**   
**Kategorie**: upozornění   

**Název**   
Příznaky trasování slouží k dočasnému nastavení specifických vlastností serveru nebo k přepnutí určitého chování. Příznaky trasování se často používají k diagnostice problémů s výkonem nebo k ladění uložených procedur nebo složitých počítačových systémů. Azure SQL Database nepodporuje příznaky trasování. 


**Základě**   
V části s ovlivněnými objekty v Azure Migrate uvidíte všechny příznaky trasování, které nejsou podporované v Azure SQL Database a vyhodnoťte, jestli je možné je odebrat. Případně migrujte do spravované instance Azure SQL, která podporuje omezený počet globálních příznaků trasování nebo SQL Server na virtuálním počítači Azure.

Další informace: [řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Ověřování systému Windows<a id="WindowsAuthentication"></a>

**Title: uživatelé databáze mapování s ověřováním systému Windows (integrované zabezpečení) nejsou v Azure SQL Database podporovány.**   
**Kategorie**: upozornění   

**Název**   
Azure SQL Database podporuje dva typy ověřování. 
- Ověřování SQL: používá uživatelské jméno a heslo. 
- Ověřování Azure Active Directory: používá identity spravované pomocí Azure Active Directory a podporuje se u spravovaných a integrovaných domén. 

Uživatelé databáze namapované s ověřováním systému Windows (integrované zabezpečení) nejsou v Azure SQL Database podporovány. 



**Základě**   
Federovat místní službu Active Directory pomocí Azure Active Directory. Identitu Windows je pak možné nahradit ekvivalentními Azure Active Directory identitami. Případně migrujte na SQL Server na virtuálním počítači Azure.

Další informace: [SQL Database možnosti zabezpečení](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell se v Azure SQL Database nepodporuje.**   
**Kategorie**: problém   

**Název**   
xp_cmdshell, který vytvoří příkazové prostředí Windows a předá řetězec ke spuštění, se v Azure SQL Database nepodporuje. 


**Základě**   
Pokud chcete zobrazit všechny objekty pomocí xp_cmdshell a vyhodnotit, jestli je možné odebrat odkaz na xp_cmdshell nebo ovlivněný objekt, přečtěte si část ovlivněné objekty v Azure Migrate. Zvažte také prozkoumání Azure Automation, které poskytují cloudovou službu pro automatizaci a konfiguraci. Případně migrujte na SQL Server na virtuálním počítači Azure. 

## <a name="next-steps"></a>Další kroky

Pokud chcete zahájit migraci SQL Server do Azure SQL Database, přečtěte si [Průvodce migrací SQL Server na SQL Database](sql-server-to-sql-database-guide.md).

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o SQL Database najdete v těchto tématech:
   - [Přehled Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).
