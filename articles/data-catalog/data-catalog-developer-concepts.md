---
title: Koncepce pro vývojáře Azure Data Catalog
description: Úvod do klíčových konceptů v Azure Data Catalog konceptuální model, jako vystavené prostřednictvím rozhraní API REST katalogu.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 97dafe01db4779332a530dc8981b3d6ada8c9df9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406511"
---
# <a name="azure-data-catalog-developer-concepts"></a>Koncepce pro vývojáře Azure Data Catalog
Microsoft **Azure Data Catalog** je plně spravovaná Cloudová služba, která poskytuje možnosti pro zjišťování zdrojů dat a crowdsourcingová metadata zdroje dat. Vývojáři můžou využívat službu přes jeho rozhraní REST API. Vysvětlení pojmů implementované ve službě je důležité pro vývojáře úspěšně integrovat **Azure Data Catalog**.

## <a name="key-concepts"></a>Klíčové koncepty
**Azure Data Catalog** Koncepční model založený na čtyři klíčové koncepty: **katalogu**, **uživatelé**, **prostředky**, a  **Poznámky**.

![Koncept][1]

*Obrázek 1 – zjednodušené koncepčního modelu Azure Data Catalog*

### <a name="catalog"></a>Katalog
A **katalogu** je kontejner pro všechny organizace ukládá metadata nejvyšší úrovně. Existuje jedna **katalogu** povolených pro účet služby Azure. Katalogy, které jsou vázané na předplatné Azure, ale pouze jeden **katalogu** lze vytvořit pro libovolný daný účet Azure, i když účet může obsahovat víc předplatných.

Katalog obsahuje **uživatelé** a **prostředky**.

### <a name="users"></a>Uživatelé
Uživatelé jsou objekty zabezpečení, které mají oprávnění k provedení akce (vyhledávání v katalogu, přidat, upravit nebo odebrat položky atd.) v katalogu.

Existuje několik různých rolí, které uživatel může mít. Informace o rolích najdete v části role a autorizace.

Můžete přidat jednotlivé uživatele a skupiny zabezpečení.

Azure Data Catalog používá Azure Active Directory pro správu identit a přístupů. Každý uživatel katalogu musí být členem skupiny služby Active Directory pro účet.

### <a name="assets"></a>Prostředky
A **katalogu** obsahuje datové assety. **Prostředky** jsou jednotky členitosti spravuje katalogu.

Ve zdroji dat se liší členitosti prostředek. Pro SQL Server nebo Oracle Database prostředek může být tabulka nebo zobrazení. Pro SQL Server Analysis Services může být prostředek opatření, dimenze nebo klíčový ukazatel výkonu (KPI). Pro SQL Server Reporting Services je prostředek sestavy.

**Asset** je věc, kterou můžete přidat nebo odebrat z katalogu. Je to jednotka výsledků, které získáte zpět ze **hledání**.

**Asset** se skládá z jeho název, umístění a typ a poznámky, které podrobnější popis.

### <a name="annotations"></a>Poznámky
Poznámky jsou položky, které představují metadata o prostředcích.

Příklady poznámky: popis, značky, schéma, dokumentaci, atd. Úplný seznam typů prostředků a poznámky typů najdete v oddílu Asset objektu modelu.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcingu poznámky a pohledu uživatele (násobnost stanovisko)
Klíčovým prvkem služby Azure Data Catalog je, jak podporuje crowdsourcingový metadat v systému. Rozdíl od přístupu wiki – pokud existuje pouze jeden stanovisko a poslední zápis – model Azure Data Catalog umožňuje více názory k živým vedle sebe v systému.

Tento přístup odráží reálného světa podnikových dat, kde různí uživatelé mají různých perspektiv dané prostředků:

* Správce databáze může poskytnout informace o smlouvách o úrovni služeb nebo intervalu k dispozici zpracování pro hromadné operace ETL
* Společnosti steward dat může poskytují informace o obchodních procesů, u kterých bude použito prostředek nebo klasifikace, které se má použít obchodní
* Finanční analytik může poskytnout informace o využití dat během úlohy konce období vytváření sestav

Pro podporu v tomto příkladu, může každý uživatel – správce databáze, steward dat a analytik – přidat popis do jedné tabulky, který byl zaregistrován v katalogu. V systému se zachovají všechny popisy a na portálu Azure Data Catalog se zobrazují všechny popisy.

Tento vzor se použije pro většinu položek v objektovém modelu, takže typy objektů v datové části JSON jsou často pole pro vlastnosti kde očekáváte typu singleton.

Kořen je v rámci prostředku pole Popis objektů. Vlastnost pole má název "Popis". Popis objektu má jednu vlastnost – Popis. Vzor je, že každý uživatel, který popis typů získá popis objekt vytvořen pro hodnota zadaná uživatelem.

Uživatelském prostředí pak můžete vybrat způsob zobrazení kombinace. Existují tři různé vzorce pro zobrazení.

* Nejjednodušším vzorem je "Zobrazit všechny". V tomto modelu se zobrazí všechny objekty v zobrazení seznamu. Na portálu Azure Data Catalog uživatelského prostředí používá tento vzor pro popis.
* Jiný způsob je "Slučování". V tomto modelu všechny hodnoty z různých uživatelů jsou sloučeny, s duplicitními odebrat. Příkladem tohoto modelu na portálu Azure Data Catalog uživatelského prostředí jsou vlastnosti značek a odborníky.
* Třetí vzor je "poslední zápis". V tomto modelu se zobrazí pouze nejnovější hodnotu zadanou v. friendlyName je příkladem tohoto modelu.

## <a name="asset-object-model"></a>Asset objektový model
Jak představíme v části klíčové koncepty nástroje **Azure Data Catalog** objektový model obsahuje položky, které je možné prostředky nebo poznámky. Položky mají vlastnosti, které můžou být nepovinné nebo povinné. Některé vlastnosti se vztahují na všechny položky. Některé vlastnosti se vztahují na všechny prostředky. Některé vlastnosti se vztahují jenom na konkrétní majetek typy.

### <a name="system-properties"></a>Systémové vlastnosti
<table><tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr><tr><td>časové razítko</td><td>DateTime</td><td>Čas poslední změny položky. Toto pole je generován serveru při vložení položky a pokaždé, když se aktualizuje položku. Hodnota této vlastnosti se ignoruje na vstupu operace publikování.</td></tr><tr><td>id</td><td>URI</td><td>Absolutní adresa url položky (jen pro čtení). Je jedinečný adresovatelný identifikátor URI pro položku.  Hodnota této vlastnosti se ignoruje na vstupu operace publikování.</td></tr><tr><td>type</td><td>Řetězec</td><td>Typ prostředku (jen pro čtení).</td></tr><tr><td>Značka Etag</td><td>Řetězec</td><td>Řetězec odpovídající verzi položky, která se dá použít pro optimistického řízení souběžnosti při provádění operací, které aktualizovat položky v katalogu. "*" lze hledat jakoukoli jinou hodnotu.</td></tr></table>

### <a name="common-properties"></a>Společné vlastnosti
Tyto vlastnosti se vztahují na všechny typy prostředků kořenové a všechny typy poznámek.

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logická hodnota</td><td>Označuje, zda dat položky je odvozen ze zdrojového systému (např. Sql Server Database, Oracle Database) nebo definovaných uživatelem.</td></tr>
</table>

### <a name="common-root-properties"></a>Společné vlastnosti kořenové
<p>
Tyto vlastnosti se vztahují na všechny typy prostředků root.

<table><tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr><tr><td>jméno</td><td>Řetězec</td><td>Název odvozené z umístění zdroje dat</td></tr><tr><td>DSL</td><td>Datasourcelocation nešlo</td><td>Jednoznačně popisuje zdroje dat a je jeden z identifikátorů pro prostředek. (Viz oddíl duální identity).  Struktura dsl se liší podle typu protokolu a zdroj.</td></tr><tr><td>Zdroj dat</td><td>Zdrojdatinfo</td><td>Další podrobnosti o typu prostředku.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Uživatel, který naposledy zaregistrována tento prostředek popisuje.  Obsahuje jedinečné id pro uživatele (upn) a zobrazovaný název (jméno a příjmení).</td></tr><tr><td>identifikátor containerId</td><td>Řetězec</td><td>ID assetu kontejneru pro zdroj dat Tato vlastnost není podporována pro typ kontejneru.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Společné vlastnosti anotace typu singleton
Tyto vlastnosti se vztahují na všechny typy není typu singleton poznámky (poznámky, které může být víc za asset).

<table>
<tr><td><b>Název vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>key</td><td>Řetězec</td><td>Uživatel zadaný klíč, který jednoznačně identifikuje poznámky v aktuální kolekci. Délka klíče nesmí překročit 256 znaků.</td></tr>
</table>

### <a name="root-asset-types"></a>Typy prostředků kořenové
Kořenové asset typy jsou typy, které představují různé druhy datových assetů, které mohou být registrovány v katalogu. Pro každý typ kořenového je zobrazení, která popisuje asset a poznámky, které jsou zahrnuté v zobrazení. Název zobrazení byste měli použít ve odpovídající segment adresy url {view_name} při publikování assetu pomocí rozhraní REST API.

<table><tr><td><b>Typ prostředku (název zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Povolené poznámky</b></td><td><b>Komentáře</b></td></tr><tr><td>Tabulka ("tabulky")</td><td></td><td></td><td>Popis<p>FriendlyName<p>Značka<p>Schéma<p>ColumnDescription<p>ColumnTag<p> Expert<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentace<p></td><td>Tabulka představuje žádná tabulková data.  Příklad: tabulku SQL, zobrazení SQL, tabulka tabulkové služby Analysis Services, Analysis Services Multidimensional dimenze, tabulka Oracle atd.   </td></tr><tr><td>Míry ("opatření")</td><td></td><td></td><td>Popis<p>FriendlyName<p>Značka<p>Expert<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ reprezentuje míra Analysis Services.</td></tr><tr><td></td><td>Míra</td><td>Sloupec</td><td></td><td>Metadata popisující míry</td></tr><tr><td></td><td>isCalculated </td><td>Logická hodnota</td><td></td><td>Určuje, pokud je míra počítá nebo ne.</td></tr><tr><td></td><td>Skupina měr</td><td>Řetězec</td><td></td><td>Hmotných kontejnerů pro míru</td></tr><td>Klíčový ukazatel výkonu "(KPI)</td><td></td><td></td><td>Popis<p>FriendlyName<p>Značka<p>Expert<p>AccessInstruction<p>Dokumentace</td><td></td></tr><tr><td></td><td>Skupina měr</td><td>Řetězec</td><td></td><td>Hmotných kontejnerů pro míru</td></tr><tr><td></td><td>goalExpression</td><td>Řetězec</td><td></td><td>Číselný výraz MDX nebo výpočet, který vrátí cílovou hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>valueExpression</td><td>Řetězec</td><td></td><td>Multidimenzionální číselný výraz, který vrací aktuální hodnotu klíčového ukazatele výkonu.</td></tr><tr><td></td><td>statusExpression</td><td>Řetězec</td><td></td><td>Výraz MDX, který představuje stav klíčového ukazatele výkonu k určitému bodu v čase.</td></tr><tr><td></td><td>trendExpression</td><td>Řetězec</td><td></td><td>Výraz MDX, který se vyhodnotí jako hodnota klíčového ukazatele výkonu v čase. Trend může být jakékoli podle času kritérium, které je užitečné v kontextu konkrétní obchodní.</td>
<tr><td>Sestavy ("zprávy")</td><td></td><td></td><td>Popis<p>FriendlyName<p>Značka<p>Expert<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ reprezentuje sestavy služby SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Řetězec</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Řetězec</td><td></td><td></td></tr><tr><td>Kontejner ("kontejnery")</td><td></td><td></td><td>Popis<p>FriendlyName<p>Značka<p>Expert<p>AccessInstruction<p>Dokumentace<p></td><td>Tento typ reprezentuje kontejner jiných prostředcích, třeba SQL database, kontejneru objektů BLOB systému Azure nebo model služby Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Anotace typů
Anotace typů představují typy metadat, které je možné přiřadit na jiné typy v katalogu.

<table>
<tr><td><b>Typ poznámky (název vnořené zobrazení)</b></td><td><b>Další vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>

<tr><td>Popis ("Popis")</td><td></td><td></td><td>Tato vlastnost obsahuje popis pro určitý prostředek. Každý uživatel systému můžete přidat vlastní popis.  Jenom tento uživatel může upravit popis objektu.  (Vlastníci Admins a prostředků můžete odstranit objekt popisu, ale nikoli upravovat). Systém udržuje popisy uživatelova samostatně.  Proto je pole popisy jednotlivých prostředků (jeden pro každý uživatel, který se přidal své znalosti o majetku, kromě může být takový, který obsahuje informace získané ze zdroje dat).</td></tr>
<tr><td></td><td>description</td><td>řetězec</td><td>Krátký popis prostředku (2 až 3 řádky)</td></tr>

<tr><td>Značky ("tags")</td><td></td><td></td><td>Tato vlastnost definuje značky pro prostředek. Každý uživatel systému můžete přidat více značky pro prostředek.  Pouze uživatel, který vytvořil objekty značky můžete upravovat.  (Vlastníci Admins a prostředků můžete odstranit objekt značky, ale nikoli upravovat). Systém samostatně udržuje značky uživatelů.  Proto je pole objektů značka jednotlivých prostředků.</td></tr>
<tr><td></td><td>značka</td><td>řetězec</td><td>Značka s popisem prostředku.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Tato vlastnost obsahuje popisný název pro určitý prostředek. FriendlyName je anotaci typu singleton – pouze jeden FriendlyName lze přidat do assetu.  Pouze uživatel, který vytvořil objekt FriendlyName můžete upravit ho. (Vlastníci Admins a prostředků můžete odstranit objekt FriendlyName, ale nikoli upravovat). Systém samostatně udržuje popisné názvy uživatelů.</td></tr>
<tr><td></td><td>FriendlyName</td><td>řetězec</td><td>Popisný název assetu.</td></tr>

<tr><td>Schéma ("schéma")</td><td></td><td></td><td>Schéma popisuje strukturu dat.  Zobrazí seznam názvů atribut (sloupec, atribut, pole, atd.), typy a další metadata.  Tyto informace je odvozen z datového zdroje.  Schéma je anotaci typu singleton – je možné přidat pouze jedno schéma pro prostředek.</td></tr>
<tr><td></td><td>Sloupce</td><td>Sloupec]</td><td>Pole objektů sloupce. Popisují se informace získané ze zdroje dat ve sloupci.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Tato vlastnost obsahuje popis pro sloupec.  Každý uživatel systému můžete přidat vlastní popisy pro více sloupců (maximálně jeden sloupec). Pouze uživatel, který vytvořil objekty ColumnDescription můžete upravovat.  (Vlastníci Admins a prostředků můžete odstranit objekt ColumnDescription, ale nikoli upravovat). Systém udržuje popisy sloupců tyto uživatele samostatně.  Proto je pole objektů ColumnDescription jednotlivých prostředků (jeden pro každý sloupec pro každého uživatele, kterým přispěl své znalosti o sloupce navíc může být takový, který obsahuje informace získané ze zdroje dat).  ColumnDescription volně vázán na schéma tak můžete získat synchronizovaný. ColumnDescription popisuje sloupec, který již existuje ve schématu.  Záleží zapisovač, který má udržovat synchronizované popis a schéma.  Zdroj dat může mít také informace o popisu sloupce a jsou další ColumnDescription objekty, které by se vytvořil při spuštění nástroje.</td></tr>
<tr><td></td><td>Názevsloupce</td><td>Řetězec</td><td>Název sloupce, na který odkazuje tento popis.</td></tr>
<tr><td></td><td>description</td><td>Řetězec</td><td>krátký popis (2 až 3 řádky) sloupce.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Tato vlastnost obsahuje značku pro sloupec. Každý uživatel systému můžete přidat více značek pro daný sloupec a můžete přidat značky pro více sloupců. Pouze uživatel, který vytvořil objekty ColumnTag můžete upravovat. (Vlastníci Admins a prostředků můžete odstranit objekt ColumnTag, ale nikoli upravovat). Systém udržuje značky sloupec tyto uživatele samostatně.  Proto je pole objektů ColumnTag jednotlivých prostředků.  ColumnTag volně vázán na schéma tak můžete získat synchronizovaný. ColumnTag popisuje sloupec, který již existuje ve schématu.  Záleží zapisovač, který má udržovat synchronizované sloupec značku a schéma.</td></tr>
<tr><td></td><td>Názevsloupce</td><td>Řetězec</td><td>Název sloupce, na který odkazuje tato značka.</td></tr>
<tr><td></td><td>značka</td><td>Řetězec</td><td>Značky popisující sloupce.</td></tr>

<tr><td>Odborníky "(odborníci)</td><td></td><td></td><td>Tato vlastnost obsahuje uživatele, který je považován za experti v datové sadě. Odborné opinions(descriptions) bublin k hornímu okraji uživatelského rozhraní při výpisu popisy. Každý uživatel může určit vlastní odborníky. Jenom tento uživatel může upravovat objektů experty. (Vlastníci Admins a prostředků můžete odstranit odborné objekty, ale nikoli upravovat).</td></tr>
<tr><td></td><td>Odborník</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Ve verzi Preview ("verze Preview")</td><td></td><td></td><td>Verze preview obsahuje snímek toho prvních 20 řádků dat pro prostředek. Ve verzi Preview smysl jenom pro některé typy prostředků (dává smysl pro tabulku, ale ne pro míru).</td></tr>
<tr><td></td><td>preview</td><td>Object]</td><td>Pole objektů, které představují sloupce.  Každý objekt má vlastnost mapování na sloupec s hodnotou pro tento sloupec řádku.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>řetězec</td><td>Typ mime obsahu.</td></tr>
<tr><td></td><td>content</td><td>řetězec</td><td>Pokyny k tom, jak získat přístup k této datovému assetu. Obsah může být adresa URL, e-mailovou adresu nebo sadu pokynů.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Počet řádků v datové sadě</td></tr>
<tr><td></td><td>velikost</td><td>zem. šířka</td><td>Velikost v bajtech datové sady.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>řetězec</td><td>Čas posledního schématu byla změněna.</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>řetězec</td><td>Čas poslední změnil sady dat (data byla přidána, upravit, nebo odstranění)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Sloupce</td></td><td>[] ColumnDataProfile</td><td>Pole sloupců dat profilů.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Názevsloupce</td><td>Řetězec</td><td>Název sloupce, na který odkazuje tato klasifikace.</td></tr>
<tr><td></td><td>Klasifikace</td><td>Řetězec</td><td>Klasifikace dat v tomto sloupci.</td></tr>

<tr><td>Dokumentace ke službě dokumentace ("k")</td><td></td><td></td><td>Daný prostředek může mít pouze jeden dokumentaci s ním spojená.</td></tr>
<tr><td></td><td>mimeType</td><td>řetězec</td><td>Typ mime obsahu.</td></tr>
<tr><td></td><td>content</td><td>řetězec</td><td>Dokumentace k obsahu.</td></tr>

</table>

### <a name="common-types"></a>Běžné typy
Běžné typy může sloužit jako typy vlastností, ale nejsou položky.

<table>
<tr><td><b>Společný typ.</b></td><td><b>Vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>
<tr><td>Zdrojdatinfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>řetězec</td><td>Popisuje typ datového zdroje.  Příklad: SQL Server, Oracle Database, atd.  </td></tr>
<tr><td></td><td>objectType</td><td>řetězec</td><td>Popisuje typ objektu v datovém zdroji. Příklad: tabulky, zobrazení pro SQL Server.</td></tr>

<tr><td>Datasourcelocation nešlo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokol</td><td>řetězec</td><td>Povinná hodnota. Popisuje protokol používaný ke komunikaci se zdrojem dat. Příklad: "tds" pro SQl Server, "oracle" pro Oracle, atd. Odkazovat na [specifikace odkazu - DSL struktury zdroje dat](data-catalog-dsr.md) seznam aktuálně podporovaných protokolů.</td></tr>
<tr><td></td><td>Adresa</td><td>Slovník<string, object></td><td>Povinná hodnota. Adresa je sada data specifická pro protokol, který se používá k identifikaci zdroje dat, na kterou se odkazuje. Adresa data na protokol pro konkrétní obor, to znamená, ho je význam bez znalosti protokolu.</td></tr>
<tr><td></td><td>Ověřování</td><td>řetězec</td><td>Volitelné. Schéma ověřování používaný ke komunikaci se zdrojem dat. Příklad: windows, oauth, atd.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Slovník<string, object></td><td>Volitelné. Další informace o tom, jak se připojit ke zdroji dat</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Back-endu neprovádí žádné ověřování zadané vlastnosti pro AAD během publikování.</td></tr>
<tr><td></td><td>hlavní název uživatele</td><td>řetězec</td><td>Jedinečnou e-mailovou adresu uživatele. Je třeba zadat Pokud není zadané ID objektu nebo v kontextu "lastRegisteredBy" vlastnosti, jinak volitelné.</td></tr>
<tr><td></td><td>ID objektu</td><td>Guid</td><td>Identity uživatele nebo zabezpečení skupiny AAD. Volitelné. Je třeba zadat Pokud hlavní název uživatele není k dispozici, v opačném případě nepovinný.</td></tr>
<tr><td></td><td>Jméno</td><td>řetězec</td><td>Křestní jméno uživatele (pro účely zobrazení). Volitelné. Jediná platná v kontextu "lastRegisteredBy" vlastnosti. Nelze zadat při zadávání objektu zabezpečení pro "role", "oprávnění" a "odborníky".</td></tr>
<tr><td></td><td>Příjmení</td><td>řetězec</td><td>Příjmení uživatele (pro účely zobrazení). Volitelné. Jediná platná v kontextu "lastRegisteredBy" vlastnosti. Nelze zadat při zadávání objektu zabezpečení pro "role", "oprávnění" a "odborníky".</td></tr>

<tr><td>Sloupec</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název sloupce nebo atributu.</td></tr>
<tr><td></td><td>type</td><td>řetězec</td><td>datový typ sloupce nebo atributu. Povolené typy závisí na sourceType datového prostředku.  Je podporován pouze podmnožinu typů.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Maximální povolená délka pro sloupec nebo atributu. Odvozena z datového zdroje. Platí jenom pro některé typy zdrojů.</td></tr>
<tr><td></td><td>Přesnost</td><td>byte</td><td>Přesnost sloupce nebo atributu. Odvozena z datového zdroje. Platí jenom pro některé typy zdrojů.</td></tr>
<tr><td></td><td>IsNullable</td><td>Logická hodnota</td><td>Určuje, zda sloupec může mít hodnotu null, nebo ne. Odvozena z datového zdroje. Platí jenom pro některé typy zdrojů.</td></tr>
<tr><td></td><td>Výraz</td><td>řetězec</td><td>Pokud je hodnota počítaného sloupce, toto pole obsahuje výraz, který vyjadřuje hodnotu. Odvozena z datového zdroje. Platí jenom pro některé typy zdrojů.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Názevsloupce </td><td>řetězec</td><td>Název sloupce</td></tr>
<tr><td></td><td>type </td><td>řetězec</td><td>Typ sloupce</td></tr>
<tr><td></td><td>min </td><td>řetězec</td><td>Minimální hodnota v datové sadě</td></tr>
<tr><td></td><td>max </td><td>řetězec</td><td>Maximální hodnota v datové sadě</td></tr>
<tr><td></td><td>prům. </td><td>double</td><td>Průměrná hodnota v datové sadě</td></tr>
<tr><td></td><td>Směrodatná odchylka </td><td>double</td><td>Směrodatná odchylka pro datovou sadu</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Počet hodnot null v datové sadě</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Počet jedinečných hodnot v datové sadě</td></tr>


</table>

## <a name="asset-identity"></a>Identita prostředku
Azure Data Catalog používá "protokol" a identity vlastnosti z kontejneru objektů a "address" vlastnosti "dsl" datasourcelocation nešlo generovat identity prostředku, který se používá k řešení v katalogu asset.
Například "tds" protokolu má vlastnosti identity "server", "databázi", "schéma" a "objekt". Kombinace protokolu a vlastnosti identity slouží ke generování identity prostředku tabulka SQL serveru.
Azure Data Catalog poskytuje několik protokolů předdefinované datové zdroje, které jsou uvedeny v tématu [specifikace odkazu - DSL struktury zdroje dat](data-catalog-dsr.md).
Prostřednictvím kódu programu je možné rozšířit sadu podporovaných protokolů (viz část o reference k rozhraní API REST katalogu Data). Správci katalogu můžou zaregistrovat vlastní protokoly zdrojů dat. Následující tabulka popisuje vlastnosti potřebné k registraci vlastního protokolu.

### <a name="custom-data-source-protocol-specification"></a>Specifikace protokolu zdroj vlastních dat
<table>
<tr><td><b>Typ</b></td><td><b>Vlastnosti</b></td><td><b>Datový typ</b></td><td><b>Komentáře</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Obor názvů</td><td>řetězec</td><td>Obor názvů protokolu. Namespace musí mít 1 až 255 znaků, obsahovat jednu nebo více částí neprázdný oddělené tečkou (.). Každá část musí mít délku 1 až 255 znaků, začínat písmenem a obsahovat jenom písmena a číslice.</td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název protokolu. Název musí mít délku 1 až 255 znaků, začínat písmenem a obsahovat jenom písmena, číslice a znak spojovníku (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Seznam vlastností identity, musí obsahovat alespoň jednu, ale žádné vlastnosti více než 20. Příklad: "server", "databázi", "schéma", "objekt" jsou vlastnosti identity protokolu "tds".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Seznam sad identit. Definuje sadu vlastností identity, které reprezentuje identitu platný asset. Musí obsahovat alespoň jednu, ale žádné sady více než 20. Příklad: {"server", "databáze", "schéma" a "objekt"} je identita nastavená pro protokol "tds", který definuje totožnost asset tabulky serveru Sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název vlastnosti. Název musí mít délku 1 až 100 znaků, začínat písmenem a může obsahovat jenom písmena a číslice.</td></tr>
<tr><td></td><td>type</td><td>řetězec</td><td>Typ vlastnosti. Podporované hodnoty: "bool", logická hodnota ","bajtů","guid","int","celočíselné","dlouhý","řetězec","url"</td></tr>
<tr><td></td><td>IgnoreCase</td><td>BOOL</td><td>Určuje, zda by měl být ignoruje velikost písmen při použití hodnoty vlastnosti. Lze zadat pouze pro vlastnosti typu "řetězec". Výchozí hodnota je false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>[] BOOL</td><td>Určuje, zda by měl pro každou část cesty adresy url ignoruje velikost písmen. Lze zadat pouze pro vlastnosti typu "url". Výchozí hodnota je [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>jméno</td><td>řetězec</td><td>Název sady identity.</td></tr>
<tr><td></td><td>properties</td><td>řetězec]</td><td>Nastavit seznam vlastnosti identity, které jsou součástí této identity. Nesmí obsahovat duplicitní hodnoty. Každou vlastnost odkazuje sada identity musí být definován v seznamu "identityProperties" protokolu.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Role a autorizace
Microsoft Azure Data Catalog poskytuje možnosti autorizace pro operace CRUD s prostředky a poznámky.

## <a name="key-concepts"></a>Klíčové koncepty
Azure Data Catalog používá dva mechanismy ověřování:

* Autorizace na základě rolí
* Autorizace na základě oprávnění

### <a name="roles"></a>Role
Existují tři role: **správce**, **vlastníka**, a **Přispěvatel**.  Každá role má jejich rozsah a oprávnění, které jsou shrnuty v následující tabulce.

<table><tr><td><b>Role</b></td><td><b>Rozsah</b></td><td><b>Práva</b></td></tr><tr><td>Správce</td><td>Katalog (všechny prostředky/poznámky v katalogu)</td><td>Čtení ViewRoles Delete

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Vlastník</td><td>Každý prostředek (kořenovou položku)</td><td>Čtení ViewRoles Delete

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Přispěvatel</td><td>Každý jednotlivý prostředek a poznámky</td><td>Čtení aktualizace odstranit ViewRoles Poznámka: všechna práva jsou odvolat v případě čtení přímo na položce je odvolána Přispěvatel</td></tr></table>

> [!NOTE]
> **Čtení**, **aktualizace**, **odstranit**, **ViewRoles** práva se vztahují na libovolnou položku (asset nebo poznámky) při **vlastnictví**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** platí pouze pro kořenový asset.
> 
> **Odstranit** vpravo platí pro položky a podřízené položky nebo pod ním jednu položku. Například odstraněním asset se odstraní také jakékoliv anotace pro tento prostředek.
> 
> 

### <a name="permissions"></a>Oprávnění
Oprávnění se jako seznam položek řízení přístupu. Každé položky řízení přístupu přiřadí sadu práv k objektu zabezpečení. Oprávnění lze zadat pouze na prostředek (to znamená, kořenovou položku) a použít na prostředek a všechny podřízené položky.

Během **Azure Data Catalog** ve verzi preview, pouze **čtení** vpravo se podporuje v seznamu oprávnění k povolení scénář omezení viditelnosti prostředku.

Ve výchozím nastavení má každý ověřený uživatel **čtení** pravým tlačítkem myši pro libovolnou položku v katalogu, pokud je omezena na sadu objekty zabezpečení v oprávnění viditelnost.

## <a name="rest-api"></a>REST API
**Vložit** a **příspěvek** položka zobrazení požadavky, můžete použít k řízení rolí a oprávnění: kromě datové položky se dá nastavit dvě vlastnosti systému **role** a  **oprávnění**.

> [!NOTE]
> **oprávnění** platí jenom pro kořenovou položku.
> 
> **Vlastník** role platí jenom pro kořenovou položku.
> 
> Ve výchozím nastavení při vytvoření položky v katalogu jeho **Přispěvatel** je nastavena na aktuálně ověřeného uživatele. Pokud položka by měla být aktualizovat všichni uživatelé, **Přispěvatel** by mělo být nastavené &lt;všem uživatelům&gt; zvláštní objekt v zabezpečení **role** vlastnosti, když je první položka publikovaných (viz část o v následujícím příkladu). **Přispěvatel** nelze změnit a zůstane stejný během životnosti položky (i **správce** nebo **vlastníka** nemá práva změnit **Přispěvatel**). Hodnota pro explicitní nastavení **Přispěvatel** je &lt;všichni&gt;: **Přispěvatel** může být jenom uživatel, který vytvořil položku nebo &lt;všem uživatelům &gt;.
> 
> 

### <a name="examples"></a>Příklady
**Nastavte přispěvatele na &lt;Everyone&gt; při publikování položky.**
Objekt zabezpečení speciální &lt;Everyone&gt; má objectId "00000000-0000-0000-0000-000000000201".
  **PŘÍSPĚVEK** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Některé implementace klienta HTTP může automaticky opakujte požadavků v reakci 302 ze serveru, ale obvykle pruhu autorizační hlavičky z požadavku. Protože autorizační hlavičky. je třeba provést žádosti o připojení ke službě Azure Data Catalog, musíte zajistit, že autorizační hlavičky stále získáte při přístupovými požadavek na přesměrování umístění určeného proměnnou Azure Data Catalog. Následující ukázkový kód ukazuje pomocí objekt .NET HttpWebRequest.
> 
> 

**Text**

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

  **Přiřaďte vlastníky, kteří a omezení viditelnosti pro existující položku kořenové**: **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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

> [!NOTE]
> V PUT to není nutné zadat datovou část položky v těle: PUT je možné aktualizovat jenom role nebo oprávnění.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
