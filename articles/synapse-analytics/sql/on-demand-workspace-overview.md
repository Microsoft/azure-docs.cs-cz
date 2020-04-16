---
title: SQL na vyžádání (náhled)
description: Další informace o synapse SQL na vyžádání v Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424906"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL na vyžádání (preview) v Azure Synapse Analytics 

Každý pracovní prostor Azure Synapse Analytics (preview) je dodáván s koncovými body SQL na vyžádání (preview), které můžete použít k dotazování dat v jezeře.

SQL na vyžádání je dotazová služba přes data v datovém jezeře. Umožňuje přístup k vašim datům prostřednictvím následujících funkcí:
 
- Známá syntaxe T-SQL pro dotazování dat na místě bez nutnosti kopírování nebo načítání dat do specializovaného úložiště. 
- Integrovaná konektivita prostřednictvím rozhraní T-SQL, které nabízí širokou škálu business intelligence a ad-hoc dotazovacích nástrojů, včetně nejoblíbenějších ovladačů. 

SQL na vyžádání je distribuovaný systém zpracování dat, vytvořený pro velké množství dat a výpočetních prostředků. SQL na vyžádání umožňuje analyzovat vaše big data během sekund až minut, v závislosti na zatížení. Díky integrované odolnosti proti chybám při provádění dotazů poskytuje systém vysokou spolehlivost a úspěšnost i při dlouhotrvajících dotazech zahrnujících velké datové sady.

SQL na vyžádání je bez serveru, proto neexistuje žádná infrastruktura pro nastavení nebo clustery udržovat. Výchozí koncový bod pro tuto službu je k dispozici v rámci každého pracovního prostoru Azure Synapse, takže můžete začít dotazování dat, jakmile je vytvořen pracovní prostor. Za rezervované prostředky se neúčtují žádné poplatky, jsou účtovány pouze za data naskenovaná dotazy, které spustíte, a proto je tento model skutečným modelem platby za použití.  

Pokud používáte Spark ve vašem datovém kanálu, pro přípravu dat, čištění nebo obohacení, můžete [dotazovat všechny tabulky Spark,](develop-storage-files-spark-tables.md) které jste vytvořili v procesu, přímo z SQL na vyžádání. Pomocí [privátního odkazu](../security/how-to-connect-to-workspace-with-private-links.md) přenesete koncový bod SQL na vyžádání do [spravované virtuální sítě pracovního prostoru](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>Kdo je SQL na vyžádání

Pokud potřebujete prozkoumat data v datovém jezeře, získat z něj přehledy nebo optimalizovat existující kanál transformace dat, můžete využít sql na vyžádání. Je vhodný pro následující scénáře:

- Základní zjišťování a zkoumání – Rychle uvažujte o datech v různých formátech (parkety, CSV, JSON) v datovém jezeře, abyste si mohli naplánovat, jak z něj získat poznatky.
- Logický datový sklad – poskytuje relační abstrakce nad nezpracovaná nebo různorodá data bez přemístění a transformace dat, což umožňuje vždy aktuální zobrazení dat.
- Transformace dat – jednoduchý, škálovatelný a výkonný způsob transformace dat v jezeře pomocí T-SQL, takže je lze napájet do BI a dalších nástrojů nebo načíst do relačního úložiště dat (databáze Synapse SQL, Azure SQL Database atd.).

Různé profesionální role mohou těžit z SQL na vyžádání:

- Datoví inženýři mohou prozkoumat jezero, transformovat a připravit data pomocí této služby a zjednodušit jejich kanály transformace dat. Další informace naleznete v tomto [kurzu](tutorial-data-analyst.md).
- Data Vědci mohou rychle uvažovat o obsahu a struktuře dat v jezeře, a to díky funkcím, jako je OPENROWSET a automatické inference schématu.
- Analytici dat mohou [zkoumat data a tabulky Spark](develop-storage-files-spark-tables.md) vytvořené datovými vědci nebo datovými inženýry pomocí známého jazyka T-SQL nebo jejich oblíbených nástrojů, které se mohou připojit k SQL na vyžádání.
- Profesionálové bi mohou rychle [vytvářet sestavy Power BI nad data v tabulkách jezera](tutorial-connect-power-bi-desktop.md) a Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Co musím udělat, abych ji začal používat?

Koncový bod SQL na vyžádání je k dispozici v rámci každého pracovního prostoru Azure Synapse. Můžete vytvořit pracovní prostor a začít okamžitě dotazovat data pomocí nástrojů, které znáte.

## <a name="client-tools"></a>Klientské nástroje

SQL na vyžádání umožňuje existující sql ad-hoc dotazování a business intelligence nástroje pro připojení k datové jezero. Vzhledem k tomu, že poskytuje známou syntaxi T-SQL, může se každý nástroj schopný navázat na nabídky SQL připojení TDS [připojit a dotazovat se synapse SQL](connect-overview.md) na vyžádání. Můžete se spojit s Azure Data Studio a spouštět ad-hoc dotazy nebo se spojit s Power BI a získat přehled během několika minut.

## <a name="is-full-t-sql-supported"></a>Je podporováncelý T-SQL?

SQL on-demand nabízí T-SQL dotazování povrchu, který je mírně rozšířené/rozšířené v některých aspektech tak, aby vyhovovaly pro prostředí kolem dotazování částečně strukturovaných a nestrukturovaných dat. Kromě toho některé aspekty jazyka T-SQL nejsou podporovány z důvodu návrhu SQL na vyžádání, jako příklad funkce DML není aktuálně podporována.

- Pracovní vytížení lze uspořádat pomocí známých konceptů:
- Databáze – koncový bod na vyžádání sql může mít více databází.
- Schémata - V rámci databáze může existovat jedna nebo více skupin vlastnictví objektů nazývaných schémata.
- Zobrazení
- Externí zdroje – zdroje dat, formáty souborů a tabulky

Zabezpečení lze vynutit pomocí:

- Přihlášení a uživatelé
- Pověření pro řízení přístupu k účtům úložiště
- Udělení, odepření a odvolání oprávnění podle úrovně objektu
- Integrace Azure Active Directory

Podporované T-SQL:

- Je podporována celá plocha [SELECT,](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) včetně většiny funkcí SQL
- CETAS - VYTVOŘIT EXTERNÍ TABULKU JAKO VÝBĚR
- DDL příkazy týkající se pouze zobrazení a zabezpečení

SQL na vyžádání nemá žádné místní úložiště, pouze objekty metadat jsou uloženy v databázích. Proto T-SQL vztahující se k následující koncepty není podporována:

- Tabulky
- Aktivační události
- Zhmotněné pohledy
- DDL příkazy jiné než ty, které se týkají zobrazení a zabezpečení
- Příkazy DML

### <a name="extensions"></a>Rozšíření

Aby bylo možné plynule pracovat pro dotazování dat v souborech v datovém jezeře, SQL na vyžádání rozšiřuje stávající funkci [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) přidáním následujících funkcí:

[Dotaz na více souborů nebo složek](develop-storage-files-overview.md#query-multiple-files-or-folders)

[FORMÁT SOUBORU PARKET](develop-storage-files-overview.md#parquet-file-format)

[Další možnosti pro práci s odděleným textem (zakončení pole, zakončení řádku, escape char)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Čtení vybrané podmnožiny sloupců](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Odvození schématu](develop-storage-files-overview.md#schema-inference)

[funkce název souboru](develop-storage-files-overview.md#filename-function)

[funkce cesta souboru](develop-storage-files-overview.md#filepath-function)

[Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Zabezpečení

SQL na vyžádání nabízí mechanismy pro zabezpečení přístupu k vašim datům.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL na vyžádání umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu pomocí [integrace Azure Active Directory](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory (Azure AD) podporuje [vícefaktorové ověřování](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) pro zvýšení zabezpečení dat a aplikací a zároveň podporuje proces jednotného přihlášení.

#### <a name="authentication"></a>Authentication

Ověřování SQL na vyžádání odkazuje na způsob, jakým uživatelé prokázat svou identitu při připojování ke koncovému bodu. Podporovány jsou dva typy ověřování:

- **Ověřování SQL**

  Tato metoda ověřování používá uživatelské jméno a heslo.

- **Ověřování služby Azure Active Directory**:

  Tato metoda ověřování používá identity spravované službou Azure Active Directory. Pro uživatele Azure AD lze povolit vícefaktorové ověřování. [Kdykoliv to půjde](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), použijte ověřování pomocí Active Directory (integrované zabezpečení).

#### <a name="authorization"></a>Autorizace

Autorizace odkazuje na to, co může uživatel dělat v databázi na vyžádání, a je řízena členstvím v databázové roli vašeho uživatelského účtu a oprávněními na úrovni objektů.

Pokud se používá ověřování SQL, uživatel SQL existuje pouze v SQL na vyžádání a oprávnění jsou vymezeny na objekty v SQL na vyžádání. Přístup k seřiditelným objektům v jiných službách (jako je Azure Storage) nelze udělit uživateli SQL přímo, protože existuje pouze v rozsahu SQL na vyžádání. Uživatel SQL musí pro přístup k souborům použít jeden z [podporovaných typů autorizace.](develop-storage-files-storage-access-control.md#supported-storage-authorization-types)

Pokud se používá ověřování Azure AD, uživatel se může přihlásit k SQL na vyžádání a dalším službám, jako je Azure Storage, a může udělit oprávnění uživateli Azure AD.

### <a name="access-to-storage-accounts"></a>Přístup k účtům úložiště

Uživatel, který je přihlášen ke službě SQL na vyžádání, musí mít oprávnění k přístupu k souborům ve službě Azure Storage a k jejich dotazování. SQL na vyžádání podporuje následující typy autorizace:

- **Podpis sdíleného přístupu (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. S SAS můžete udělit klientům přístup k prostředkům v účtu úložiště, bez sdílení klíčů účtu. SAS poskytuje podrobnou kontrolu nad typem přístupu, který udělujete klientům, kteří mají Interval platnosti SAS, udělená oprávnění, přijatelný rozsah IP adres, přijatelný protokol (https/http).

- **Identita uživatele** (označovaná také jako "předávací") je typ autorizace, kde se identita uživatele Azure AD, který se přihlásil k SQL na vyžádání, používá k autorizaci přístupu k datům. Před přístupem k datům musí správce Azure Storage udělit oprávnění uživateli Azure AD pro přístup k datům. Tento typ autorizace používá uživatele Azure AD, který se přihlásil k SQL na vyžádání, proto není podporován pro typy uživatelů SQL.

## <a name="next-steps"></a>Další kroky
Další informace o připojení koncových bodů a dotazování souborů lze nalézt v následujících článcích: 
- [Připojení ke koncovému bodu](connect-overview.md)
- [Dotaz na soubory](develop-storage-files-overview.md)
