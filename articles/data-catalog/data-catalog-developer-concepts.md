---
title: Koncepty vývojářů katalogu dat Azure
description: Úvod ke klíčovým konceptům v koncepčním modelu Katalogu dat Azure, které jsou zpřístupňovány prostřednictvím rozhraní REST API katalogu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976833"
---
# <a name="azure-data-catalog-developer-concepts"></a>Koncepty vývojářů katalogu dat Azure
Microsoft **Azure Data Catalog** je plně spravovaná cloudová služba, která poskytuje funkce pro zjišťování zdrojů dat a pro metadata crowdsourcingového zdroje dat. Vývojáři mohou službu používat prostřednictvím rozhraní REST API. Principy konceptů implementovaných ve službě je důležité pro vývojáře úspěšně integrovat s **Azure Data Catalog**.

## <a name="key-concepts"></a>Klíčové koncepty
Koncepční model **katalogu dat Azure** je založený na čtyřech klíčových konceptech: **Katalog**, **Uživatelé**, **Datové zdroje**a **Poznámky**.

![Ilustrace koncepčního modelu katalogu dat Azure](./media/data-catalog-developer-concepts/concept2.png)

*Obrázek 1 – Zjednodušený koncepční model katalogu dat Azure*

### <a name="catalog"></a>Katalog
**Katalog** je kontejner nejvyšší úrovně pro všechna metadata, která organizace ukládá. Pro každý účet Azure je povolen jeden **katalog.** Katalogy jsou vázané na předplatné Azure, ale pro daný účet Azure se dá vytvořit jenom jeden **katalog,** i když účet může mít víc předplatných.

Katalog obsahuje **uživatele** a **datové zdroje**.

### <a name="users"></a>Uživatelé
Uživatelé jsou objekty zabezpečení, které mají oprávnění k provádění akcí (vyhledávání v katalogu, přidávání, úpravy nebo odebrání položek atd.) v katalogu.

Existuje několik různých rolí, které může uživatel mít. Informace o rolích naleznete v části Role a autorizace.

Lze přidat jednotlivé uživatele a skupiny zabezpečení.

Azure Data Catalog používá Azure Active Directory pro správu identit a přístupu. Každý uživatel katalogu musí být členem služby Active Directory pro účet.

### <a name="assets"></a>Prostředky
**Katalog** obsahuje datové prostředky. **Datové zdroje** jsou jednotkou rozlišovací schopnost spravované katalogem.

Členitost datového zdroje se liší podle zdroje dat. Pro SQL Server nebo Oracle Database může být datový md tabulkou nebo zobrazením. Pro SQL Server Analysis Services může být prostředek Míra, dimenze nebo klíčový ukazatel výkonu (KPI). Pro sql server reporting services je datový mši sestavou.

Datový **zdroj** je věc, kterou přidáte nebo odeberete z katalogu. Je to jednotka výsledku dostanete zpět z **vyhledávání**.

Datový **zdroj** je tvořen jeho názvem, umístěním a typem a popisy, které jej dále popisují.

### <a name="annotations"></a>Poznámky
Anotace jsou položky, které představují metadata o datových zdrojů.

Příklady poznámky jsou popis, značky, schéma, dokumentace, atd. Úplný seznam typů majetku a typů anotací je uveden v části Model objektu majetku.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcingové poznámky a uživatelská perspektiva (mnohonázornost)
Klíčovým aspektem Azure Data Catalog je, jak podporuje crowdsourcing metadat v systému. Na rozdíl od wiki přístupu – kde je jen jeden názor a poslední zapisovatel vyhraje – model Azure Data Catalog umožňuje více názorů žít vedle sebe v systému.

Tento přístup odráží reálný svět podnikových dat, kde různí uživatelé mohou mít různé pohledy na dané aktivum:

* Správce databáze může poskytnout informace o smlouvách o úrovni služeb nebo dostupné okno zpracování pro hromadné operace ETL
* Správce dat může poskytnout informace o obchodních procesech, na které se aktivum vztahuje, nebo o klasifikacích, které na něj podnik použil.
* Finanční analytik může poskytnout informace o tom, jak jsou data používána během úkolů vykazování na konci období.

Pro podporu tohoto příkladu může každý uživatel – DBA, správce dat a analytik – přidat popis do jedné tabulky, která byla zaregistrována v katalogu. Všechny popisy jsou udržovány v systému a na portálu Katalog dat Azure jsou zobrazeny všechny popisy.

Tento vzorek se aplikuje na většinu položek v objektovém modelu, takže typy objektů v datové části JSON jsou často matice pro vlastnosti, kde můžete očekávat singleton.

Například pod kořenem datového zdroje je pole objektů popisu. Vlastnost pole se nazývá "popisy". Objekt popisu má jednu vlastnost - popis. Vzor je, že každý uživatel, který zadá popis získá popis objekt vytvořený pro hodnotu dodanou uživatelem.

Uživatelské kolo pak může zvolit způsob zobrazení kombinace. Existují tři různé vzory pro zobrazení.

* Nejjednodušší vzor je "Zobrazit vše". V tomto vzorku jsou všechny objekty zobrazeny v zobrazení seznamu. UX portálu Azure Data Catalog používá tento vzor pro popis.
* Dalším vzorem je "Sloučit". V tomto vzoru jsou všechny hodnoty od různých uživatelů sloučeny a duplikát odebrány. Příklady tohoto vzoru v uživatelském rozhraní portálu Azure Data Catalog jsou vlastnosti značek a odborníků.
* Třetí vzor je "poslední spisovatel vyhraje". V tomto vzoru je zobrazena pouze poslední zapsaná hodnota. friendlyName je příkladem tohoto vzoru.

## <a name="asset-object-model"></a>Objektový model majetku
Jak je uvedeno v části Klíčové koncepty, **objektový** model Azure Data Catalog obsahuje položky, které mohou být prostředky nebo poznámky. Položky mají vlastnosti, které mohou být volitelné nebo povinné. Některé vlastnosti platí pro všechny položky. Některé vlastnosti platí pro všechny datové zdroje. Některé vlastnosti platí pouze pro určité typy prostředků.

### <a name="system-properties"></a>Systémové vlastnosti
<table><tr><td><b>Název vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr><tr><td>časové razítko</td><td>DateTime</td><td>Čas poslední úpravy položky. Toto pole je generováno serverem při vložení položky a při každé aktualizaci položky. Hodnota této vlastnosti je ignorována při vstupu operací publikování.</td></tr><tr><td>id</td><td>Uri</td><td>Absolutní url položky (jen pro čtení). Jedná se o jedinečný adresovatelný identifikátor URI pro položku.  Hodnota této vlastnosti je ignorována při vstupu operací publikování.</td></tr><tr><td>type</td><td>Řetězec</td><td>Typ datového zdroje (jen pro čtení).</td></tr><tr><td>Etag</td><td>Řetězec</td><td>Řetězec odpovídající verzi položky, která může být použita pro optimistické řízení souběžnosti při provádění operací, které aktualizují položky v katalogu. "*" lze použít k odpovídající libovolné hodnotě.</td></tr></table>

### <a name="common-properties"></a>Společné vlastnosti
Tyto vlastnosti platí pro všechny kořenové typy datových zdrojů a všechny typy anotací.

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logická hodnota</td><td>Označuje, zda jsou data položky odvozena ze zdrojového systému (například sql server databáze, databáze Oracle) nebo vytvořená uživatelem.</td></tr>
</table>

### <a name="common-root-properties"></a>Společné kořenové vlastnosti
<p>
Tyto vlastnosti platí pro všechny typy kořenových datových zdrojů.

<table><tr><td><b>Název vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr><tr><td>jméno</td><td>Řetězec</td><td>Název odvozený z informací o umístění zdroje dat</td></tr><tr><td>Dsl</td><td>Umístění zdroje dat</td><td>Jednoznačně popisuje zdroj dat a je jedním z identifikátorů pro datový zdroj. (Viz oddíl dvojí identity).  Struktura dsl se liší podle protokolu a typu zdroje.</td></tr><tr><td>Datasource</td><td>DataSourceInfo</td><td>Více podrobností o typu majetku.</td></tr><tr><td>lastRegisteredBy</td><td>Zásada zabezpečení</td><td>Popisuje uživatele, který tento datový zdroj naposledy zaregistroval.  Obsahuje jedinečné id pro uživatele (upn) a zobrazované jméno (lastName a firstName).</td></tr><tr><td>containerId</td><td>Řetězec</td><td>Id datového zdroje kontejneru pro zdroj dat. Tato vlastnost není podporována pro typ Container.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Běžné vlastnosti poznámky bez otevaní
Tyto vlastnosti platí pro všechny typy poznámk y bez oteva (poznámky, které umožňují být více násobné na datový zdroj).

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr>
<tr><td>key</td><td>Řetězec</td><td>Uživatelský zadaný klíč, který jednoznačně identifikuje poznámku v aktuální kolekci. Délka klíče nesmí přesáhnout 256 znaků.</td></tr>
</table>

### <a name="root-asset-types"></a>Kořenové typy datových zdrojů
Kořenové typy prostředků jsou typy, které představují různé typy datových prostředků, které lze zaregistrovat v katalogu. Pro každý kořenový typ je zobrazení, které popisuje datový zdroj a poznámky zahrnuté v zobrazení. Název zobrazení by měl být použit v odpovídajícím segmentu adresy URL {view_name} při publikování datového zdroje pomocí rozhraní REST API.

<table><tr><td><b>Typ majetku (název zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Povolené poznámky</b></td><td><b>Komentáře</b></td></tr><tr><td>Tabulka ("tabulky")</td><td></td><td></td><td>Popis<p>Friendlyname<p>Značka<p>Schéma<p>Popis sloupce<p>SloupecTag<p> Odborník<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentace<p></td><td>Tabulka představuje všechna tabulková data.  Například: SQL Table, SQL View, Analysis Services Tabul Table, Analysis Services Multidimensional dimenze, Oracle Table, atd.   </td></tr><tr><td>Opatření ("opatření")</td><td></td><td></td><td>Popis<p>Friendlyname<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje analysis services míra.</td></tr><tr><td></td><td>Opatření</td><td>Sloupec</td><td></td><td>Metadata popisující míru</td></tr><tr><td></td><td>isCalculated (isCalculated) </td><td>Logická hodnota</td><td></td><td>Určuje, zda je míra vypočtena či nikoli.</td></tr><tr><td></td><td>Measuregroup</td><td>Řetězec</td><td></td><td>Fyzický kontejner pro měření</td></tr><td>Klíčové ukazatele výkonu ("kpis")</td><td></td><td></td><td>Popis<p>Friendlyname<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace</td><td></td></tr><tr><td></td><td>Measuregroup</td><td>Řetězec</td><td></td><td>Fyzický kontejner pro měření</td></tr><tr><td></td><td>goalExpression</td><td>Řetězec</td><td></td><td>Číselný výraz MDX nebo výpočet, který vrací cílovou hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>valueExpression</td><td>Řetězec</td><td></td><td>Číselný výraz MDX, který vrací skutečnou hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>stavVýraz</td><td>Řetězec</td><td></td><td>Výraz MDX, který představuje stav klíčového ukazatele výkonu v zadaném okamžiku.</td></tr><tr><td></td><td>trendExpression</td><td>Řetězec</td><td></td><td>Výraz MDX, který vyhodnocuje hodnotu klíčového ukazatele výkonu v čase. Trendem může být libovolné kritérium založené na čase, které je užitečné v konkrétním obchodním kontextu.</td>
<tr><td>Zpráva ("zprávy")</td><td></td><td></td><td>Popis<p>Friendlyname<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje sestavu služby SQL Server Reporting Services. </td></tr><tr><td></td><td>assetCreatedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td>Kontejner ("kontejnery")</td><td></td><td></td><td>Popis<p>Friendlyname<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje kontejner jiných prostředků, jako je například databáze SQL, kontejner objektů Blobs Azure nebo model Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Typy poznámk
Typy anotací představují typy metadat, které lze přiřadit k jiným typům v rámci katalogu.

<table>
<tr><td><b>Typ poznámky (název vnořeného zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr>

<tr><td>Popis ("popisy")</td><td></td><td></td><td>Tato vlastnost obsahuje popis majetku. Každý uživatel systému může přidat svůj vlastní popis.  Objekt Description může upravovat pouze tento uživatel.  (Správci a vlastníci datových zdrojů mohou odstranit objekt Description, ale ne upravovat jej). Systém udržuje popisy uživatelů samostatně.  Proto je pole popisů na každém datovém zdroji (jeden pro každého uživatele, který přispěl své znalosti o datovém zdroji, kromě případně ten, který obsahuje informace odvozené ze zdroje dat).</td></tr>
<tr><td></td><td>description</td><td>řetězec</td><td>Stručný popis (2-3 řádky) majetku</td></tr>

<tr><td>Značka ("značky")</td><td></td><td></td><td>Tato vlastnost definuje značku pro datový zdroj. Každý uživatel systému může přidat více značek pro datový zdroj.  Upravovat je může pouze uživatel, který vytvořil objekty Značek.  (Správci a vlastníci datových zdrojů mohou objekt Tag odstranit, ale neupravovat). Systém udržuje značky uživatelů samostatně.  Proto je pole Tag objekty na každém datovém zdroji.</td></tr>
<tr><td></td><td>značka</td><td>řetězec</td><td>Značka popisující datový zdroj.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Tato vlastnost obsahuje popisný název datového zdroje. FriendlyName je jednotónová anotace – k datovému zdroji lze přidat pouze jeden název.  Upravovat jej může pouze uživatel, který vytvořil objekt FriendlyName. (Správci a vlastníci datových zdrojů mohou odstranit objekt FriendlyName, ale ne upravovat jej). Systém udržuje popisné názvy uživatelů samostatně.</td></tr>
<tr><td></td><td>Friendlyname</td><td>řetězec</td><td>Popisný název aktiva.</td></tr>

<tr><td>Schéma ("schéma")</td><td></td><td></td><td>Schéma popisuje strukturu dat.  Obsahuje seznam názvů atributů (sloupec, atribut, pole atd.), typů a dalších metadat.  Všechny tyto informace jsou odvozeny ze zdroje dat.  Schéma je ojedinělá anotace – pro datový zdroj lze přidat pouze jedno schéma.</td></tr>
<tr><td></td><td>columns</td><td>Sloupec[]</td><td>Pole objektů sloupců. Popisují sloupec s informacemi odvozenými ze zdroje dat.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Tato vlastnost obsahuje popis sloupce.  Každý uživatel systému může přidat své vlastní popisy pro více sloupců (maximálně jeden na sloupec). Upravovat je může pouze uživatel, který vytvořil objekty ColumnDescription.  (Správci a vlastníci datových zdrojů mohou odstranit objekt ColumnDescription, ale ne upravovat jej). Systém udržuje tyto popisy sloupců uživatele samostatně.  Proto je pole ColumnDescription objekty na každý datový zdroj (jeden na sloupec pro každého uživatele, který přispěl své znalosti o sloupci kromě případně ten, který obsahuje informace odvozené ze zdroje dat).  ColumnDescription je volně vázána na schéma, takže může získat mimo synchronizaci. ColumnDescription může popisovat sloupec, který již neexistuje ve schématu.  Je na zapisovateli, aby popis a schéma synchronizoval.  Zdroj dat může mít také informace o popisu sloupců a jsou to další objekty ColumnDescription, které by byly vytvořeny při spuštění nástroje.</td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který tento popis odkazuje.</td></tr>
<tr><td></td><td>description</td><td>Řetězec</td><td>krátký popis (2-3 řádky) sloupce.</td></tr>

<tr><td>ColumnTag ("sloupecTagy")</td><td></td><td></td><td>Tato vlastnost obsahuje značku pro sloupec. Každý uživatel systému může přidat více značek pro daný sloupec a může přidat značky pro více sloupců. Upravovat je může pouze uživatel, který vytvořil objekty ColumnTag. (Správci a vlastníci datových zdrojů mohou odstranit objekt ColumnTag, ale ne upravovat jej). Systém udržuje značky sloupců těchto uživatelů samostatně.  Proto je pole ColumnTag objekty na každém datovém zdroji.  ColumnTag je volně vázána na schéma, takže může získat mimo synchronizaci. Sloupec Sloupec, který již neexistuje ve schématu.  Je na zapisovateli, aby značku sloupce a schéma synchronizovali.</td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který tato značka odkazuje.</td></tr>
<tr><td></td><td>značka</td><td>Řetězec</td><td>Značka popisující sloupec.</td></tr>

<tr><td>Expert ("odborníci")</td><td></td><td></td><td>Tato vlastnost obsahuje uživatele, který je považován za odborníka v datové sadě. Názory odborníků (popisy) bublina na horní části UX při výpisu popisy. Každý uživatel může specifikovat své vlastní odborníky. Pouze tento uživatel může upravovat objekt odborníků. (Správci a vlastníci datových zdrojů mohou odstranit expertní objekty, ale ne upravovat je).</td></tr>
<tr><td></td><td>Expert</td><td>Zásada zabezpečení</td><td></td></tr>

<tr><td>Náhled ("náhledy")</td><td></td><td></td><td>Náhled obsahuje snímek z prvních 20 řádků dat pro datový zdroj. Náhled má smysl pouze pro některé typy datových zdrojů (má smysl pro tabulka, ale ne pro měření).</td></tr>
<tr><td></td><td>Náhled</td><td>objekt[]</td><td>Pole objektů, které představují sloupec.  Každý objekt má vlastnost mapování na sloupec s hodnotou pro tento sloupec pro řádek.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Mimetype</td><td>řetězec</td><td>Typ mimů obsahu.</td></tr>
<tr><td></td><td>content</td><td>řetězec</td><td>Pokyny, jak získat přístup k tomuto datovému prostředku. Obsahem může být adresa URL, e-mailová adresa nebo sada pokynů.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Počet řádků v sadě dat</td></tr>
<tr><td></td><td>velikost</td><td>long</td><td>Velikost v bajtů sady dat.  </td></tr>
<tr><td></td><td>schémaModifiedTime</td><td>řetězec</td><td>Při poslední úpravě schématu</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>řetězec</td><td>Poslední změna datové sady (data byla přidána, změněna nebo odstraněna)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Pole profilů dat sloupců.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který tato klasifikace odkazuje.</td></tr>
<tr><td></td><td>klasifikace</td><td>Řetězec</td><td>Klasifikace dat v tomto sloupci.</td></tr>

<tr><td>Dokumentace ("dokumentace")</td><td></td><td></td><td>K danému datovému zdroji může být přidružena pouze jedna dokumentace.</td></tr>
<tr><td></td><td>Mimetype</td><td>řetězec</td><td>Typ mimů obsahu.</td></tr>
<tr><td></td><td>content</td><td>řetězec</td><td>Obsah dokumentace.</td></tr>

</table>

### <a name="common-types"></a>Běžné typy
Běžné typy lze použít jako typy pro vlastnosti, ale nejsou Položky.

<table>
<tr><td><b>Běžný typ</b></td><td><b>Vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Sourcetype</td><td>řetězec</td><td>Popisuje typ zdroje dat.  Například: SQL Server, Oracle Database atd.  </td></tr>
<tr><td></td><td>Objecttype</td><td>řetězec</td><td>Popisuje typ objektu ve zdroji dat. Například: Tabulka, Zobrazení pro SQL Server.</td></tr>

<tr><td>Umístění zdroje dat</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokol</td><td>řetězec</td><td>Povinná hodnota. Popisuje protokol používaný ke komunikaci se zdrojem dat. Například: "tds" pro SQl Server, "oracle" pro Oracle, atd. Seznam aktuálně podporovaných protokolů naleznete v <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">části Referenční specifikace zdroje dat - Struktura DSL.</a></td></tr>
<tr><td></td><td>adresa</td><td>Slovníkový&lt;řetězec, objekt&gt;</td><td>Povinná hodnota. Adresa je sada dat specifická pro protokol, který se používá k identifikaci zdroje dat, na který se odkazuje. Data adresy s rozsahem na konkrétní protokol, což znamená, že je bezvýznamný bez znalosti protokolu.</td></tr>
<tr><td></td><td>ověřování</td><td>řetězec</td><td>Nepovinný parametr. Schéma ověřování používané ke komunikaci se zdrojem dat. Například: okna, oauth, atd.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Slovníkový&lt;řetězec, objekt&gt;</td><td>Nepovinný parametr. Další informace o připojení ke zdroji dat.</td></tr>

<tr><td>Zásada zabezpečení</td><td></td><td></td><td>Back-end neprovádí žádné ověření zadaných vlastností proti AAD během publikování.</td></tr>
<tr><td></td><td>Upn</td><td>řetězec</td><td>Jedinečná e-mailová adresa uživatele. Musí být zadán, pokud objectId není k dispozici nebo v kontextu "lastRegisteredBy" vlastnost, jinak volitelné.</td></tr>
<tr><td></td><td>Objectid</td><td>Identifikátor GUID</td><td>Identita AAD uživatele nebo skupiny zabezpečení. Nepovinný parametr. Musí být zadán, pokud upn není k dispozici, jinak volitelné.</td></tr>
<tr><td></td><td>firstName</td><td>řetězec</td><td>Jméno uživatele (pro účely zobrazení). Nepovinný parametr. Platí pouze v kontextu vlastnosti "lastRegisteredBy". Nelze zadat při poskytování zaregistrovaný objekt zabezpečení pro "role", "oprávnění" a "odborníci".</td></tr>
<tr><td></td><td>lastName</td><td>řetězec</td><td>Příjmení uživatele (pro účely zobrazení). Nepovinný parametr. Platí pouze v kontextu vlastnosti "lastRegisteredBy". Nelze zadat při poskytování zaregistrovaný objekt zabezpečení pro "role", "oprávnění" a "odborníci".</td></tr>

<tr><td>Sloupec</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název sloupce nebo atributu.</td></tr>
<tr><td></td><td>type</td><td>řetězec</td><td>datový typ sloupce nebo atributu. Přípustné typy závisí na zdroji datTyp datového zdroje.  Podporována je pouze podmnožina typů.</td></tr>
<tr><td></td><td>Maxlength</td><td>int</td><td>Maximální délka povolená pro sloupec nebo atribut. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>Přesnost</td><td>byte</td><td>Přesnost pro sloupec nebo atribut. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>Isnullable</td><td>Logická hodnota</td><td>Určuje, zda je sloupec povolen jako nulová hodnota nebo ne. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>výraz</td><td>řetězec</td><td>Pokud je hodnota počítaný sloupec, obsahuje toto pole výraz, který hodnotu vyjadřuje. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>řetězec</td><td>Název sloupce</td></tr>
<tr><td></td><td>type </td><td>řetězec</td><td>Typ sloupce</td></tr>
<tr><td></td><td>min </td><td>řetězec</td><td>Minimální hodnota v souboru dat</td></tr>
<tr><td></td><td>max </td><td>řetězec</td><td>Maximální hodnota v sadě dat</td></tr>
<tr><td></td><td>Průměrná </td><td>double</td><td>Průměrná hodnota v sadě dat</td></tr>
<tr><td></td><td>Stdev </td><td>double</td><td>Směrodatná odchylka pro soubor dat</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Počet hodnot null v sadě dat</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Počet odlišných hodnot v sadě dat</td></tr>


</table>

## <a name="asset-identity"></a>Identita majetku
Azure Data Catalog používá "protokol" a vlastnosti identity z "adresa" vlastnosti DataSourceLocation "dsl" vlastnost generovat identitu prostředku, který se používá k adresování prostředku uvnitř katalogu.
Například protokol "tds" má vlastnosti identity "server", "databáze", "schéma" a "objekt". Kombinace protokolu a vlastnosti identity se používají ke generování identity SQL Server Tabulka Asset.
Azure Data Catalog poskytuje několik integrovaných protokolů zdroje dat, které jsou uvedeny na [stránce Referenční specifikace zdroje dat - STRUKTURA DSL](data-catalog-dsr.md).
Sadu podporovaných protokolů lze rozšířit programově (viz odkaz na odkaz REST API katalogu dat). Správci katalogu mohou registrovat vlastní protokoly zdroje dat. Následující tabulka popisuje vlastnosti potřebné k registraci vlastního protokolu.

### <a name="custom-data-source-protocol-specification"></a>Specifikace vlastního protokolu zdroje dat
<table>
<tr><td><b>Typ</b></td><td><b>Vlastnosti</b></td><td><b>Typ dat</b></td><td><b>Komentáře</b></td></tr>

<tr><td>Protokol DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>řetězec</td><td>Obor názvů protokolu. Obor názvů musí mít dlouhý 1 až 255 znaků, musí obsahovat jednu nebo více neprázdných částí oddělených tečkou (.). Každá část musí mít dlouhý počet 1 až 255 znaků, musí začínat písmenem a obsahovat pouze písmena a číslice.</td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název protokolu. Název musí mít dlouhý 1 až 255 znaků, musí začínat písmenem a obsahovat pouze písmena, číslice a znak pomlčky (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>PropertySourceProtocolIdentityProperty[]</td><td>Seznam vlastností identity musí obsahovat alespoň jednu, ale ne více než 20 vlastností. Příklad: "server", "databáze", "schéma", "objekt" jsou vlastnosti identity protokolu "tds".</td></tr>
<tr><td></td><td>sady identit</td><td>Sada IdentityProtokoluYSet datového zdroje[]</td><td>Seznam sad identit. Definuje sady vlastností identity, které představují identitu platného majetku. Musí obsahovat alespoň jednu, ale ne více než 20 sad. Příklad: {"server", "database", "schema" a "object"} je sada identit pro protokol "tds", který definuje identitu datového zdroje Sql Server Table.</td></tr>

<tr><td>Vlastnost Identity protokolu DataSourceProtocolIdentity</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název vlastnosti Název musí mít dlouhý 1 až 100 znaků, musí začínat písmenem a může obsahovat pouze písmena a číslice.</td></tr>
<tr><td></td><td>type</td><td>řetězec</td><td>Typ proměnné Podporované hodnoty: "bool", boolean", "byte", "guid", "int", "integer", "long", "string", "url"</td></tr>
<tr><td></td><td>Ignorecase</td><td>bool</td><td>Označuje, zda by měl být případ ignorován při použití hodnoty vlastnosti. Lze zadat pouze pro vlastnosti s typem "řetězec". Výchozí hodnota je false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnorCase</td><td>bool[]</td><td>Označuje, zda má být případ ignorován pro každý segment cesty adresy URL. Lze zadat pouze pro vlastnosti s typem "url". Výchozí hodnota je [false].</td></tr>

<tr><td>Sada IdentityProtokoluYSet datasourceprotocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název sady identit.</td></tr>
<tr><td></td><td>properties</td><td>řetězec[]</td><td>Seznam vlastností identity zahrnutých do této sady identit. Nemůže obsahovat duplikáty. Každá vlastnost odkazovaná sadou identit musí být definována v seznamu "identityProperties" protokolu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role a autorizace
Katalog dat Microsoft Azure poskytuje možnosti autorizace pro operace CRUD s prostředky a anotacemi.

## <a name="key-concepts"></a>Klíčové koncepty
Katalog dat Azure používá dva autorizační mechanismy:

* Ověřování založené na rolích
* Autorizace založená na oprávněních

### <a name="roles"></a>Role
Existují tři role: **Správce**, **Vlastník**a **Přispěvatel**.  Každá role má svůj rozsah a práva, které jsou shrnuty v následující tabulce.

<table><tr><td><b>Role</b></td><td><b>Rozsah</b></td><td><b>Práva</b></td></tr><tr><td>Správce</td><td>Katalog (všechny datové zdroje/poznámky v katalogu)</td><td>Číst článek Odstranit role zobrazení

ChangeOwnership ChangeVisibility Oprávnění zobrazeníOprávnění</td></tr><tr><td>Vlastník</td><td>Každý datový zdroj (kořenová položka)</td><td>Číst článek Odstranit role zobrazení

ChangeOwnership ChangeVisibility Oprávnění zobrazeníOprávnění</td></tr><tr><td>Přispěvatel</td><td>Každý jednotlivý majetek a anotace</td><td>Číst článek Aktualizovat Odstranit ViewRoles Poznámka: všechna práva jsou odvolána, pokud je právo na čtení položky odvoláno od přispěvatele</td></tr></table>

> [!NOTE]
> **Čtení**, **Aktualizace**, **Odstranit**, **ViewRoles** práva jsou použitelné pro všechny položky (datový zdroj nebo poznámky), zatímco **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** jsou použitelné pouze pro kořenový datový zdroj.
> 
> **Odstranit** právo se vztahuje na položku a všechny podpoložky nebo jednu položku pod ní. Například odstranění matážního prostředku také odstraní všechny poznámky pro tento datový zdroj.
> 
> 

### <a name="permissions"></a>Oprávnění
Oprávnění je jako seznam položek řízení přístupu. Každá položka řízení přístupu přiřazuje sadu práv objektu zabezpečení. Oprávnění lze zadat pouze u datového zdroje (tj. kořenové položky) a platí pouze pro datový zdroj a všechny podpoložky.

Během náhledu **katalogu dat Azure** je v seznamu oprávnění podporováno jenom právo **čtení,** aby se scénář omezil na viditelnost datového zdroje.

Ve výchozím nastavení má každý ověřený uživatel **právo číst** pro libovolnou položku v katalogu, pokud není viditelnost omezena na sadu objektů zabezpečení v oprávněních.

## <a name="rest-api"></a>REST API
**Požadavky** na položky zobrazení PUT a **POST** lze použít k řízení rolí a oprávnění: kromě datové části položky lze zadat dvě systémové vlastnosti **role** a **oprávnění**.

> [!NOTE]
> **oprávnění** platná pouze pro kořenovou položku.
> 
> **Role vlastníka** platí pouze pro kořenovou položku.
> 
> Ve výchozím nastavení, když je položka vytvořena v katalogu, její **přispěvatel** je nastaven na aktuálně ověřeného uživatele. Pokud by měla být položka aktualizovatelná všemi &lt;&gt; uživateli, měl by být **přispěvatel** nastaven na zvláštní zaregistrovaný objekt zabezpečení Everyone ve **vlastnosti role** při prvním publikování položky (viz následující příklad). **Přispěvatele** nelze změnit a zůstane stejný po celou dobu životnosti položky (ani **správce** nebo **vlastník** nemá právo na změnu **přispěvatele).** Jedinou podporovanou hodnotou pro explicitní &lt;&gt;nastavení **přispěvatele** je Everyone : **Přispěvatelem** může být pouze uživatel, který vytvořil položku, nebo &lt;Everyone&gt;.
> 
> 

### <a name="examples"></a>Příklady
**Při publikování &lt;&gt; položky nastavte přispěvatele na všechny.**
Zvláštní zabezpečení &lt;&gt; zabezpečení Každý má objectId "00000000-0000-0000-0000-000000000000201".
  **PŘÍSPĚVEK** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Některé implementace klientů HTTP může automaticky znovu vystavit požadavky v reakci na 302 ze serveru, ale obvykle strip autorizace záhlaví z požadavku. Vzhledem k tomu, že je vyžadována hlavička autorizace k provádění požadavků do katalogu dat Azure, musíte zajistit, aby se hlavička autorizace stále poskytovala při opětovném vydání požadavku na umístění přesměrování určeného katalogem dat Azure. Následující ukázkový kód jej demonstruje pomocí objektu HttpWebRequest rozhraní .NET.
> 
> 

**Text**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Přiřaďte vlastníkům a omezte viditelnost pro existující kořenovou položku**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> V put není nutné zadat datové části položky v těle: PUT lze použít k aktualizaci pouze role nebo oprávnění.
> 
