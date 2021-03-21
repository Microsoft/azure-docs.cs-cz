---
title: Azure Data Catalog koncepce pro vývojáře
description: Úvod do klíčových konceptů v Azure Data Catalog koncepční model, který je zveřejněný prostřednictvím REST API katalogu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674695"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Data Catalog koncepce pro vývojáře

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Microsoft **Azure Data Catalog** je plně spravovaná cloudová služba, která poskytuje možnosti pro zjišťování zdrojů dat a pro metadata crowdsourcingový zdrojů dat. Vývojáři můžou službu používat přes rozhraní REST API. Porozumění konceptům implementovaným ve službě je důležité, aby se vývojáři mohli úspěšně integrovat s **Azure Data Catalog**.

## <a name="key-concepts"></a>Klíčové koncepty 
**Azure Data Catalog** koncepční model vychází ze čtyř klíčových konceptů: **katalogu**, **uživatelů**, **assetů** a **poznámek**.

![Ilustrace Azure Data Catalog koncepčního modelu](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Katalog
**Katalog** je kontejner nejvyšší úrovně pro všechna metadata, která organizace obchoduje. Pro každý účet Azure je povolený jeden **katalog** . Katalogy jsou vázané na předplatné Azure, ale pro libovolný účet Azure se dá vytvořit jenom jeden **katalog** , a to i v případě, že účet může mít víc předplatných.

Katalog obsahuje **uživatele** a **prostředky**.

### <a name="users"></a>Uživatelé
Uživatelé jsou objekty zabezpečení, které mají oprávnění k provádění akcí (hledání v katalogu, přidávání, upravování nebo odebírání položek atd.) v katalogu.

Uživatel může mít několik různých rolí. Informace o rolích najdete v části role a autorizace.

Jednotlivé uživatele a skupiny zabezpečení je možné přidat.

Azure Data Catalog používá Azure Active Directory pro správu identit a přístupu. Každý uživatel katalogu musí být členem služby Active Directory pro daný účet.

### <a name="assets"></a>Prostředky
**Katalog** obsahuje datové assety. **Prostředky** jsou jednotka podrobností spravovaná katalogem.

Členitost assetů se liší podle zdroje dat. V případě SQL Server nebo Oracle Database může být Assetem tabulka nebo zobrazení. V případě Služba Analysis Services serveru SQL může být Assetem míra, dimenze nebo klíčový ukazatel výkonu (KPI). V případě SQL Server Reporting Services je Assetem sestava.

**Asset** je věcí, kterou přidáte nebo odeberete z katalogu. Je to jednotka výsledku, kterou vrátíte ze služby **Search**.

**Asset** se skládá z jeho názvu, umístění a typu a poznámek, které ho podrobněji popisují.

### <a name="annotations"></a>Poznámky
Poznámky jsou položky, které představují metadata o prostředcích.

Příklady poznámek jsou popis, značky, schéma, dokumentace atd. Úplný seznam typů assetů a typů poznámek najdete v [části model objektu prostředku](#asset-object-model) .

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Poznámky k crowdsourcingový a perspektiva uživatelů (násobnost stanoviska)
Klíčovým aspektem Azure Data Catalog je, jak podporuje crowdsourcingový metadat v systému. Na rozdíl od přístupu na wikiwebu – kde je jenom jedno stanovisko a poslední zapisovač WINS – model Azure Data Catalog umožňuje, aby se v systému v reálném čase nacházelo více stanovisek.

Tento přístup odráží reálný svět podnikových dat, kde různí uživatelé můžou mít pro určitý prostředek různé perspektivy:

* Správce databáze může poskytovat informace o smlouvách o úrovni služeb nebo dostupném časovém období pro hromadné operace ETL.
* Steward dat může poskytovat informace o obchodních procesech, na které se vztahuje daný prostředek, nebo o klasifikacích, které se na něj v podniku vztahují.
* Finanční analytik může poskytnout informace o tom, jak se data používají během úloh generování sestav na konci období.

Pro podporu tohoto příkladu může každý uživatel – DBA, data Steward a analytik – přidat popis do jedné tabulky, která byla zaregistrována v katalogu. Všechny popisy se udržují v systému a na portálu Azure Data Catalog se zobrazí všechny popisy.

Tento model je aplikován na většinu položek v objektovém modelu, takže typy objektů v datové části JSON jsou často pole pro vlastnosti, kde je možné očekávat typ singleton.

Například v rámci kořenového adresáře assetu je pole objektů Description. Vlastnost Array se nazývá descriptions. Objekt Description má jednu vlastnost-Description. Vzor je v tom, že každý uživatel, který má typ Description, získá objekt Description, který byl vytvořen pro hodnotu poskytnutou uživatelem.

UŽIVATELSKÉ prostředí pak může zvolit, jak se má kombinace zobrazit. Existují tři různé vzory pro zobrazení.

* Nejjednodušším vzorem je "Zobrazit vše". V tomto vzoru jsou všechny objekty zobrazeny v zobrazení seznamu. UŽIVATELSKÉ rozhraní portálu Azure Data Catalog používá tento vzor pro popis.
* Dalším vzorem je "sloučení". V tomto modelu jsou všechny hodnoty různých uživatelů sloučeny společně s duplicitními odebranými. Příklady tohoto modelu v uživatelském prostředí portálu Azure Data Catalog jsou značky a odborné vlastnosti.
* Třetí vzor je "poslední zapisovač WINS". V tomto modelu je zobrazená jenom poslední hodnota zadaná v poli. friendlyName je příkladem tohoto modelu.

## <a name="asset-object-model"></a>Objektový model prostředků
Jak je uvedeno v části klíčové koncepty, model **Azure Data Catalogho** objektu obsahuje položky, které mohou být prostředky nebo anotace. Položky mají vlastnosti, které mohou být volitelné nebo povinné. Některé vlastnosti se vztahují na všechny položky. Některé vlastnosti se vztahují na všechny prostředky. Některé vlastnosti se vztahují pouze na konkrétní typy prostředků.

### <a name="system-properties"></a>Systémové vlastnosti
<table><tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr><tr><td>časové razítko</td><td>DateTime</td><td>Čas poslední změny položky Toto pole je generováno serverem při vložení položky a pokaždé, když je položka aktualizována. Hodnota této vlastnosti se ignoruje při vstupu operací publikování.</td></tr><tr><td>ID</td><td>Identifikátor URI</td><td>Absolutní adresa URL položky (jen pro čtení) Jedná se o jedinečný adresovatelný identifikátor URI pro položku.  Hodnota této vlastnosti se ignoruje při vstupu operací publikování.</td></tr><tr><td>typ</td><td>Řetězec</td><td>Typ prostředku (jen pro čtení).</td></tr><tr><td>značk</td><td>Řetězec</td><td>Řetězec odpovídající verzi položky, kterou lze použít pro optimistické řízení souběžnosti při provádění operací, které aktualizují položky v katalogu. "*" lze použít k vyhledání libovolné hodnoty.</td></tr></table>

### <a name="common-properties"></a>Společné vlastnosti
Tyto vlastnosti se vztahují na všechny typy kořenového prostředku a všechny typy poznámek.

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logická hodnota</td><td>Určuje, zda jsou data položky odvozena ze zdrojového systému (jako je SQL Server databáze, Oracle Database) nebo vytvořená uživatelem.</td></tr>
</table>

### <a name="common-root-properties"></a>Společné kořenové vlastnosti
<p>
Tyto vlastnosti se vztahují na všechny typy kořenových prostředků.

<table><tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr><tr><td>name</td><td>Řetězec</td><td>Název odvozený z informací o umístění zdroje dat</td></tr><tr><td>pevná</td><td>DataSourceLocation</td><td>Jedinečně popisuje zdroj dat a je jedním z identifikátorů pro daný prostředek. (Viz oddíl Dual identity).  Struktura DSL se liší podle protokolu a typu zdroje.</td></tr><tr><td>Datového</td><td>DataSourceInfo</td><td>Další podrobnosti o typu assetu.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Popisuje uživatele, který tento Asset naposledy zaregistroval.  Obsahuje jedinečné ID pro uživatele (UPN) a zobrazované jméno (lastName a firstName).</td></tr><tr><td>containerID</td><td>Řetězec</td><td>ID prostředku kontejneru pro zdroj dat Tato vlastnost není pro typ kontejneru podporována.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Běžné vlastnosti anotace nesouvisející s jedním prvkem
Tyto vlastnosti se vztahují na všechny typy poznámek bez typu Singleton (poznámky, které mohou být pro jednotlivé prostředky povoleny více).

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>key</td><td>Řetězec</td><td>Uživatelem zadaný klíč, který jedinečně identifikuje anotaci v aktuální kolekci. Délka klíče nesmí překročit 256 znaků.</td></tr>
</table>

### <a name="root-asset-types"></a>Typy kořenového prostředku
Typy kořenového prostředku jsou tyto typy, které představují různé typy datových assetů, které mohou být registrovány v katalogu. U každého kořenového typu existuje zobrazení, které popisuje prostředky a poznámky, které jsou zahrnuty v zobrazení. Při publikování assetu pomocí REST API by měl být v odpovídajícím segmentu adresy URL {view_name} použit název zobrazení.

<table><tr><td><b>Typ prostředku (název zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Povolené poznámky</b></td><td><b>Komentáře</b></td></tr><tr><td>Tabulka ("tabulky")</td><td></td><td></td><td>Description<p>FriendlyName<p>Značka<p>Schéma<p>ColumnDescription<p>ColumnTag<p> Odborník<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentace<p></td><td>Tabulka představuje jakákoli tabulková data.  Například: tabulka SQL, zobrazení SQL, Analysis Services tabulková tabulka, Analysis Services multidimenzionální dimenze, tabulka Oracle atd.   </td></tr><tr><td>Measure ("míry")</td><td></td><td></td><td>Description<p>FriendlyName<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje míru Analysis Services.</td></tr><tr><td></td><td>měrné</td><td>Sloupec</td><td></td><td>Metadata popisující míru</td></tr><tr><td></td><td>-Počítané </td><td>Logická hodnota</td><td></td><td>Určuje, zda je míra vypočítána.</td></tr><tr><td></td><td>Skupina</td><td>Řetězec</td><td></td><td>Fyzický kontejner pro měření</td></tr><td>Klíčový ukazatel výkonu (KPI)</td><td></td><td></td><td>Description<p>FriendlyName<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace</td><td></td></tr><tr><td></td><td>Skupina</td><td>Řetězec</td><td></td><td>Fyzický kontejner pro měření</td></tr><tr><td></td><td>goalExpression</td><td>Řetězec</td><td></td><td>Číselný výraz MDX nebo výpočet, který vrací cílovou hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>valueExpression</td><td>Řetězec</td><td></td><td>Číselný výraz MDX, který vrací skutečnou hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>statusExpression</td><td>Řetězec</td><td></td><td>Výraz MDX, který představuje stav klíčového ukazatele výkonu v určeném časovém okamžiku.</td></tr><tr><td></td><td>trendExpression</td><td>Řetězec</td><td></td><td>Výraz MDX, který vyhodnocuje hodnotu klíčového ukazatele výkonu v čase. Trend může být libovolné kritérium založené na čase, které je užitečné v konkrétním obchodním kontextu.</td>
<tr><td>Sestava ("sestavy")</td><td></td><td></td><td>Description<p>FriendlyName<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje sestavu SQL Server Reporting Services. </td></tr><tr><td></td><td>assetCreatedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td>Kontejner ("kontejnery")</td><td></td><td></td><td>Description<p>FriendlyName<p>Značka<p>Odborník<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ představuje kontejner dalších prostředků, jako je SQL Database, kontejner objektů blob Azure nebo model Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Typy poznámek
Typy poznámek reprezentují typy metadat, které mohou být přiřazeny jiným typům v rámci katalogu.

<table>
<tr><td><b>Typ anotace (název vnořeného zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>

<tr><td>Popis ("popisy")</td><td></td><td></td><td>Tato vlastnost obsahuje popis prostředku. Každý uživatel systému může přidat svůj vlastní popis.  Pouze tento uživatel může upravit objekt Description.  (Správci a vlastníci prostředků mohou odstranit objekt Description, ale Neupravovat ho). Systém uchovává popisy uživatelů samostatně.  Proto je k dispozici pole popisů každého assetu (jeden pro každého uživatele, který přispěl ke své znalosti o assetu, kromě možného toho, který obsahuje informace odvozené ze zdroje dat).</td></tr>
<tr><td></td><td>description</td><td>řetězec</td><td>Krátký popis (2-3 řádků) prostředku</td></tr>

<tr><td>Tag ("značky")</td><td></td><td></td><td>Tato vlastnost definuje značku pro určitý Asset. Každý uživatel systému může do prostředku přidat více značek.  Pouze uživatel, který vytvořil objekty značek, je může upravovat.  (Správci a vlastníci prostředků mohou odstranit objekt značky, ale Neupravovat ho). Systém uchovává značky uživatelů samostatně.  Proto je v každém prostředku pole objektů značek.</td></tr>
<tr><td></td><td>značka</td><td>řetězec</td><td>Značka popisující prostředek.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Tato vlastnost obsahuje popisný název assetu. FriendlyName je pouze Poznámka typu Singleton – k assetu lze přidat pouze jeden parametr FriendlyName.  Pouze uživatel, který vytvořil objekt FriendlyName, ho může upravit. (Správci a vlastníci prostředků mohou odstranit objekt FriendlyName, ale Neupravovat ho). Systém udržuje popisné názvy uživatelů samostatně.</td></tr>
<tr><td></td><td>friendlyName</td><td>řetězec</td><td>Popisný název assetu.</td></tr>

<tr><td>Schéma ("Schema")</td><td></td><td></td><td>Schéma popisuje strukturu dat.  Obsahuje seznam názvů (sloupec, atribut, pole atd.), typy i další metadata.  Tyto informace jsou odvozeny ze zdroje dat.  Schéma je anotace typu Singleton – pro určitý prostředek lze přidat pouze jedno schéma.</td></tr>
<tr><td></td><td>columns</td><td>Sloupec []</td><td>Pole objektů sloupce. Popisují sloupec s informacemi odvozenými ze zdroje dat.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Tato vlastnost obsahuje popis sloupce.  Každý uživatel systému může přidat vlastní popisy pro více sloupců (maximálně jeden pro každý sloupec). Pouze uživatel, který vytvořil objekty ColumnDescription, je může upravit.  (Správci a vlastníci prostředků mohou odstranit objekt ColumnDescription, ale ne ho upravovat). Systém uchovává popisy sloupců těchto uživatelů samostatně.  Proto je k dispozici pole objektů ColumnDescription u každého assetu (jeden pro každý sloupec pro každého uživatele, který přispěl jejich znalosti o sloupci kromě možného toho, který obsahuje informace odvozené ze zdroje dat).  ColumnDescription se dá volně svázat se schématem, aby se mohl dostat mimo synchronizaci. ColumnDescription může popsat sloupec, který už ve schématu neexistuje.  Je až do zapisovače, aby bylo možné zachovat popis a schéma v synchronizaci.  Zdroj dat může mít také informace o popiscích sloupců a jsou další objekty ColumnDescription, které by se vytvořily při spuštění tohoto nástroje.</td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který tento popis odkazuje</td></tr>
<tr><td></td><td>description</td><td>Řetězec</td><td>krátký popis (2-3 řádků) sloupce.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Tato vlastnost obsahuje značku pro sloupec. Každý uživatel systému může do daného sloupce přidat více značek a může přidat značky pro více sloupců. Pouze uživatel, který vytvořil objekty ColumnTag, je může upravit. (Správci a vlastníci prostředků mohou odstranit objekt ColumnTag, ale ne ho upravovat). Systém zachovává značky sloupců těchto uživatelů samostatně.  Proto je pro každý Asset k dispozici pole ColumnTag objektů.  ColumnTag se dá volně svázat se schématem, aby se mohl dostat mimo synchronizaci. ColumnTag může popsat sloupec, který už ve schématu neexistuje.  Je až do zapisovače, aby bylo možné zachovat značku sloupce a schéma v synchronizaci.</td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který se tato značka odkazuje</td></tr>
<tr><td></td><td>značka</td><td>Řetězec</td><td>Značka popisující sloupec</td></tr>

<tr><td>Expert (experti)</td><td></td><td></td><td>Tato vlastnost obsahuje uživatele, který se považuje za odborníka v datové sadě. Bublinová stanoviska expertů (popisy) k hornímu okraji uživatelského rozhraní při výpisu popisu. Každý uživatel může zadat své odborníky. Pouze tento uživatel může upravit objekt Experts. (Správci a vlastníci prostředků mohou odstranit odborníka na objekty, ale ne upravovat).</td></tr>
<tr><td></td><td>odborník</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Náhled ("verze Preview")</td><td></td><td></td><td>Verze Preview obsahuje snímek prvních 20 řádků dat pro daný prostředek. Používejte jenom náhled pro některé typy prostředků (dává smysl pro tabulku, ale ne pro míru).</td></tr>
<tr><td></td><td>preview</td><td>objekt []</td><td>Pole objektů, které reprezentují sloupec  Každý objekt má mapování vlastností na sloupec s hodnotou pro tento sloupec pro řádek.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>řetězec</td><td>Typ MIME obsahu</td></tr>
<tr><td></td><td>obsah</td><td>řetězec</td><td>Pokyny, jak získat přístup k tomuto datovému assetu. Obsahem může být adresa URL, e-mailová adresa nebo sada instrukcí.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Počet řádků v sadě dat</td></tr>
<tr><td></td><td>size</td><td>long</td><td>Velikost datové sady v bajtech  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>řetězec</td><td>Čas poslední úpravy schématu</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>řetězec</td><td>Čas poslední změny datové sady (přidání, změna nebo odstranění dat)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columns</td></td><td>ColumnDataProfile[]</td><td>Pole profilů dat sloupců</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Řetězec</td><td>Název sloupce, na který se tato klasifikace odkazuje</td></tr>
<tr><td></td><td>klasifikace</td><td>Řetězec</td><td>Klasifikace dat v tomto sloupci</td></tr>

<tr><td>Dokumentace ("dokumentace")</td><td></td><td></td><td>K danému prostředku může být přidružena pouze jedna dokumentace.</td></tr>
<tr><td></td><td>mimeType</td><td>řetězec</td><td>Typ MIME obsahu</td></tr>
<tr><td></td><td>obsah</td><td>řetězec</td><td>Obsah dokumentace</td></tr>

</table>

### <a name="common-types"></a>Běžné typy
Společné typy lze použít jako typy pro vlastnosti, ale nejsou položkami.

<table>
<tr><td><b>Společný typ</b></td><td><b>Vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Zdroje</td><td>řetězec</td><td>Popisuje typ zdroje dat.  Například: SQL Server, Oracle Database atd.  </td></tr>
<tr><td></td><td>objectType</td><td>řetězec</td><td>Popisuje typ objektu ve zdroji dat. Například: Table, View for SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokol</td><td>řetězec</td><td>Povinná hodnota. Popisuje protokol, který se používá ke komunikaci se zdrojem dat. Například: `tds` pro SQL Server pro `oracle` Oracle atd. Seznam aktuálně podporovaných protokolů najdete v tématu [specifikace odkazu na zdroj dat – struktura DSL](data-catalog-dsr.md) .</td></tr>
<tr><td></td><td>adresa</td><td>&lt;Řetězec slovníku, objekt&gt;</td><td>Povinná hodnota. Adresa je sada dat specifických pro protokol, který se používá k identifikaci odkazovaného zdroje dat. Data adresy jsou v oboru pro určitý protokol, což znamená, že nemají žádný význam.</td></tr>
<tr><td></td><td>ověřování</td><td>řetězec</td><td>Nepovinný parametr. Schéma ověřování používané ke komunikaci se zdrojem dat. Například: Windows, OAuth atd.</td></tr>
<tr><td></td><td>connectionProperties</td><td>&lt;Řetězec slovníku, objekt&gt;</td><td>Nepovinný parametr. Další informace o tom, jak se připojit ke zdroji dat</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Back-end neprovádí žádné ověření poskytovaných vlastností proti Azure Active Directory během publikování.</td></tr>
<tr><td></td><td>názvu</td><td>řetězec</td><td>Jedinečná e-mailová adresa uživatele Je nutné zadat, pokud není zadáno objectId nebo v kontextu vlastnosti "lastRegisteredBy", jinak volitelné.</td></tr>
<tr><td></td><td>objectId</td><td>Identifikátor GUID</td><td>Uživatel nebo skupina zabezpečení Azure Active Directory identitu. Nepovinný parametr. Je nutné zadat, pokud není zadán hlavní název uživatele (UPN), jinak volitelné.</td></tr>
<tr><td></td><td>firstName</td><td>řetězec</td><td>Křestní jméno uživatele (pro účely zobrazení). Nepovinný parametr. Platné pouze v kontextu vlastnosti "lastRegisteredBy". Nelze zadat při poskytování objektu zabezpečení pro role, oprávnění a odborníky.</td></tr>
<tr><td></td><td>lastName</td><td>řetězec</td><td>Poslední jméno uživatele (pro účely zobrazení). Nepovinný parametr. Platné pouze v kontextu vlastnosti "lastRegisteredBy". Nelze zadat při poskytování objektu zabezpečení pro role, oprávnění a odborníky.</td></tr>

<tr><td>Sloupec</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>řetězec</td><td>Název sloupce nebo atributu</td></tr>
<tr><td></td><td>typ</td><td>řetězec</td><td>datový typ sloupce nebo atributu Povolené typy závisí na datovém sourceType prostředku.  Je podporována pouze podmnožina typů.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Maximální povolená délka sloupce nebo atributu. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>Přesnost</td><td>byte</td><td>Přesnost sloupce nebo atributu Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>isNullable</td><td>Logická hodnota</td><td>Určuje, zda má sloupec povolenou hodnotu null. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>
<tr><td></td><td>expression</td><td>řetězec</td><td>Pokud je hodnotou počítaný sloupec, obsahuje toto pole výraz, který hodnotu vyjadřuje. Odvozeno ze zdroje dat. Platí pouze pro některé typy zdrojů.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>řetězec</td><td>Název sloupce</td></tr>
<tr><td></td><td>typ </td><td>řetězec</td><td>Typ sloupce</td></tr>
<tr><td></td><td>min </td><td>řetězec</td><td>Minimální hodnota v sadě dat</td></tr>
<tr><td></td><td>max </td><td>řetězec</td><td>Maximální hodnota v sadě dat</td></tr>
<tr><td></td><td>volání </td><td>double</td><td>Průměrná hodnota v sadě dat</td></tr>
<tr><td></td><td>STDEVA </td><td>double</td><td>Směrodatná odchylka pro datovou sadu</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Počet hodnot null v datové sadě</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Počet jedinečných hodnot v datové sadě</td></tr>
</table>

## <a name="asset-identity"></a>Identita prostředku
Azure Data Catalog používá "protokol" a vlastnosti identity z kontejneru vlastností "adresa" vlastnosti "DSL" pro vygenerování identity assetu, který se používá k adresování prostředků v katalogu.
Například protokol TDS (Tabular data Stream) má vlastnosti identity "Server", "Database", "Schema" a "Object". Kombinace protokolu a vlastností identity se používají ke generování identity Assetu SQL Server tabulky.
Azure Data Catalog poskytuje několik integrovaných protokolů zdroje dat, které jsou uvedeny ve [struktuře dat referenční specifikace-DSL](data-catalog-dsr.md).
Sadu podporovaných protokolů lze programově rozšířit (viz odkaz Data Catalog REST API). Správci katalogu můžou registrovat vlastní protokoly zdrojů dat. Následující tabulka popisuje vlastnosti potřebné k registraci vlastního protokolu.

### <a name="custom-data-source-protocol-specification"></a>Vlastní specifikace protokolu pro zdroj dat
<table>
<tr><td><b>Typ</b></td><td><b>Vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>řetězec</td><td>Obor názvů protokolu. Obor názvů musí být dlouhý 1 až 255 znaků a musí obsahovat jednu nebo více neprázdných částí oddělených tečkou (.). Každá část musí mít délku 1 až 255 znaků. začínat písmenem a obsahovat jenom písmena a číslice.</td></tr>
<tr><td></td><td>name</td><td>řetězec</td><td>Název protokolu. Název musí mít délku 1 až 255 znaků a nesmí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Seznam vlastností identity musí obsahovat aspoň jednu, ale ne víc než 20 vlastností. Například: "Server", "Database", "Schema", "Object" jsou vlastnosti identity protokolu "TDS".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Seznam sad identit Definuje sady vlastností identity, které reprezentují platnou identitu prostředku. Musí obsahovat alespoň jeden, ale ne více než 20 sad. Například: {"Server", "Database", "Schema" a "Object"} je sada identit pro protokol TDS, která definuje identitu SQL Server assetů tabulky.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>řetězec</td><td>Název vlastnosti Název musí mít délku 1 až 100 znaků a nesmí obsahovat jenom písmena a číslice.</td></tr>
<tr><td></td><td>typ</td><td>řetězec</td><td>Typ proměnné Podporované hodnoty: "bool", Boolean "," byte "," GUID "," int "," Integer "," Long "," String "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Určuje, zda má být při použití hodnoty vlastnosti ignorována velikost písmen. Dá se zadat jenom pro vlastnosti typu řetězec. Výchozí hodnota je false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Označuje, zda má být pro každý segment cesty adresy URL ignorována velikost písmen. Lze zadat pouze pro vlastnosti typu "URL". Výchozí hodnota je [FALSE].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>řetězec</td><td>Název sady identity</td></tr>
<tr><td></td><td>properties</td><td>řetězec []</td><td>Seznam vlastností identity zahrnutých do této sady identit Nemůže obsahovat duplicity. Každá vlastnost, na kterou odkazuje sada identity, musí být definovaná v seznamu "identityProperties" protokolu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role a autorizace
Microsoft Azure Data Catalog poskytuje možnosti autorizace pro operace CRUD pro prostředky a poznámky.

Azure Data Catalog používá dva autorizační mechanismy:

* Ověřování založené na rolích
* Autorizace na základě oprávnění

### <a name="roles"></a>Role
Existují tři role: **správce**, **vlastník** a **Přispěvatel**.  Každá role má svůj rozsah a práva, které jsou shrnuty v následující tabulce.

<table><tr><td><b>Role</b></td><td><b>Scope</b></td><td><b>Rights</b></td></tr><tr><td>Správce</td><td>Katalog (všechny prostředky a poznámky v katalogu)</td><td>Čtení odstranit ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Vlastník</td><td>Každý prostředek (kořenová položka)</td><td>Čtení odstranit ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Přispěvatel</td><td>Každý jednotlivý prostředek a Poznámka</td><td>Přečíst aktualizace odstranit ViewRoles Poznámka: všechna práva se odvolají, pokud je právo Číst u položky odvoláno od přispěvatele.</td></tr></table>

> [!NOTE]
> Oprávnění **ke čtení**, **aktualizaci**, **odstranění** a **ViewRoles** se vztahují na jakékoli položky (Asset nebo anotace), zatímco **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** a **ViewPermissions** se vztahují jenom na kořenový prostředek.
> 
> Právo **Odstranit** platí pro položku a všechny podpoložky nebo jednu položku pod ní. Například odstranění assetu odstraní také všechny poznámky k tomuto prostředku.
> 

### <a name="permissions"></a>Oprávnění
Oprávnění je jako seznam položek řízení přístupu. Každá položka řízení přístupu přiřadí sadu práv k objektu zabezpečení. Oprávnění lze zadat pouze u prostředku (to znamená na kořenovou položku) a použít u prostředku a všech podpoložky.

V rámci **Azure Data Catalog** Preview se v seznamu oprávnění podporuje jenom **oprávnění ke čtení** , aby bylo možné povolit scénář pro omezení viditelnosti assetu.

Ve výchozím nastavení má každý ověřený uživatel právo **číst** pro jakoukoli položku v katalogu, pokud není viditelnost omezen na sadu objektů zabezpečení v oprávněních.

## <a name="rest-api"></a>REST API
Žádosti o **vložení** a **odeslání** položky zobrazení lze použít k řízení rolí a oprávnění: Kromě datové části položky mohou být pro dvě systémové vlastnosti zadány **role** a **oprávnění**.

> [!NOTE]
> **oprávnění** platí pouze pro kořenovou položku.
> 
> Role **vlastníka** platí pouze pro kořenovou položku.
> 
> Ve výchozím nastavení, když je v katalogu vytvořena položka **Přispěvatel** je nastavena na aktuálně ověřeného uživatele. Pokud by měla být položka aktualizovatelná všemi uživateli, **Přispěvatel** by měl být nastaven na hodnotu &lt; Everyone &gt; speciální objekt zabezpečení ve vlastnosti **role** při prvním publikování položky (viz následující příklad). **Přispěvatel** nemůže být změněn a zůstane stejný během životního času položky ( **správce** nebo **vlastník** nemá právo změnit **přispěvatele**). Jediná hodnota podporovaná explicitním nastavením **přispěvatele** je &lt; Everyone &gt; : **Přispěvatel** může být pouze uživatel, který vytvořil položku nebo &lt; kdokoli &gt; .
> 

### <a name="examples"></a>Příklady
**Nastavit přispěvatele pro &lt; všechny &gt; při publikování položky**
Speciální objekt zabezpečení &lt; Everyone &gt; má objectId "00000000-0000-0000-0000-000000000201".
  **Post** https: \/ /API.azuredatacatalog.com/catalogs/default/views/Tables/?API-Version=2016-03-30

> [!NOTE]
> Některé implementace klienta protokolu HTTP mohou automaticky vystavovat požadavky v reakci na server 302 ze serveru, obvykle ale z požadavku odstranit autorizační hlavičky. Vzhledem k tomu, že se autorizační hlavička vyžaduje k tomu, aby byly požadavky na Azure Data Catalog, je nutné zajistit, aby byla při opětovném vystavování žádosti na umístění přesměrování určeném Azure Data Catalog stále k dispozici autorizační hlavička. Následující vzorový kód demonstruje použití objektu .NET HttpWebRequest.
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

  **Přiřazení vlastníků a omezení viditelnosti pro existující kořenovou položku**: **Put** https: \/ /API.azuredatacatalog.com/catalogs/default/views/Tables/042297b0...1be45ecd462a?API-Version=2016-03-30

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
> V části PUT není nutné zadávat datovou část položky v těle: lze použít k aktualizaci pouze rolí a oprávnění.
> 

## <a name="next-steps"></a>Další kroky
[Odkaz na Azure Data Catalog REST API](/rest/api/datacatalog/)
