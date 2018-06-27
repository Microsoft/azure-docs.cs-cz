---
title: Co je nového v Azure Data Catalog | Microsoft Docs
description: Tento článek obsahuje přehled nových funkcí, které jsou přidány do Azure Data Catalog.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 780ab56df19e8d6761fcf88fa7fbed3e8002fbe8
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025683"
---
# <a name="whats-new-in-azure-data-catalog"></a>Co je nového v Azure Data Catalog
Aktualizace **Azure Data Catalog** jsou vydávány pravidelně. Ne každý verze obsahuje nové funkce zobrazující se uživatelům, jako některých vydání se zaměřuje na funkce back endové službě. Tato stránka označuje nové funkce uživatelsky orientovaný přidán do služby Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Co je nového pro listopad 2017 
Následující funkce jsou od listopadu 2017 přidané do Azure Data Catalog:

* Podpora pro propojování přímo na konkrétní obchodní Glosář podmínky na portálu katalogu Data Catalog. Uživatele můžete zkopírovat odkazy z obchodní Glosář a vložit do dokumentů, e-mailů, sestavy nebo jiné umístění pro přímý odkaz na definici Glosář termínů.
* Podpora pro objekty služby Azure Active Directory. Správci katalogu dat může autorizovat klientským aplikacím pomocí objekty služby přístup ke katalogu a můžete udělit tyto aplikace konkrétní oprávnění stejně jako se můžete udělit oprávnění pro uživatele a skupiny zabezpečení. Další informace najdete v článku [aplikace a služby hlavní objekty ve službě Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Podpora pro ověřování Azure Active Directory při připojování k databázi SQL Azure a Azure SQL Data Warehouse zdroje dat pomocí nástroj registrace zdroje dat katalogu Data Catalog. Další informace najdete v článku [použití Azure ověřování služby Active Directory k ověřování připojení k SQL Database nebo SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Co je nového u září 2017 
Od září 2017 následující funkce jsou přidané do Azure Data Catalog:

* Podpora pro extrahování připojení relace metadata ze zdroje dat DB2 při registraci pomocí nástroj registrace zdroje dat tabulky v relaci.
* Podpora pro registraci zdroje dat 3,4 verze MongoDB pomocí nástroj registrace zdroje dat.
* Podpora pro odstranění všech metadat pro objekty obsažené v rámci jedné operace při odebírání databáze nebo jiných kontejneru z katalogu Data Catalog.
* Podpora pro zobrazení až 1 000 značky, obchodní Glosář podmínky nebo jiná hledání omezující, když upřesnění vyhledávání v portálu katalogu Data Catalog.


## <a name="whats-new-for-august-2017"></a>Co je nového u 2017 srpen 
Následující funkce jsou od srpna 2017 přidané do Azure Data Catalog:

*   Nových vzorků vývojáře je k dispozici pro vytváření a správu vztah metadat pomocí Data Catalog REST API. *Importovat informace o vztahu do katalogu Data Catalog* ukázka je dostupná na [stránce ukázek kódu katalogu Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Podpora pro extrahování připojení metadata relace ze zdrojů dat Teradata při registraci pomocí nástroj registrace zdroje dat tabulky v relaci.
* Podpora pro SQL Server funkci vracející tabulku (TVF) objekty při registraci zdroje dat systému SQL Server pomocí nástroj registrace zdroje dat.
* Více aktualizací a obecnější zvýšit výkonnost a použitelnost portálu katalogu Data Catalog.

## <a name="whats-new-for-july-2017"></a>Co je nového u července 2017 
Od července 2017 následující funkce jsou přidané do Azure Data Catalog:
*   Podpora pro podrobnější řízení činnosti rozhodného metadat, včetně:
    - Správci katalogu můžete omezit schopnosti uživatelů přispívat značky a související metadata do katalogu, povolení přístupu jen pro čtení do katalogu.
    - Katalog správci můžou omezit schopnosti uživatelů k registraci nového zdroje dat v katalogu.
    - Katalog správci můžou omezit schopnosti uživatelů k převzetí vlastnictví data asset metadata v katalogu.
    - Oprávnění lze udělit skupiny zabezpečení Azure Active Directory a uživatelů pro snadnou správu oprávnění.
* Podpora pro vztahy mezi registrovaných datových prostředků a zjišťování prostředků souvisejících dat v portálu katalogu Data Catalog, včetně:
    - Extrakce metadat relace ze serveru SQL Server (včetně Azure SQL Database), Oracle a MySQL zdroje dat, pokud používáte nástroj registrace zdroje dat katalogu Data Catalog.
    - Zjišťování prostředků souvisejících dat při prohlížení metadatech prostředku na portálu katalogu Data Catalog.
    - Operace, které chcete definovat, zjišťovat a spravovat vztahy mezi datové prostředky pomocí Data Catalog REST API.

Další podrobnosti týkající se správy oprávnění v katalogu Data Catalog najdete v tématu [jak zabezpečit přístup do katalogu data catalog a datových prostředků](data-catalog-how-to-secure-catalog.md).
Další informace o vztahy v katalogu Data Catalog najdete v tématu [postup zobrazení souvisejících dat prostředky v Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Co je nového u června 2017 
Od června 2017 následující funkce jsou přidané do Azure Data Catalog:
*   Podpora pro zdroje dat databáze Sybase, Apache Cassandra a MongoDB. Uživatelé teď můžete registrovat a zjišťovat Cassandra a MongoDB databáze a tabulky a databáze Sybase, tabulky a zobrazení.

> [!NOTE]
> Při registraci MongoDB a Cassandra tabulky, které zahrnují sloupce s komplexními datovými typy, jako je například záznamy a pole, v těchto sloupcích nebudou zahrnuty v metadatech přidaných do katalogu Data.

## <a name="whats-new-for-may-2017"></a>Co je nového u může 2017 
Následující funkce jsou od může 2017 přidané do Azure Data Catalog:
*   Nových vzorků vývojáře je k dispozici pro hromadný import obchodní Glosář termínů. Glosář hromadným importem ukázka je dostupná na [katalogu Data Catalog vývojáře ukázky stránky](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Podpora pro úpravy informace o připojení ODBC v portálu Azure Data Catalog. Vlastníci asset data a Data Catalog správci nyní můžete upravit informace o připojení pro registrované zdroje dat ODBC bez nutnosti znovu registrace zdrojů dat.
*   Podpora pro prokliknutelný adresy URL v obchodní Glosář definice a popisy. Adresy URL jsou zahrnutá ve vlastnostech definice a popis pro obchodní Glosář podmínky se adresy URL se zobrazí jako hypertextové odkazy na portálu katalogu Data Catalog.
*   Podpora pro zobrazení odborné názvy kromě odborné e-mailové adresy. Při zobrazení odborníky ve vlastnostech pro datový prostředek portálu katalogu Data Catalog, zobrazí se v popisek odborníka úplný název ze služby Azure Active Directory.

## <a name="whats-new-for-april-2017"></a>Co je nového u dubna 2017 
Následující funkce jsou od dubna 2017 přidané do Azure Data Catalog:
*   Podpora pro zdroje dat ODBC. Uživatelé teď můžete registrovat a zjišťovat databáze rozhraní ODBC, tabulky a zobrazení pomocí nástroj registrace zdroje dat katalogu Data Catalog.

## <a name="whats-new-for-march-2017"></a>Co je nového u března 2017 
Následující funkce jsou od března 2017 přidané do Azure Data Catalog:
*   Podpora pro použití skupin zabezpečení AAD správcům katalogu Data Catalog.
*   Azure Data Catalog je k dispozici v nové oblasti Azure. Zákazníci tak můžou zřizovat Azure Data Catalog v oblasti v – Západ střední USA, kromě Východ USA, západ USA, západní Evropa a Austrálie – východ, Severní Evropa a jihovýchodní Asie. Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Co je nového u února 2017 
Od února 2017 následující funkce jsou přidané do Azure Data Catalog:
*   Podpora pro pokročilé nastavení v nástroj registrace zdroje dat katalogu Data Catalog. Při registraci zdroje velkého množství dat, mohou uživatelé zadat příkaz hodnoty časového limitu.
*   Podpora pro FTP a PostgreSQL datové zdroje. Uživatelé teď můžete registrovat a zjišťovat FTP soubory a adresáře a databáze PostgreSQL, tabulky a zobrazení.

## <a name="whats-new-for-january-2017"></a>Co je nového u ledna 2017 
Následující funkce jsou od ledna 2017 přidané do Azure Data Catalog:
*   Azure Data Catalog je nyní [CSA HVĚZDIČKY](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) kompatibilní.
*   Integrace s [získat & transformaci v aplikaci Excel 2016 a Power Query pro Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Aplikace Excel uživatelé mohli sdílet dotazy a dotazy pomocí Azure Data Catalog ze zjišťování v aplikaci Excel. Tato funkce je k dispozici pro uživatelé s licencí pro Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Co je nového pro prosinec 2016
Od prosince 2016 jsou následující možnosti přidané do Azure Data Catalog:
*   Azure Data Catalog je nyní [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) a [modelové doložky EU](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) kompatibilní.
*   Podpora pro úpravy informace o připojení zdroje dat. Vlastníci asset data a Data Catalog správci nyní můžete upravit informace o připojení zdroje dat registrované bez nutnosti znovu registrace zdrojů dat.
*   Podpora pro zdroje dat Salesforce.com. Uživatelé teď můžete registrovat a zjišťovat objekty služby Salesforce.


## <a name="whats-new-for-november-2016"></a>Co je nového pro listopadu 2016
Od listopadu 2016 jsou následující možnosti přidané do Azure Data Catalog:
*   Azure Data Catalog je nyní [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) a [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) kompatibilní.
*   Podpora pro ruční registraci zdroje dat ODBC pomocí portálu pro katalog Data Catalog a REST API.

## <a name="whats-new-for-september-2016"></a>Co je nového u září 2016
Od září 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Podpora pro zdroje dat IBM DB2. Uživatelé teď můžete registrovat a zjišťovat databází DB2, tabulky a zobrazení.
* Podpora pro Azure Cosmos DB datové zdroje. Uživatelé teď můžete registrovat a zjišťovat Cosmos DB databáze a kolekce.
* Podpora pro přizpůsobení název katalogu Data Catalog portálu. Teď umožňuje správcům katalogu zobrazit text, který se zobrazí v nadpis portálu, jako je například název organizace.

## <a name="whats-new-for-august-2016"></a>Co je nového u srpna 2016
Od srpna 2016 následující funkce jsou přidané do Azure Data Catalog:

* Vylepšení pro registraci zdroje dat SQL Server Master Data Services (MDS). Uživatelé mohou nyní zahrnují profily verze Preview a data při registraci služby MDS entity používat nástroj registrace zdroje dat katalogu Data Catalog.
* Podpora pro definované správcem organizační uložená hledání. Při ukládání vyhledávání v portálu katalogu Data Catalog, teď můžete Data Catalog správci zvolit uložit hledání pro osobní použití nebo pro všechny uživatele katalogu. Organizační uložená hledání jsou sdíleny se všemi uživateli katalogu a může poskytnout standardizované počáteční body pro zjišťování zdroje dat.
* Aktualizace vlastnosti zobrazení na portálu katalogu Data Catalog. Všechny vlastnosti datového zdroje dat se nyní zobrazují a spravovat v jednom, s možností změny velikosti podokně poskytnout více konzistentní a zjistitelný prostředí.

## <a name="whats-new-for-july-2016"></a>Co je nového pro července 2016
Od července 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Podpora pro zdroje dat SQL Server Master Data Services (MDS). Uživatelé teď můžete registrovat a zjišťovat MDS modelů a entit.
* Podpora pro uložené procedury SQL serveru. Uživatelé teď můžete registrovat a zjišťovat objekty uložené procedury v datové zdroje systému SQL Server.
* Podpora dalších jazyků na portálu Azure Data Catalog nástroj registrace zdroje dat pro celkem 18 podporované jazyky. Činnost koncového uživatele Azure Data Catalog je lokalizované podle jazykové předvolby, nastavte v systému Windows nebo ve webovém prohlížeči.
* Aktualizace a vylepšení pro katalog Data Catalog domovské stránky portálu, včetně vylepšení výkonu a efektivní uživatelské prostředí.

## <a name="whats-new-for-june-2016"></a>Novinky z června 2016
Od června 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Podpora pro změnu velikosti sloupce v zobrazení seznamu při zjišťování datových prostředků na portálu katalogu Data Catalog. Uživatelé nyní změnit velikost snadněji číst metadata zdlouhavé asset například značky a popisy jednotlivých sloupců.
* Power Query pro Excel přidala do nabídky "Open in" v portálu katalogu Data Catalog. Uživatelé nyní lze otevřít podporovaných zdrojů dat, v aplikaci Excel 2016 nebo v aplikaci Excel 2010 a Excel 2013 s [Power Query pro Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) nainstalovaným doplňkem.
* Podpora pro zdroje dat úložiště tabulek Azure. Uživatelé teď můžete registrovat a zjišťovat objekty tabulky v zdrojů dat úložiště Azure.

## <a name="whats-new-for-may-2016"></a>Co je nového pro května 2016
Od může 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Obchodní glosář, který umožňuje správcům katalogu definovat firemní podmínky a hierarchií pro vytvoření běžných termínů firmy. Uživatele můžete označit registrované datové prostředky pomocí slovníku pojmů, aby bylo snazší vyhledatelné a pochopitelné obsah katalogu. Další informace najdete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md)  
* Vylepšení Data katalogu obchodní glosář, který umožňuje uživatelům aktualizovat více slovníku pojmů v rámci jedné operace. Uživatelé mohou vybrat více podmínek upravit následující pole:
  * Nadřazené termín: Kterého uživatel může vybrat novou nadřazenou termín a všechny vybrané podmínky se aktualizují jako podřízené objekty vybrané nadřazené podmínek. Pokud vybraný podmínky všechny mají stejnou nadřazenou položku, pak tento nadřazený zobrazen v textovému poli, jinak nadřazený termín je nastaveno na prázdný.   
  * Značky a zúčastněnými stranami: Uživatelé můžete přidávat a odebírat značky a zúčastněnými stranami pro více slovníku pojmů pomocí stejné prostředí jako označování více datových prostředků.

> [!NOTE]
> Obchodní Glosář je k dispozici pouze v Azure Data Catalog, Standard Edition. Edice Free nenabízí možnosti pro upraveny označování nebo obchodní Glosář.

## <a name="whats-new-for-march-2016"></a>Co je nového u března 2016
Od. března 2016 následující funkce byly přidány do Azure Data Catalog: g:

* Konsolidované endpoint REST API k programovému přístupu k možnosti vyhledávání a možnosti správy katalogu asset služby Azure Data Catalog. Tento koncový bod hledání koncový bod a katalog API rozhraní API byla zastaralá a zastaví 21. března 2016. Neexistují žádné změny sémantice rozhraní API. Změnit URI pouze koncový bod. Další informace najdete v tématu [referenční dokumentace rozhraní API Azure Data Catalog REST](https://msdn.microsoft.com/library/azure/mt267595.aspx). Ukázky rozhraní API naleznete v části [ukázky pro vývojáře Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Co je nového u února 2016
Od února 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Výběr zdroje dat nově přepracovanou služeb na základě zkušeností nástroj registrace zdroje dat Azure Data Catalog. Nástroj registrace zdroje dat se aktualizovalo, aby bylo snazší vyhledat a vybrat ze zdroje dat nepodporuje Azure Data Catalog.
* Podpora dalších 10 jazyků na portálu Azure Data Catalog nástroj registrace zdroje dat. Kromě angličtiny je teď prostředí Azure Data Catalog dostupná v němčina, španělština, francouzština, italština, japonština, korejština, brazilská portugalština, ruština, zjednodušená čínština a tradiční čínštině. Činnost koncového uživatele Azure Data Catalog je lokalizované podle jazykové předvolby, nastavte v systému Windows nebo ve webovém prohlížeči uživatele.
* Podpora pro geografická replikace dat Azure Data Catalog pro obchodní kontinuitu a zotavení po havárii. Veškerý obsah v Azure Data Catalog, včetně metadata a crowdsourcovaná poznámky zdroje dat se teď replikují mezi dvěma oblastmi Azure pro zákazníky bez dalších poplatků. Jsou předem spárovat, od sebe, 500 miles a postupujte podle mapování, jak je popsáno v oblastech Azure [obchodní kontinuitu a zotavení po havárii (BCDR): spárovat oblasti Azure](../best-practices-availability-paired-regions.md).
* Podpora pro změnu předplatné Azure použité pomocí Azure Data Catalog. Azure Data Catalog mohou správci stránce nastavení na portálu Azure Data Catalog a vyberte jiné předplatné Azure pro účely fakturace.

## <a name="whats-new-for-january-2016"></a>Co je nového pro leden 2016
Od ledna 2016 jsou následující možnosti přidané do Azure Data Catalog:

* Podpora pro ruční registraci dalších zdrojů dat. Teď můžete použít "Vytvořit ruční položku" na portálu Azure Data Catalog nebo Azure Data Catalog REST API použijte k registraci následující zdroje dat:
  * OData - funkce sady entit a kontejneru entit
  * HTTP – soubor, koncový bod, sestavy a lokality
  * Systém souborů – soubor
  * SharePoint – seznam
  * FTP – souborů a adresářů
  * Salesforce.com – objekt
  * DB2 - tabulky, zobrazení a databáze
  * PostgreSQL - tabulky, zobrazení a databáze
* Podpora pro "Otevřít v SQL Server Data Tools" zdroje dat serveru SQL Server (včetně databáze SQL Azure a Azure SQL Data Warehouse).  
* Podpora pro registraci a zjišťování SAP HANA zobrazení a balíčků. Můžete zaregistrovat SAP HANA zdroje dat pomocí Azure Data Catalog zdroje dat nástroj pro registraci a můžete opatřit poznámkami a zjistit registrované SAP HANA zdroje dat pomocí portálu Azure Data Catalog.
* Možnost připnout a Odepnout datových prostředků na portálu Azure Data Catalog. Můžete připnout datových prostředků, aby se usnadnilo opakované zjišťování a znovu použít.
* Nově přepracovanou domovskou stránku na portálu Azure Data Catalog. Nová domovská stránka obsahuje přehled o aktuální aktivita uživatele – včetně nedávno publikovaných prostředky, definovaného prostředky a uložená hledání - a přehled o aktivity v katalogu jako celek.
* Podpora pro trvalé uživatelských nastavení na portálu Azure Data Catalog. -Včetně zobrazení mřížky nebo dlaždice, počet výsledků na stránku a stiskněte klávesu zvýraznění zapnout nebo vypnout - nastavení činnosti koncového uživatele jsou nastavené jako trvalé mezi uživatelských relací.
* Azure Data Catalog je teď dostupná v dva nové oblastech Azure. Zákazníci tak můžou zřizovat Azure Data Catalog v oblastech severní Evropa a jihovýchodní Asie, kromě Východ USA, západ USA, západní Evropa a Austrálie – východ. Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Open in SQL Server Data Tools" vyžaduje Visual Studio 2013 s aktualizacemi Update 4 a nástrojů SQL Server k instalaci. Chcete-li nainstalovat nejnovější verzi SQL Server Data Tools, navštivte [stáhnout SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Co je nového pro prosinec 2015
Následující funkce jsou od prosince 2015 přidané do Azure Data Catalog:

* Podpora pro profily data pro zdroje dat Azure SQL Data Warehouse. Při registraci Azure SQL Data Warehouse tabulek a zobrazení, uživatelé mohou obsahovat data profilu metriky s metadat extrahovaných ze zdroje dat.
* Podpora pro registraci a zjišťování objektů MySQL a databází. Uživatelé mohou registrovat MySQL zdroje dat pomocí Azure Data Catalog zdroje dat nástroj pro registraci a můžete opatřit poznámkami a zjistit registrované MySQL zdroje dat pomocí portálu Azure Data Catalog.
* Podpora ověřování SPNEGO a systému Windows pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou připojit k Teradata pomocí SPNEGO a systému Windows a LDAP a TD2 ověřování.
* Podpora pro zdroje dat Azure Data Lake Store. Uživatelé teď můžete registrovat a zjišťovat zdroje dat Azure Data Lake Store pomocí Azure Data Catalog.
* Podpora pro ruční zadání nastavení proxy sítě v nástroji pro registraci zdroje dat Azure Data Catalog. Uživatelé "Upravit nastavení proxy serveru" můžete vybrat z úvodní stránka nástroje a můžete zadat adresu proxy serveru a port, který se použije nástroj.

## <a name="whats-new-for-november-2015"></a>Co je nového pro listopad 2015
Od listopadu 2015 následující funkce jsou přidané do Azure Data Catalog:

* Možnost zobrazení a zkopírujte připojovací řetězce z portálu Azure Data Catalog pro SQL Server (včetně Azure SQL Database) a zdroje dat Oracle. Uživatele můžete kliknout na odkaz "Zobrazit připojovací řetězce" v informace o připojení pro SQL Server nebo Oracle tabulka, zobrazení nebo databáze, pokud chcete zobrazit připojovací řetězce používaná k připojení ke zdroji dat. ADO.NET, rozhraní ODBC, OLEDB a JDBC připojovací řetězce jsou k dispozici pro datové zdroje systému SQL Server. ODBC a OLEDB připojovací řetězce jsou uvedené pro zdroje dat Oracle.
* Podpora včetně profily dat při registraci Teradata tabulek a zobrazení.
* Podpora pro "Otevřít v Power BI Desktop" pro SQL Server (včetně databáze SQL Azure a Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage a HDFS zdroje.  
* Podpora pro ověřování pomocí protokolu LDAP pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou připojit k Teradata pomocí protokolu LDAP a TD2 ověřování.
* Podpora pro "Otevřít v aplikaci Excel" zdroje dat Teradata.
* Podpora pro poslední hledaný text v portálu Azure Data Catalog. Při vyhledávání v portálu, uživatelé mohou vybrat z naposledy použité hledaných termínů k urychlení prostředí zjišťování.
* Podpora pro verzi preview pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou obsahovat záznamy, které snímku s metadat extrahovaných ze zdroje dat.
* Podpora pro "Otevřít v aplikaci Excel" zdroje dat Azure SQL Data Warehouse.
* Podpora pro definování a úprava schémata úrovni sloupce pro ručně registrované datové prostředky. Po vytvoření ručně pomocí portálu Azure Data Catalog datový prostředek, mohou uživatelé přidat definice sloupců ve vlastnostech asset data.
* Podpora pro dotazy "má" při hledání Azure Data Catalog, chcete-li povolit zjišťování registrovaných datových prostředků, které měl konkrétních metadat. Syntaxe dotazu Azure Data Catalog nyní zahrnuje:

| Syntaxe dotazů | Účel |
| --- | --- |
| `has:previews` |Vyhledá datové prostředky, které zahrnují náhled |
| `has:documentation` |Vyhledá datových prostředků, pro které bylo zadáno dokumentace |
| `has:tableDataProfiles` |Vyhledá datové prostředky se informace o profilu úrovni tabulky dat |
| `has:columnsDataProfiles` |Vyhledá datové prostředky se informace o profilu data na úrovni sloupce |


> [!NOTE]
> "Otevřít v Power BI Desktop" vyžaduje aktuální verzi aplikace Power BI Desktop k instalaci. Pokud narazíte na problémy nebo chyby při použití této funkce, ujistěte se, že máte nejnovější verzi aplikace Power BI Desktop z [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Co je nového pro říjen 2015
Od října 2015 následující funkce jsou přidané do Azure Data Catalog:

* Podporu pro šifrování v klidovém stavu dat profily verze Preview a dat pro registrované datové zdroje. Azure Data Catalog transparentně šifruje všechny záznamy preview a data profily zdroje dat registrované ve službě bez nutnosti pro správu klíčů správci katalogu.
* Podpora pro zdroje dat Teradata. Uživatelé teď můžete registrovat a zjišťovat Teradata tabulek a zobrazení.
* Podpora pro místní zdroje dat Hive. Uživatelé teď můžete registrovat a zjišťovat tabulek Hive pro Apache Hive v Hadoop místní datové zdroje.
* Podpora pro uložená hledání na portálu Azure Data Catalog. Uživatele můžete uložit hledaných termínů a filtrovat výběr snadno opakujte předchozí hledání a definujte užitečné zobrazení katalogu obsahu. Uživatele můžete také označit uloženého hledání jako jejich výchozí vyhledávání. Když uživatel klikne na ikonu hledání "lupy" z domovské stránky portálu Azure Data Catalog nebo ze stránky "Začínáme", uživatel je převzat přímo do uloženého hledání označené jako výchozí.
* Podpora pro dokumentaci RTF registrovaných datových prostředků a kontejnerů na portálu Azure Data Catalog. Uživatelé nyní zadat dokumentace pro datové prostředky, jako je například tabulek, zobrazení a sestavy a pro kontejnery, jako jsou databáze a modely pro scénáře, kde značky a popisy nejsou dostatečná.
* Podpora pro ruční registraci data známé typy zdrojů. Uživatele můžete ručně zadat informace o zdroji dat pomocí portálu Azure Data Catalog pro všechny typy zdrojů dat podporovaných Azure Data Catalog.
* Podpora pro ověřování skupiny zabezpečení Azure Active Directory. Katalog správci mohou povolit katalogu přístup ke skupinám zabezpečení uživatelské účty, což usnadňuje správu přístupu k Azure Data Catalog.
* Podpora pro otevírání podregistru zdroje dat v aplikaci Excel na portálu Azure Data Catalog.

> [!NOTE]
> V aktuální verzi je podporována pouze Teradata TD2 ověřování. Uvolní další ověřovací mechanismy jsou podporovány v budoucnosti.

> [!NOTE]
> Použití funkce "Otevřít v aplikaci Excel" zdroje dat Hive, uživatelům musí mít nainstalované ovladače ODBC pro Hive.

## <a name="whats-new-for-september-2015"></a>Co je nového u září 2015
Od září 2015 následující funkce jsou přidané do Azure Data Catalog:

* Podpora včetně profily dat při registraci zdroje dat Hive.
* Podpora pro prostřednictvím kódu programu zjišťování rozhraní API katalogu usnadnit pro aplikace pro integraci s Azure Data Catalog.
* Nová "Začínáme" data source práci s funkcí zjišťování v portálu Azure Data Catalog. Když uživatelé zadají "objevit" stránky portálu Azure Data Catalog bez zadávání hledaný termín, zobrazí se přehled katalogu obsah, včetně nejčastěji používaných značky, odborníky, typy zdrojů dat a typy objektů.
* Podpora pro registraci a zjišťování objektů Azure SQL datového skladu a databází. Další informace o Azure SQL Data Warehouse, najdete v části [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Podpora pro registraci a zjišťování servery SQL Server Reporting Services jako kontejnery a modely služby SQL Server Analysis Services. Při registraci služby SSAS a SSRS objekty, Azure Data Catalog vytvoří položku pro model služby SSAS nebo serveru služby SSRS a pro sestavy a další objekty. Kontejnery můžete zjistit a opatřeny poznámkami pomocí portálu Azure Data Catalog. Uživatelé mohou také hledání a filtrování obsahu modelu nebo server kromě vyhledávání a filtrování obsahu katalogu.
* Podpora pro registraci a zjišťování objektů SQL Server Analysis Services přes HTTP nebo HTTPS. Uživatelé mohou teď připojit k serverům služby SSAS pomocí adresy URL (například https://servername/olap/msmdpump.dll) místo serveru název a mohou použít základní ověřování a anonymní připojení kromě ověřování systému Windows. Další informace o připojení protokolu HTTP nebo HTTPS k SSAS najdete v tématu [HTTP konfigurovat přístup ke službě Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Podpora pro zdroje dat Hive v HDInsight. Uživatelé teď můžete registrovat a zjišťovat tabulek Hive pro Apache Hive v Hadoop v HDInsight datové zdroje. Další informace o Hive v HDInsight, najdete v článku [centru dokumentace HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Podpora pro registraci a zjišťování Oracle – databáze a clustery HDFS jako kontejnery. Při registraci Oracle tabulek a zobrazení nebo HDFS, Azure Data Catalog vytvoří záznam pro databáze, tabulky a zobrazení. Databáze může být zjištěn a pomocí portálu Azure Data Catalog. Uživatelé mohou také hledání a filtrování obsahu databáze nebo clusteru kromě vyhledávání a filtrování obsahu katalogu.
* Podpora pro ruční registraci typy zdrojů dat je neznámý. Uživatele můžete ručně zadat informace o zdroji dat pomocí portálu Azure Data Catalog, aby zdroje dat nepodporuje nástroj registrace zdroje dat není explicitně mohli opatřeny poznámkami a zjistit.
* Podpora pro registraci a zjišťování databází systému SQL Server jako kontejnery. Při registraci serveru SQL Server tabulek a zobrazení, Azure Data Catalog vytvoří záznam pro databáze, tabulky a zobrazení. Databáze může být zjištěn a pomocí portálu Azure Data Catalog. Uživatelé mohou také hledání a filtrování obsahu databáze kromě vyhledávání a filtrování obsahu katalogu.

> [!NOTE]
> SSAS a SSRS objektů, které byly registrované před září 18 verze musí znovu registrovat pomocí nástroj registrace zdroje dat, než položka modelu nebo server se přidá do katalogu. Znovu registraci zdroje dat nemá vliv na žádné poznámky, které jsou přidané na portálu Azure Data Catalog uživatelům.

> [!NOTE]
> Oracle tabulek a zobrazení a HDFS soubory a adresáře, které byly registrované před září 11 verze musí být znovu zaregistrován pomocí nástroj registrace zdroje dat, než položka databáze nebo clusteru je přidaných do katalogu. Znovu registraci zdroje dat nemá vliv na žádné poznámky, které jsou přidané na portálu Azure Data Catalog uživatelům.

> [!NOTE]
> SQL Server tabulek a zobrazení, které byly registrované před září 4 verze musí být znovu zaregistrován pomocí nástroj registrace zdroje dat, než položka databáze je přidaných do katalogu. Znovu registraci zdroje dat nemá vliv na žádné poznámky, které jsou přidané na portálu Azure Data Catalog uživatelům.

## <a name="whats-new-for-august-2015"></a>Co je nového u srpen 2015
K srpnu 2015 následující funkce jsou přidané do Azure Data Catalog:

* Podpora profilace data z datové zdroje systému SQL Server a Oracle. Při registraci serveru SQL Server a Oracle tabulek a zobrazení, uživatelé mohou zahrnovat informace o data profilu pro objekty, které jsou registrované. Profil dat zahrnuje statistik úrovni objektů a úrovni sloupce.
* Podpora pro zdroje dat Hadoop HDFS. Uživatelé teď můžete registrovat a zjišťovat HDFS souborů a adresářů.
* Podpora pro poskytování informací o žádosti o přístup pro registrované datové zdroje. Pro všechny registrované datový prostředek uživatelé mohou teď poskytovat pokyny pro vyžádání přístupu, včetně adres URL, nebo e-mailu odkazy umožní snadnou integraci s existující nástroje a procesy.
* Popisy pro značky a odborníky, aby bylo snazší zjistit, co uživatelé zadali jaká metadata pro registrované datové prostředky.
* Přidali jsme nový tlačítko "User" a nabídky k naší horním navigačním panelu. Tato nabídka umožňuje uživateli najdete v části účet použitý k přihlášení k Azure Data Catalog a k odhlášení, pokud potřeby. Tato nabídka zobrazí název katalogu, které je vhodné pro vývojáře, kteří používají Azure Data Catalog REST API.
* Pouze edice Standard: Při přidávání vlastníky k datovým prostředkům, Azure Data Catalog teď podporuje uživatelské účty a skupiny zabezpečení jako vlastníci. Chcete-li přidat skupinu zabezpečení jako vlastníka pro vybrané datové prostředky, můžete zadat zobrazovaný název skupiny nebo skupiny (UPN) e-mailovou adresu, pokud má jedna.
* Podpora pro zdroje dat Azure Blob Storage. Uživatelé teď můžete registrovat a zjišťovat objekty BLOB úložiště Azure a adresářů.

