---
title: SQL na vyžádání (Preview)
description: Přečtěte si o synapse SQL na vyžádání v Azure synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: dc47bf73f39d73861c166674a692932d51064e6d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91288524"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL na vyžádání (Preview) ve službě Azure synapse Analytics 

Každý pracovní prostor Azure synapse Analytics (Preview) se dodává s koncovými body SQL na vyžádání (Preview), které můžete použít k dotazování dat v Lake.

SQL na vyžádání je dotazovací služba na základě dat ve službě Data Lake. Umožňuje přístup k datům prostřednictvím následujících funkcí:
 
- Známá syntaxe T-SQL pro dotazování na data, aniž byste museli kopírovat nebo načítat data do specializovaného úložiště. 
- Integrované připojení prostřednictvím rozhraní T-SQL, které nabízí rozsáhlou škálu business intelligence a nástrojů pro dotazování ad-hoc, včetně nejoblíbenějších ovladačů. 

SQL na vyžádání je distribuovaný systém zpracování dat, který je sestavený pro velké objemy dat a výpočetní funkce. SQL na vyžádání umožňuje analyzovat velké objemy dat v řádu sekund v závislosti na zatížení. Díky integrovanému zajištění odolnosti proti chybám při provádění dotazů poskytuje systém vysokou spolehlivost a úspěšnost i pro dlouhotrvající dotazy zahrnující velké datové sady.

SQL na vyžádání je bez serveru, proto není k dispozici žádná infrastruktura pro instalaci ani clustery. Výchozí koncový bod pro tuto službu je k dispozici v rámci každého pracovního prostoru Azure synapse, takže můžete začít zadávat dotazy na data hned po vytvoření pracovního prostoru. 

Za rezervované prostředky se neúčtují žádné poplatky, účtují se vám jenom data zpracovávaná dotazy, a to proto, že tento model představuje skutečný model plateb za použití.  

Pokud používáte Apache Spark pro Azure synapse v datovém kanálu, pro přípravu, čištění nebo obohacení dat, můžete [zadávat dotazy na externí tabulky Spark](develop-storage-files-spark-tables.md) , které jste v procesu vytvořili, přímo z SQL na vyžádání. Pomocí [privátního odkazu](../security/how-to-connect-to-workspace-with-private-links.md) přeneste koncový bod SQL na vyžádání do vaší [virtuální sítě spravovaného pracovního prostoru](../security/synapse-workspace-managed-vnet.md).  

## <a name="sql-on-demand-benefits"></a>Výhody SQL na vyžádání

Pokud potřebujete prozkoumat data v Data Lake, získat z nich přehledy nebo optimalizovat stávající kanál pro transformaci dat, můžete využívat výhod SQL na vyžádání. Je vhodný pro následující scénáře:

- Základní zjišťování a průzkum – v datech Lake se rychle pokusíte o data v různých formátech (Parquet, CSV, JSON), abyste si mohli naplánovat, jak z nich získat přehledy.
- Logický datový sklad – poskytuje relační abstrakci nad nezpracovanými nebo různorodými daty bez nutnosti přemístění a transformaci dat, což umožňuje vždy aktuální zobrazení dat.
- Transformace dat – jednoduchý, škálovatelný a vykonávající způsob, jak transformovat data v Lake pomocí T-SQL, takže se dají doplňovat do BI a dalších nástrojů nebo načíst do relačního úložiště dat (databáze SQL synapse, Azure SQL Database atd.).

Z SQL na vyžádání můžou využívat různé role Professional:

- Data technici můžou Prozkoumat Lake, transformovat a připravit data pomocí této služby a zjednodušit jejich kanály pro transformaci dat. Další informace najdete v tomto [kurzu](tutorial-data-analyst.md).
- Vědečtí data mohou rychle vypříčinit obsah a strukturu dat v Lake, a to díky funkcím, jako je například OPENROWSET a automatické odvození schématu.
- Datové Analytiké můžou [Prozkoumat data a Spark externí tabulky](develop-storage-files-spark-tables.md) vytvořené odborníky přes data nebo pomocí známých nástrojů T-SQL nebo jejich oblíbených nástrojů, které se můžou připojit k SQL na vyžádání.
- Profesionálové v BI můžou rychle [vytvářet Power BI sestavy nad daty v tabulkách Lake](tutorial-connect-power-bi-desktop.md) a Spark.

## <a name="how-to-start-using-sql-on-demand"></a>Jak začít používat SQL na vyžádání

Koncový bod SQL na vyžádání je k dispozici v rámci každého pracovního prostoru Azure synapse. Můžete vytvořit pracovní prostor a spustit dotazování na data okamžitě pomocí nástrojů, které znáte.

## <a name="client-tools"></a>Nástroje klienta

SQL na vyžádání umožňuje existujícím nástrojům pro dotazování ad-hoc a business intelligenceům, aby mohli na data Lake klepnout. Vzhledem k tomu, že poskytuje známou syntaxi T-SQL, se může každý nástroj schopný vytvořit nabídky SQL připojení TDS, [připojit k SQL na vyžádání a dotazovat se na synapse](connect-overview.md) . Můžete se připojit pomocí Azure Data Studio a spustit ad-hoc dotazy nebo se připojit pomocí Power BI a získat přehledy během několika minut.

## <a name="t-sql-support"></a>Podpora T-SQL

SQL na vyžádání nabízí oblast pro dotazování T-SQL, která je mírně rozšířená nebo rozšířená v některých aspektech, aby se zajistila možnost vyhledávání částečně strukturovaných a nestrukturovaných dat. Kromě toho některé aspekty jazyka T-SQL nejsou podporované z důvodu návrhu SQL na vyžádání, jako je například funkce DML není v současné době podporovaná.

- Úlohy je možné organizovat pomocí známých konceptů:
- Databáze – koncový bod SQL na vyžádání může mít více databází.
- Schémata – v rámci databáze může existovat jedna nebo mnoho skupin vlastnictví objektů s názvem schemas.
- Zobrazení
- Externí prostředky – zdroje dat, formáty souborů a tabulky

Zabezpečení je možné vyhovět pomocí:

- Přihlášení a uživatelé
- Přihlašovací údaje pro řízení přístupu k účtům úložiště
- Udělení, zamítnutí a odvolání oprávnění na úrovni objektu
- Integrace Azure Active Directory

Podporované T-SQL:

- Plocha kompletního [výběru](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) je podporovaná, včetně většiny funkcí SQL.
- CETAS – VYTVOŘIT EXTERNÍ TABULKU JAKO SELECT
- Příkazy DDL související s pohledy a zabezpečením

SQL na vyžádání nemá žádné místní úložiště, ukládají se do databází jenom objekty metadat. Proto se T-SQL, který souvisí s následujícími koncepty, nepodporuje:

- Tabulky
- Aktivační události
- Materializovaná zobrazení
- Příkazy DDL kromě těch, které souvisejí s zobrazeními a zabezpečením
- Příkazy DML

### <a name="extensions"></a>Rozšíření

Aby bylo možné povolit hladké prostředí pro účely zadávání dotazů na data umístěná v souborech v Data Lake, rozšiřuje SQL na vyžádání existující funkci [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) přidáním následujících možností:

[Dotazování na více souborů nebo složek](query-data-storage.md#query-multiple-files-or-folders)

[Formát souboru PARQUET](query-data-storage.md#query-parquet-files)

[Další možnosti pro práci s odděleným textem (ukončovací znak pole, ukončovací znak řádku, řídicí znak)](query-data-storage.md#query-csv-files)

[Čtení zvolené podmnožiny sloupců](query-data-storage.md#read-a-chosen-subset-of-columns)

[Odvození schématu](query-data-storage.md#schema-inference)

[filename – funkce](query-data-storage.md#filename-function)

[FilePath – funkce](query-data-storage.md#filepath-function)

[Práce se složitými typy a vnořenými nebo opakovanými datovými strukturami](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Zabezpečení

SQL na vyžádání nabízí mechanismy pro zabezpečení přístupu k datům.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL na vyžádání umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu pomocí [Azure Active Directory Integration](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory (Azure AD) podporuje [vícefaktorové ověřování](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) pro zvýšení zabezpečení dat a aplikací a současně podporuje proces jednotného přihlašování.

#### <a name="authentication"></a>Authentication

Ověřování SQL na vyžádání odkazuje na to, jak uživatelé při připojení ke koncovému bodu prokáže svoji identitu. Podporují se dva typy ověřování:

- **Ověřování SQL**

  Tato metoda ověřování používá uživatelské jméno a heslo.

- **Ověřování Azure Active Directory**:

  Tato metoda ověřování používá identity spravované pomocí Azure Active Directory. Pro uživatele Azure AD můžete povolit službu Multi-Factor Authentication. [Kdykoliv to půjde](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), použijte ověřování pomocí Active Directory (integrované zabezpečení).

#### <a name="authorization"></a>Autorizace

Autorizace odkazuje na to, co uživatel může provádět v databázi SQL na vyžádání, a je řízen členstvím databázové role vašeho uživatelského účtu a oprávněními na úrovni objektů.

Pokud se používá ověřování SQL, uživatel SQL existuje jenom v SQL na vyžádání a oprávnění jsou vymezená na objekty v SQL na vyžádání. Přístup k zabezpečeným objektům v jiných službách (například Azure Storage) se nedá udělit přímo uživateli SQL, protože existuje jenom v rozsahu SQL na vyžádání. Uživatel SQL musí pro přístup k souborům použít jeden z [podporovaných typů autorizace](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) .

Pokud se používá ověřování Azure AD, může se uživatel přihlásit k SQL na vyžádání a dalším službám, jako je Azure Storage, a může udělit oprávnění k uživateli Azure AD.

### <a name="access-to-storage-accounts"></a>Přístup k účtům úložiště

Uživatel, který je přihlášen ke službě SQL na vyžádání, musí mít oprávnění pro přístup k souborům v Azure Storage a dotazování na ně. SQL na vyžádání podporuje následující typy autorizace:

- **Sdílený přístupový podpis (SAS)** poskytuje delegovaný přístup k prostředkům v účtu úložiště. Pomocí SAS můžete klientům udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. SAS vám poskytne podrobnější kontrolu nad typem přístupu, který udělíte klientům, kteří mají přidružení zabezpečení: Interval platnosti, udělená oprávnění, přijatelný rozsah IP adres, přijatelný protokol (HTTPS/HTTP).

- **Identita uživatele** (označovaná také jako "průchozí") je typ autorizace, ve kterém se k autorizaci přístupu k datům používá identita uživatele služby Azure AD, který se přihlásil k SQL na vyžádání. Před přístupem k datům musí Azure Storage správce udělit uživateli Azure AD oprávnění k přístupu k datům. Tento typ autorizace používá uživatele Azure AD, který se přihlásil k SQL na vyžádání, proto není podporován pro uživatelské typy SQL.

## <a name="next-steps"></a>Další kroky
Další informace o připojení koncových bodů a dotazování souborů najdete v následujících článcích: 
- [Připojení ke koncovému bodu](connect-overview.md)
- [Dotazování souborů](develop-storage-files-overview.md)
