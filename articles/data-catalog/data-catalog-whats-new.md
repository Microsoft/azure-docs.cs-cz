---
title: Co je nového ve službě Azure Data Catalog | Dokumentace Microsoftu
description: Tento článek obsahuje přehled nových funkcí přidaných do katalogu dat Azure.
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
ms.openlocfilehash: 18673186e80b4d1784fa5b42e622d90dd4c889d4
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859999"
---
# <a name="whats-new-in-azure-data-catalog"></a>Co je nového ve službě Azure Data Catalog
Aktualizace **Azure Data Catalog** pravidelně se vydávají. Ne každá vydaná verze obsahuje nové funkce přístupných, protože některých vydání se zaměřuje na možnosti back-end služby. Na této stránce ukazuje novou přístupných možnosti přidané do služby Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Co je nového v listopadu 2017 
K listopadu 2017 jsme přidali následující funkce do služby Azure Data Catalog:

* Podpora pro propojení přímo na konkrétní obchodní termíny glosáře portálu Data Catalog. Uživatelům můžete zkopírovat odkazy z obchodní Glosář a vložit do dokumentů, e-mailů, zpráv nebo jiných míst jako přímý odkaz na definici termínu glosáře.
* Podpora pro instanční objekty Azure Active Directory. Správci služby Data Catalog může autorizovat klientským aplikacím přístup ke katalogu pomocí instančních objektů a oprávnění může udělit těchto aplikací konkrétní stejně, jako se udělují oprávnění pro uživatele a skupiny zabezpečení. Další informace najdete v části [aplikace a instanční objekty v Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Podpora pro ověřování Azure Active Directory při připojování ke zdrojům dat pro Azure SQL Database a Azure SQL Data Warehouse pomocí registračního nástroje zdroje dat katalogu Data Catalog. Další informace najdete v části [používání ověřování Azure Active Directory pro ověřování pomocí SQL Database nebo SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Co je nového v září 2017 
V září 2017 jsme přidali následující funkce do služby Azure Data Catalog:

* Podpora pro extrahování připojte se k relaci metadata ze zdroje dat DB2 při registraci souvisejících tabulek pomocí registračního nástroje zdroje dat.
* Podpora pro registraci zdroje 3.4 dat verzi MongoDB pomocí registračního nástroje zdroje dat.
* Podpora pro odstranění všech metadat pro objekty obsažené v rámci jedné operace při odstraňování databáze nebo jiném kontejneru z katalogu Data Catalog.
* Podpora pro zobrazení až 1 000 značky, termínů obchodního glosáře nebo jiné vyhledávání omezujících vlastností, při ladění na hledání v katalogu Data Catalog portálu.


## <a name="whats-new-for-august-2017"></a>Co je nového pro ze srpna 2017 
Od srpna 2017 jsme přidali následující funkce do služby Azure Data Catalog:

*   Nové ukázky pro vývojáře je k dispozici pro vytváření a správu metadata vztahů mezi pomocí REST API služby Data Catalog. *Importovat informace o vztahu do služby Data Catalog* ukázka je k dispozici na [stránku s ukázkami kódu katalogu Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Podpora pro extrahování připojte se k relaci metadata ze zdroje dat Teradata při registraci souvisejících tabulek pomocí registračního nástroje zdroje dat.
* Podporu pro SQL Server funkce vracející tabulku (TVF) objekty při registraci zdroje dat serveru SQL Server pomocí registračního nástroje zdroje dat.
* Více aktualizací a vylepšení zvýšit výkon a použitelnost portál služby Data Catalog.

## <a name="whats-new-for-july-2017"></a>Co je nového. července 2017 
Od července 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Podpora pro podrobnější kontrolu nad operace s metadaty permited včetně:
    - Správci katalogu můžou omezit schopnosti uživatelů přispívat značky a související metadata do katalogu, povolení přístup jen pro čtení do katalogu.
    - Správci katalogu můžou omezit schopnosti uživatelů registrovat nové zdroje dat v katalogu.
    - Správci katalogu můžou omezit schopnosti uživatelů převzít vlastnictví dat asset metadat v katalogu.
    - Pro skupiny zabezpečení Azure Active Directory a uživatele pro usnadnění správy oprávnění lze udělit oprávnění.
* Podpora vztahů mezi registrovaných datových assetů a zjišťování související datové assety v katalogu Data Catalog portálu, včetně:
    - Extrakce metadat vztah z SQL serveru (včetně Azure SQL Database), Oracle nebo MySQL zdroje dat při použití registračního nástroje zdroje dat katalogu Data Catalog.
    - Zjišťování souvisejících datových prostředků při prohlížení metadata prostředku na portálu pro Data Catalog.
    - Operace k definování, zjišťovat a spravovat vztahy mezi datové assety pomocí REST API služby Data Catalog.

Další podrobnosti o správě oprávnění ve službě Data Catalog najdete v tématu [jak zabezpečit přístup ke katalogu dat a datové assety](data-catalog-how-to-secure-catalog.md).
Další podrobnosti o relace ve službě Data Catalog, naleznete v tématu [zobrazení souvisejících datových prostředků ve službě Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Co je nového pro červen 2017 
Od června 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Podpora pro zdroje dat, Sybase, Apache Cassandra a MongoDB. Uživatelé teď můžete registrovat a zjišťovat Cassandra a MongoDB databáze a tabulky a databáze Sybase, tabulky a zobrazení.

> [!NOTE]
> Při registraci MongoDB a Cassandra tabulek, které obsahují sloupce s komplexními datovými typy, jako je například záznamy a pole, tyto sloupce nebudou zahrnuty v metadatech přidali do katalogu Data Catalog.

## <a name="whats-new-for-may-2017"></a>Co je nového pro květen 2017 
Od května 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Nové ukázky pro vývojáře je k dispozici pro hromadný import termínů obchodního glosáře. Glosář hromadný Import ukázka je k dispozici na [stránku s ukázkami katalog dat pro vývojáře](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Podpora pro úpravy informace o připojení ODBC v portálu Azure Data Catalog. Datový asset vlastníci a správci katalogu Data Catalog teď můžete upravit informace o připojení pro registrované zdroje dat ODBC aniž byste museli znovu registrovat zdroje dat.
*   Podpora pro kliknout, čímž adresy URL v obchodní Glosář definice a popisy. Když URL jsou součástí vlastnosti popis a definice pro termínů obchodního glosáře, adresy URL se zobrazí jako hypertextové odkazy na portálu pro Data Catalog.
*   Podpora zobrazení odborné názvy kromě odborné e-mailové adresy. Při prohlížení odborníky ve vlastnostech pro datový prostředek na portálu pro katalog dat, zobrazí se v popisu slovy odborníka na úplný název ze služby Azure Active Directory.

## <a name="whats-new-for-april-2017"></a>Co je nového za duben 2017 
Od dubna 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Podpora pro zdroje dat ODBC. Uživatelé teď můžete registrovat a zjišťovat databáze, tabulky a zobrazení pomocí katalogu Data Catalog registračního nástroje zdroje dat ODBC.

## <a name="whats-new-for-march-2017"></a>Co je nového v březnu 2017 
V březnu 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Podpora pro použití skupin zabezpečení AAD správcům katalogu Data Catalog.
*   Azure Data Catalog je teď dostupná v nové oblasti Azure. Zákazníci si můžou zřídit Azure Data Catalog v centrální oblasti západní USA, kromě USA – východ, USA – Západ, západní Evropa a Austrálie – východ, Severní Evropa a jihovýchodní Asie. Další informace najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Co je nového v únoru 2017 
Od února 2017 jsme přidali následující funkce do služby Azure Data Catalog:
*   Podpora pro pokročilé nastavení v nástroji pro registraci zdroje dat katalogu Data Catalog. Při registraci zdrojů velkých objemů dat mohou uživatelé zadat hodnoty časového limitu příkazu.
*   Podpora pro zdroje dat FTP využívající databázi PostgreSQL. Uživatelé teď můžete registrovat a zjišťovat FTP soubory a adresáře a databáze PostgreSQL, tabulky a zobrazení.

## <a name="whats-new-for-january-2017"></a>Co je nového v lednu 2017 
Od ledna 2017 následující funkce byly přidány do služby Azure Data Catalog:
*   Azure Data Catalog je nyní [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) kompatibilní.
*   Integrace s [načíst a transformovat data v Excelu 2016 a Power Query pro Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Excel uživatelé mohou sdílet dotazy a zjišťovat dotazů pomocí Azure Data Catalog z uvnitř aplikace Excel. Tato funkce je dostupná pro uživatele s licencí Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Co je nového v prosinci 2016
Od prosince 2016 následující funkce byly přidány do služby Azure Data Catalog:
*   Azure Data Catalog je nyní [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) a [modelové klauzule EU](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) kompatibilní.
*   Podpora pro úpravy informace o připojení zdroje dat Datový asset vlastníci a správci katalogu Data Catalog teď můžete upravit informace o připojení pro registrované datové zdroje bez nutnosti znovu registrovat zdroje dat.
*   Podpora pro zdroje dat Salesforce.com. Uživatelé teď můžete registrovat a zjišťovat objekty Salesforce.


## <a name="whats-new-for-november-2016"></a>Co je nového v listopadu 2016
Od listopadu 2016 následující funkce byly přidány do služby Azure Data Catalog:
*   Azure Data Catalog je nyní [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) a [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) kompatibilní.
*   Podpora pro ruční registraci zdroje dat ODBC pomocí katalogu Data Catalog portál a rozhraní REST API.

## <a name="whats-new-for-september-2016"></a>Co je nového v září 2016
Od září 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Podpora pro zdroje dat IBM DB2. Uživatelé teď můžete registrovat a zjišťovat databáze DB2, tabulky a zobrazení.
* Podpora pro zdroje dat služby Azure Cosmos DB. Uživatelé teď můžete registrovat a zjišťovat služby Cosmos DB, databáze a kolekce.
* Podpora pro přizpůsobení názvu katalogu Data Catalog portálu. Správci katalogu teď můžete zadat text, který se zobrazí v nadpis portálu, jako je název organizace.

## <a name="whats-new-for-august-2016"></a>Co je nového v srpnu 2016
Od srpna 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Vylepšení pro registraci zdroje dat SQL Server Master Data Services (MDS). Uživatelé teď může obsahovat verze Preview a dat profilů při registraci služby MDS entity pomocí registračního nástroje zdroje dat katalogu Data Catalog.
* Podpora správcem definované organizační uložené výsledky hledání. Při ukládání vyhledávání v portálu pro katalog dat, Data Catalog mohou nyní správci uložení hledání pro osobní použití nebo pro všechny uživatele katalogu. Organizační uložené výsledky hledání jsou sdíleny se všemi uživateli katalogu a může poskytnout standardizované počáteční body pro zjišťování zdrojů dat.
* Aktualizace zobrazení vlastností portálu Data Catalog. Všechny vlastnosti datového prostředku se teď zobrazí a spravovaná v jedné, umožňující změnu velikosti podokně poskytnout více konzistentní a zjistitelné prostředí.

## <a name="whats-new-for-july-2016"></a>Co je nového za červenec 2016
Od července 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Podpora pro zdroje dat SQL Server Master Data Services (MDS). Uživatelé teď můžete registrovat a zjišťovat služby MDS modely a entity.
* Podpora pro uložené procedury SQL serveru. Uživatelé teď můžete registrovat a zjišťovat objekty uložené procedury v zdroje dat serveru SQL Server.
* Podpora dalších jazyků na portálu Azure Data Catalog a nástroj registrace zdroje dat a cena celkem 18 podporované jazyky. Uživatelské prostředí Azure Data Catalog je lokalizován podle jazykové předvolby nastavit ve Windows nebo ve webovém prohlížeči.
* Aktualizace a vylepšení pro katalog dat domovské stránky portálu, včetně vylepšení výkonu a zjednodušené uživatelské prostředí.

## <a name="whats-new-for-june-2016"></a>Novinky z června 2016
Od června 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Podpora pro změnu velikosti sloupců v zobrazení seznamu při zjištění datových assetů v katalogu Data Catalog portálu. Uživatelé teď můžete změnit velikost snadněji číst zdlouhavé asset metadat – například značky a popisy jednotlivých sloupců.
* Power Query pro Excel je přidaný do nabídky "Open in" v katalogu Data Catalog portálu. Uživatelé mohou nyní otevřít podporovaných zdrojů dat v Excelu 2016 nebo v aplikaci Excel 2010 a Excelu 2013 se [Power Query pro Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) nainstalovaný doplněk.
* Podpora pro zdroje dat Azure Table Storage. Uživatelé teď můžete registrovat a zjišťovat objekty tabulky zdroje dat služby Azure Storage.

## <a name="whats-new-for-may-2016"></a>Co je nového pro květen 2016
Od května 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Obchodní glosář, který umožňuje správcům katalogu definovat obchodní termíny a hierarchií, chcete-li vytvořit společný obchodní slovník. Uživatelům můžete označit registrovaných datových assetů s termíny glosáře, aby bylo snazší zjišťování a pochopení obsahu katalogu. Další informace najdete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md)  
* Vylepšení Data Catalog obchodní glosář, který umožňuje uživatelům aktualizovat v rámci jedné operace více termíny glosáře. Uživatelé mohou vybrat více sad podmínek upravit následující pole:
  * Nadřazený termín: Uživatel může vybrat nový nadřazený termín a všechny vybrané podmínky se aktualizují, aby se podřízené objekty vybraného nadřazený termín. Pokud vybraný podmínky všechny mají stejnou nadřazenou položku a pak nadřazeného se zobrazí v textovém poli, jinak při nadřazený termín je nastaveno na prázdné.   
  * Značky a zúčastněnými stranami: Uživatelé můžete přidávat a odebírat značky a zúčastněnými stranami pro více termíny glosáře pomocí stejné prostředí jako označování více datových assetů.

> [!NOTE]
> Obchodní Glosář je k dispozici pouze v nástroje Azure Data Catalog Standard Edition. Edice Free neposkytuje funkce pro řízení označování nebo obchodní Glosář.

## <a name="whats-new-for-march-2016"></a>Co je nového pro březen 2016
Od března 2016 následující funkce byly přidány do Azure Data Catalog: g:

* Konsolidované rozhraní REST API pro koncový bod pro programově přístup k možnosti vyhledávání a katalog asset možnosti správy služby Azure Data Catalog. Tento koncový bod rozhraní API koncového bodu a katalog rozhraní API pro vyhledávání byla zastaralá a vyřazuje na 21. březnem 2016. Neexistují žádné změny sémantice rozhraní API. Změnit identifikátor URI, jenom koncový bod. Další informace najdete v tématu [Reference k rozhraní API REST Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267595.aspx). Ukázky rozhraní API najdete v tématu [ukázky pro vývojáře Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Co je nového v únoru 2016
Od února 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Výběr zdroje nově přepracovaném dat prostředí v nástroji pro registraci zdroje dat Azure Data Catalog. Nástroj registrace zdroje dat byla aktualizována, aby bylo snazší najít a vybrat ze zdroje dat podporované službou Azure Data Catalog.
* Podpora dalších 10 jazyků na portálu Azure Data Catalog a nástroj registrace zdroje dat Vedle angličtiny se teď je k dispozici v němčina, španělština, francouzština, italština, japonština, korejština, brazilská portugalština, ruština, zjednodušená čínština a tradiční čínštiny prostředí Azure Data Catalog Uživatelské prostředí Azure Data Catalog je lokalizován podle jazykové předvolby nastavit ve Windows nebo ve webovém prohlížeči uživatele.
* Podpora pro geografickou replikaci dat Azure Data Catalog pro obchodní kontinuity podnikových procesů a zotavení po havárii. Veškerý obsah v Azure Data Catalog, včetně metadat a crowdsourcingu poznámky zdroje dat jsou nyní replikovat mezi dvěma oblastmi Azure bez dalších poplatků pro zákazníky. Oblastí Azure jsou předem spárované, aspoň 500 mil od sebe a postupujte podle mapování, jak je popsáno v [obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): spárované oblasti Azure](../best-practices-availability-paired-regions.md).
* Podpora pro změnu předplatné Azure, které používají Azure Data Catalog. Správci služby Azure Data Catalog můžete na stránce nastavení na portálu Azure Data Catalog a vyberte jiné předplatné Azure pro účely fakturace.

## <a name="whats-new-for-january-2016"></a>Co je nového pro leden 2016
Od ledna 2016 následující funkce byly přidány do služby Azure Data Catalog:

* Podpora pro další zdroje dat ručně registraci. Teď můžete použít "Vytvořit ruční položku" na portálu Azure Data Catalog, nebo pomocí REST API služby Azure Data Catalog zaregistrovat následující zdroje dat:
  * OData - funkce, sady entit a kontejner entit
  * HTTP – soubor, koncový bod, sestavy a serveru
  * Systém souborů – soubor
  * SharePoint – seznam
  * FTP – souborů a adresářů
  * Salesforce.com – objekt
  * DB2 – tabulka, zobrazení a databáze
  * PostgreSQL – tabulka, zobrazení a databáze
* Podpora pro "Otevřít v SQL Server Data Tools" pro zdroje dat serveru SQL Server (včetně Azure SQL Database a Azure SQL Data Warehouse).  
* Podpora pro registraci a zjišťování zobrazení SAP HANA a balíčky. Zdroje dat SAP HANA pomocí Azure Data Catalog zdroje dat nástroj pro registraci a můžete opatřit poznámkami a zjistit registrované zdroje dat SAP HANA pomocí portálu Azure Data Catalog můžete registrovat.
* Možnost připnout a Odepnout datové assety v portálu Azure Data Catalog. Můžete pro Připnutí datových assetů, aby se daly snadněji opětovné zjištění a znovu použít.
* Nově přepracovaném domovské stránky portálu Azure Data Catalog. Nová domovská stránka obsahuje přehled o aktuální aktivity uživatele – včetně nedávno publikovaných prostředky, připnuté prostředky a uložené výsledky hledání – a přehled o aktivitě v katalogu jako celek.
* Podpora pro trvalé uživatelská nastavení na portálu Azure Data Catalog. Nastavení činnosti koncového uživatele – včetně zobrazení mřížky nebo dlaždici, počet výsledků na stránku a stiskněte klávesu nebo vypnout zvýraznění – jsou trvalé mezi uživatelských relací.
* Azure Data Catalog je teď dostupná ve dvou nových oblastech Azure. Zákazníci si můžou zřídit katalogu dat Azure v oblastech severní Evropa a jihovýchodní Asie, kromě USA – východ, USA – Západ, západní Evropa a Austrálie – východ. Další informace najdete v tématu [oblastí Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Open in SQL Server Data Tools" vyžaduje Visual Studio 2013 with Update 4 a nástrojů SQL serveru k instalaci. Chcete-li nainstalovat nejnovější verzi SQL Server Data Tools, navštivte [stáhnout SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Co je nového pro prosinec 2015
Od prosince 2015 následující funkce byly přidány do služby Azure Data Catalog:

* Podpora pro profily data pro zdroje dat Azure SQL Data Warehouse. Při registraci Azure SQL Data Warehouse tabulek a zobrazení, uživatelé mohou obsahující metadata extrahovaná přímo ze zdroje dat metriky dat profilu.
* Podpora pro registraci a zjišťování objektů MySQL a databází. Uživatelé můžou registrovat zdroje dat MySQL pomocí Azure Data Catalog zdroje dat nástroj pro registraci a můžete opatřit poznámkami a zjistit registrované zdroje dat MySQL pomocí portálu Azure Data Catalog.
* Podpora pro ověřování SPNEGO a Windows pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou připojit k Teradata SPNEGO a Windows a LDAP a TD2 ověřováním.
* Podpora pro zdroje dat Azure Data Lake Store. Uživatelé teď můžete registrovat a zjišťovat zdroje dat Azure Data Lake Store pomocí Azure Data Catalog.
* Podpora pro ruční zadání nastavení proxy serveru sítě v nástroji pro registraci zdroje dat Azure Data Catalog. Uživatelé "Změnit nastavení proxy serveru" můžete vybrat z úvodní stránka nástroje a můžete zadat adresu proxy serveru a port, který se použije nástroj.

## <a name="whats-new-for-november-2015"></a>Co je nového v listopadu 2015
Od listopadu 2015 jsme přidali následující funkce do služby Azure Data Catalog:

* Umožňuje zobrazit a zkopírujte připojovací řetězce z portálu Azure Data Catalog pro SQL Server (včetně Azure SQL Database) a Oracle datového zdroje. Uživatelům můžete kliknout na odkaz "Zobrazit připojovací řetězce" v informace o připojení pro SQL Server nebo Oracle tabulky, zobrazení nebo databáze, pokud chcete zobrazit připojovací řetězce použité pro připojení ke zdroji dat. ADO.NET, rozhraní ODBC, OLEDB a JDBC připojovací řetězce jsou k dispozici pro zdroje dat serveru SQL Server. ODBC a OLEDB připojovací řetězce jsou k dispozici pro Oracle datového zdroje.
* Podpora pro včetně profilů dat při registraci Teradata tabulek a zobrazení.
* Podpora pro "Otevřít v Power BI Desktopu" pro SQL Server (včetně Azure SQL Database a Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage a zdrojů HDFS.  
* Podpora pro ověřování pomocí protokolu LDAP pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou připojit k Teradata pomocí protokolu LDAP a TD2 ověřování.
* Podpora pro "Open in Excel" pro zdroje dat Teradata.
* Podpora pro poslední hledaný text v portálu Azure Data Catalog. Při hledání na portálu, uživatelé mohou vybrat z naposledy použitých hledané termíny ke zrychlení prostředí pro zjišťování.
* Podpora pro verzi preview pro zdroje dat Teradata. Při registraci Teradata tabulek a zobrazení, uživatelé mohou metadata extrahovaná přímo ze zdroje dat obsahující záznamy snímku.
* Podpora pro "Open in Excel" pro zdroje dat Azure SQL Data Warehouse.
* Podpora pro definování a úprava schémat sloupců pro ručně registrovaných datových assetů. Po vytvoření ručně k datovému assetu pomocí portálu Azure Data Catalog, uživatelé mohou přidávat definice sloupců ve vlastnosti datového prostředku.
* Podpora pro dotazy "má" při vyhledávání Azure Data Catalog umožňuje zjišťování registrovaných datových assetů, které mají konkrétní metadat. Azure Data Catalog syntaxe dotazu teď obsahuje:

| Syntaxe dotazů | Účel |
| --- | --- |
| `has:previews` |Vyhledá datových assetů, které zahrnují ve verzi preview |
| `has:documentation` |Vyhledá datových assetů, pro které byl poskytnut dokumentace |
| `has:tableDataProfiles` |Vyhledá datových assetů pomocí informace o profilu úrovni tabulky dat |
| `has:columnsDataProfiles` |Vyhledá datových assetů pomocí dat na úrovni sloupce informace o profilu |


> [!NOTE]
> "Otevřít v Power BI Desktopu" vyžaduje aktuální verzi instalaci aplikace Power BI Desktopu. Pokud narazíte na problémy nebo chyby pomocí této funkce, ujistěte se, že máte nejnovější verzi Power BI Desktopu z [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Co je nového pro říjen 2015
Od října 2015 jsme přidali následující funkce do služby Azure Data Catalog:

* Podpora pro šifrování neaktivních dat náhledy a dat profilů pro registrované datové zdroje. Azure Data Catalog transparentně šifruje všechny záznamy ve verzi preview a dat profilů zdroje dat registrované ve službě bez nutnosti správy klíčů ve Správci katalogu.
* Podpora pro zdroje dat Teradata. Uživatelé teď můžete registrovat a zjišťovat Teradata tabulek a zobrazení.
* Podpora pro místní zdroje dat Hive. Uživatelé teď můžete registrovat a zjišťovat tabulek Hive pro Apache Hive ve zdrojích dat v místním systému Hadoop.
* Podpora pro uložená hledání na portálu Azure Data Catalog. Uživatele můžete uložit hledané termíny a filtrovat tak, že jednoduše opakujte předchozí hledání a definovat užitečné zobrazení katalogu obsahu. Uživatele můžete také označit uložené výsledky hledání jako jejich výchozí hledání. Když uživatel klikne na ikonu Hledat "lupy" z domovské stránky portálu Azure Data Catalog, nebo ze stránky "Začínáme", uživatel je přesměrováni přímo na uložené výsledky hledání označený jako výchozí.
* Podpora pro dokumentaci formátovaný text registrovaných datových assetů a kontejnerů na portálu Azure Data Catalog. Uživatelé teď můžete zadat dokumentaci pro datové prostředky, jako například tabulky, zobrazení a sestavy a pro kontejnery, jako jsou databáze a modely pro scénáře, kde nejsou dostatečná značky a popisy.
* Podpora pro ruční registraci typů zdrojů dat známé. Uživatele můžete ručně zadat informace o zdroji dat pomocí portálu Azure Data Catalog pro všechny typy zdroje dat podporované službou Azure Data Catalog.
* Podpora pro autorizaci skupin zabezpečení Azure Active Directory. Správci katalogu mohou povolit katalog přístup ke skupinám zabezpečení uživatelských účtů, což usnadňuje správu přístupu ke službě Azure Data Catalog.
* Podpora otevírání zdroje dat Hive v aplikaci Excel na portálu Azure Data Catalog.

> [!NOTE]
> V aktuální verzi se podporuje jenom Teradata TD2 ověřování. Další ověřovací mechanismy jsou podporované v budoucích vydáních.

> [!NOTE]
> Použití funkce "Open in Excel" pro zdroje dat Hive, uživatelé musí máte nainstalovaný ovladač ODBC pro Hive.

## <a name="whats-new-for-september-2015"></a>Co je nového v září 2015
Od září 2015 jsme přidali následující funkce do služby Azure Data Catalog:

* Podpora pro včetně profilů dat při registraci zdroje dat Hive.
* Podpora prostřednictvím kódu programu zjišťování rozhraní API katalogu snadnější aplikací můžete integrovat s Azure Data Catalog.
* Nová "Začínáme" data source prostředí pro zjišťování na portálu Azure Data Catalog. Když uživatelé zadají stránce "objevit" portál služby Azure Data Catalog bez nutnosti zadávat hledaný termín, zobrazí se jim základní informace o obsahu katalogu včetně nejčastěji používané značky, odborníky, typů zdrojů dat a typy objektů.
* Podpora pro registraci a zjišťování objektů Azure SQL Data Warehouse a databází. Další informace o Azure SQL Data Warehouse, najdete v části [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Podpora pro registraci a zjišťování modely služby SQL Server Analysis Services a serverů SQL Server Reporting Services jako kontejnery. Při registraci objekty SSAS a služby SSRS, Azure Data Catalog vytvoří záznam pro modelu SSAS a serveru služby SSRS a sestav a dalších objektů. Kontejnery můžete zjistit a pomocí portálu Azure Data Catalog. Uživatele můžete také hledat a filtrovat obsah modelu nebo server kromě vyhledávání a filtrování obsahu katalogu.
* Podpora pro registraci a zjišťování objektů SQL Server Analysis Services přes HTTP/HTTPS. Uživatelé se nyní může připojit k serverům služby SSAS pomocí adresy URL (například https://servername/olap/msmdpump.dll) namísto serveru pojmenujte a využívat základní ověřování a anonymní připojení kromě ověřování Windows. Další informace o připojení HTTP/HTTPS pro SSAS najdete v tématu [HTTP konfigurovat přístup ke službě Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Podpora pro zdroje dat Hive v HDInsight. Uživatelé teď můžete registrovat a zjišťovat tabulek Hive pro Apache Hive v clusteru Hadoop v HDInsight zdroje dat. Další informace o Hive v HDInsight, najdete v článku [centrum dokumentace pro HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Podpora pro registraci a zjišťování databází Oracle a clustery HDFS jako kontejnery. Při registraci Oracle tabulek a zobrazení nebo HDFS, Azure Data Catalog vytvoří záznam pro databáze, tabulky a zobrazení. Databáze může být zjištěn a pomocí portálu Azure Data Catalog. Uživatele můžete také hledat a filtrovat obsah databáze nebo clusteru kromě vyhledávání a filtrování obsahu katalogu.
* Podpora pro ruční registraci typů zdrojů dat neznámý. Uživatele můžete ručně zadat informace o zdroji dat pomocí portálu Azure Data Catalog, aby zdroje dat podporované nejsou explicitně nástroj registrace zdroje dat je možné s poznámkami a zjistit.
* Podpora pro registraci a zjišťování databází systému SQL Server jako kontejnery. Při registraci, tabulky a zobrazení SQL serveru, Azure Data Catalog vytvoří záznam pro databáze, tabulky a zobrazení. Databáze může být zjištěn a pomocí portálu Azure Data Catalog. Uživatele můžete také hledat a filtrovat obsah databázi vedle vyhledávání a filtrování obsahu katalogu.

> [!NOTE]
> SSAS a SSRS objekty, které byly registrované před vydáním 18. září musí znovu registrovat pomocí registračního nástroje zdroje dat než položka modelu nebo server se přidá do katalogu. Opětovné registrace zdroje dat nemá vliv na jakékoli poznámky, které jsou přidané uživatelům na portálu Azure Data Catalog.

> [!NOTE]
> Oracle tabulek a zobrazení a HDFS soubory a adresáře, které byly registrované před vydáním 11. září musí znovu registrovat pomocí nástroje pro registraci zdroje dat předtím, než je záznam databáze nebo clusteru přidaných do katalogu. Opětovné registrace zdroje dat nemá vliv na jakékoli poznámky, které jsou přidané uživatelům na portálu Azure Data Catalog.

> [!NOTE]
> Tabulky a zobrazení, které byly registrované před verze 4. září SQL serveru musí znovu registrovat pomocí nástroje pro registraci zdroje dat před přidáním položky databáze do katalogu. Opětovné registrace zdroje dat nemá vliv na jakékoli poznámky, které jsou přidané uživatelům na portálu Azure Data Catalog.

## <a name="whats-new-for-august-2015"></a>Co je nového v srpen 2015
K srpnu 2015 jsme přidali následující funkce do služby Azure Data Catalog:

* Podpora pro data profilování zdrojů dat systému SQL Server a Oracle. Při registraci serveru SQL Server a Oracle tabulek a zobrazení, uživatelé mohou zahrnovat informace o profilu dat pro objekty, které jsou registrovány. Data profilu vloží statistiky úrovni objektu nebo sloupce.
* Podpora pro zdroje dat Hadoop HDFS. Uživatelé teď můžete registrovat a zjišťovat HDFS souborů a adresářů.
* Podpora pro poskytování informací o žádosti o přístup pro registrované datové zdroje. K registrovaným datovým prostředkům mohou uživatelé nyní poskytují pokyny pro žádost o přístup, včetně e-mailu odkazy nebo adresy URL, tak jednoduše integrovat do existujících nástrojů a procesů.
* Popisy tlačítek pro značky a odborníky, aby bylo snazší zjistit, co uživatelé zadali metadat jaký registrovaných datových assetů.
* Přidali jsme nové tlačítko "User" a nabídky pro naše horním navigačním panelu. Tato nabídka umožňuje uživateli zobrazit účet použitý k přihlášení ke službě Azure Data Catalog a odhlásit se v případě potřeby. Tato nabídka také zobrazí název katalogu, který je užitečný pro vývojáře pomocí REST API služby Azure Data Catalog.
* Standard Edition pouze: Při přidávání vlastníky k datovým assetům, Azure Data Catalog teď podporuje uživatelské účty a skupiny zabezpečení jako vlastníky. Přidat skupinu zabezpečení jako vlastník vybrané datové prostředky, můžete zadat zobrazovaný název skupiny nebo skupiny (UPN) e-mailovou adresu, pokud jej obsahuje.
* Podpora pro zdroje dat Azure Blob Storage. Uživatelé teď můžete registrovat a zjišťovat objekty BLOB služby Azure Storage a adresáře.

