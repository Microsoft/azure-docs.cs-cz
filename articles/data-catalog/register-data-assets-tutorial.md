---
title: Registrace datových prostředků v Azure Data Catalogu
description: Tento kurz popisuje, jak zaregistrovat datové prostředky ve vašem katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68950237"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Kurz: Registrace datových prostředků v Katalogu dat Azure

V tomto kurzu použijete registrační nástroj k registraci datových prostředků z ukázky databáze Azure SQL s katalogem. Registrace je proces extrahování klíčových strukturálních metadat – například názvy, typy a umístění – ze zdroje dat a assetů v tomto zdroji a zkopírování těchto metadat do katalogu. Zdroj dat a datové assety zůstanou tam, kde jsou, ale jejich metadata použije katalog k tomu, aby byly tyto objekty snadněji zjistitelné a srozumitelnější.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Registrace datových assetů 
> * Hledání datových datových prostředků
> * Přidání poznámek k datovým assetům
> * Připojení k datovým assetům
> * Správa datových assetů
> * Odstranění datových prostředků

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, je nutné dokončit [rychlý start](register-data-assets-tutorial.md).

* Předplatné [Microsoft Azure.](https://azure.microsoft.com/)
* Musíte mít vlastního [klienta Služby Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Chcete-li nastavit katalog dat, musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.

## <a name="register-data-assets"></a>Registrace datových assetů

### <a name="register-a-data-source"></a>Registrace zdroje dat

Datové prostředky (tabulky) můžete zaregistrovat z [ukázky databáze Azure SQL](../sql-database/sql-database-single-database-get-started.md), ale můžete použít libovolný podporovaný zdroj dat, pokud dáváte přednost práci s daty, která jsou známá a relevantní pro vaši roli. Seznam podporovaných zdrojů dat naleznete v tématu [Podporované zdroje dat](data-catalog-dsr.md).

Název databáze Azure SQL, který používáme v tomto kurzu, je *RLSTest*.

Datové prostředky teď můžete zaregistrovat z ukázky databáze Azure SQL pomocí katalogu dat Azure.

1. Přejděte na [domovskou stránku Katalogu dat Azure](http://azuredatacatalog.com) a vyberte **Publikovat data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Vyberte **Spustit aplikaci** ke stažení, instalaci a spuštění registračního nástroje v počítači.

   ![Azure Data Catalog – tlačítko Spustit](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na **úvodní** stránce vyberte **Přihlásit se** a zadejte svoje přihlašovací údaje.

    ![Azure Data Catalog – Úvodní stránka](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na stránce **Katalog dat Microsoft Azure** vyberte SQL **Server** a **Další**.

    ![Azure Data Catalog – zdroje dat](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Zadejte vlastnosti připojení serveru SQL Server pro ukázku databáze Azure SQL a vyberte **CONNECT**.

   ![Azure Data Catalog – nastavení připojení k SQL Serveru](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zaregistrujte metadata datového assetu. V tomto příkladu **zaregistrujete** product objekty z ukázkového oboru názvů databáze Azure SQL:

    1. Ve stromu **Hierarchie serverů** rozbalte ukázku databáze Azure SQL a vyberte **SalesLT**.

    2. Vyberte **položku** **Produkt**, Kategorie produktu , **Popis produktu**a **Produktový model** pomocí kombinace kláves Ctrl+select.

    3. vyberte **šipku vybranou pro přesun** (**>**). Tím se přesunou všechny vybrané objekty do seznamu **Objekty k registraci**.

          ![Kurz Azure Data Catalog – procházení a výběr objektů](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Vyberte možnost**Zahrnout náhled**, pokud chcete zahrnout snímek náhledu dat. Snímek obsahuje až 20 záznamů z každé tabulky a zkopíruje se do katalogu.

    5. Vyberte možnost **Zahrnout profil dat**, pokud chcete zahrnout snímek statistik objektu pro profil dat (např.: minimální, maximální a průměrné hodnoty sloupce, počet řádků).

    6. Do pole **Přidat značky** zadejte **prodej, produkt, azure SQL**. Tím se přidají vyhledávací značky pro tyto datové assety. Značky jsou skvělý způsob, jak pomoci uživatelům najít registrovaný zdroj dat.

    7. Zadejte jméno **experta** na tyto data (volitelné).

          ![Kurz Azure Data Catalog – objekty k registraci](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Vyberte **registrovat**. Služba Azure Data Catalog zaregistruje vaše vybrané objekty. V tomto cvičení jsou registrovány vybrané objekty z ukázky databáze Azure SQL. Registrační nástroj vyextrahuje metadata z datového assetu a zkopíruje je do služby Azure Data Catalog. Data zůstávají tam, kde v současné době zůstávají. Data zůstávají pod kontrolou správců a zásad původního systému.

          ![Azure Data Catalog – registrované objekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Chcete-li zobrazit registrované objekty zdroje dat, vyberte **možnost Zobrazit portál**. Na portálu Katalog dat Azure zkontrolujte, že se v zobrazení mřížky zobrazují všechny čtyři tabulky a databáze (ověřte, zda je vyhledávací panel jasný).

        ![Objekty na portálu Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

V tomto cvičení jste zaregistrovali objekty z ukázky databáze Azure SQL, aby je uživatelé v celé organizaci mohli snadno zjistit.

V dalším cvičení se dozvíte, jak zjistit registrované datové assety.

## <a name="discover-data-assets"></a>Zjišťování datových assetů

Funkce zjišťování ve službě Azure Data Catalog používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní charakteristiky, jako jsou experti, typ zdroje dat, typ objektu a značky, abyste zobrazili datové assety odpovídající těmto charakteristikám, a tak omezit výsledky vyhledávání.

Pomocí kombinace vyhledávání a filtrování můžete rychle procházet zdroje dat, které jsou registrované v Katalogu dat Azure.

V tomto cvičení použijete portál služby Azure Data Catalog ke zjištění datových assetů, které jste zaregistrovali v předchozím cvičení. Podrobnosti o syntaxi vyhledávání naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

Následuje několik příkladů, jak zjistit datové assety v katalogu.  

### <a name="discover-data-assets-with-basic-search"></a>Zjištění datových assetů pomocí základního vyhledávání

Základní vyhledávání vám pomůže prohledat katalog pomocí jednoho nebo více hledaných výrazů. Ve výsledcích se zobrazí veškeré assety, které odpovídají jakékoli vlastnosti jednoho nebo více zadaných výrazů.

1. na portálu Katalog dat Azure vyberte **Domů.** Pokud jste zavřeli webový prohlížeč, přejděte na [domovskou stránku katalogu dat Azure](https://www.azuredatacatalog.com).

2. Do vyhledávacího pole zadejte `product` a stiskněte klávesu **ENTER**.

    ![Azure Data Catalog – základní textové vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Potvrďte, že se ve výsledcích zobrazují všechny čtyři tabulky a databáze. Mezi **zobrazením mřížky** a **zobrazením seznamu** můžete přepínat výběrem tlačítek na panelu nástrojů, jak je znázorněno na následujícím obrázku. Všimněte si, že hledané klíčové slovo je ve výsledcích vyhledávání zvýrazněno, protože možnost **Zvýraznit** je **ZAPNUTO**. Ve výsledcích vyhledávání můžete také upřesnit **počet výsledků na stránku**.

    ![Azure Data Catalog – výsledky základního textového vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Panel **Vyhledávání** se nachází na levé straně a panel **Vlastnosti** na pravé. Na panelu **Vyhledávání** lze změnit kritéria vyhledávání a filtrovat výsledky. Na panelu **Vlastnosti** se zobrazují vlastnosti vybraného objektu v mřížce nebo seznamu.

4. ve výsledcích hledání vyberte **Produkt.** Vyberte karty **Náhled**, **Sloupce**, **Datový profil**a **Dokumentace** nebo vyberte šipku pro rozbalení dolního podokna.  

    ![Azure Data Catalog – spodní podokno](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Na kartě **Náhled** uvidíte náhled dat v tabulce **Produkt**.  
5. vyberte kartu **Sloupce** a vyhledejte podrobnosti o sloupcích (například **název** a **datový typ)** v datovém prostředku.

6. výběrem karty **Datový profil** zobrazíte profilování dat (například počet řádků, velikost dat nebo minimální hodnota ve sloupci) v datovém prostředku.

### <a name="discover-data-assets-with-property-scoping"></a>Zjištění datových assetů pomocí zkoumání vlastností

Funkce zkoumání vlastností vám pomůže zjistit datové assety v případě, že se hledaný výraz shoduje se zadanou vlastností.

1. Ve části **Filtry** pod položkou **Typ objektu** vymažte filtr **Tabulka**.  

2. Do vyhledávacího pole zadejte `tags:product` a stiskněte klávesu **ENTER**. Všechny vlastnosti, které lze využít při vyhledávání v katalogu dat, naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

3. Potvrďte, že se ve výsledcích zobrazují tabulky a databáze.  

    ![Data Catalog – výsledky vyhledávání funkce zkoumání vlastnost](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Uložení vyhledávání

1. V podokně **Hledání** v části **Aktuální hledání** zadejte název hledání a vyberte **Uložit**.

    ![Azure Data Catalog – uložení vyhledávání](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Zkontrolujte, jestli se uložené vyhledávání zobrazuje v sekci **Uložená vyhledávání**.

3. Vyberte jednu z akcí, které lze s uloženým vyhledáváním provést (**Přejmenovat**, **Odstranit**, **Uložit jako výchozí**).

### <a name="grouping-with-parentheses"></a>Seskupování pomocí závorek

Závorky lze použít k seskupení částí dotazu za účelem logické izolace, zejména ve spojení s logickými operátory.

1. Do vyhledávacího pole zadejte `name:product AND (tags:product AND objectType:table)` a stiskněte klávesu **ENTER**.

2. Zkontrolujte, jestli se vám ve výsledcích vyhledávání zobrazuje pouze tabulka **Product**.

    ![Azure Data Catalog – vyhledávání seskupení](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operátory porovnání

S pomocí operátorů porovnání lze použít porovnávání jiné než rovnost pro vlastnosti, které mají typ dat číslo nebo datum.

1. Do vyhledávacího pole zadejte `lastRegisteredTime:>"06/09/2016"`.

2. Pod položkou **Typ objektu** vymažte filtr **Tabulka**.

3. Stiskněte **klávesu ENTER**.

4. Zkontrolujte, zda se zobrazují tabulky **Product**, **ProductCategory**a **ProductDescription** a databáze Azure SQL, kterou jste zaregistrovali ve výsledcích hledání.

    ![Azure Data Catalog – výsledky vyhledávání porovnání](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Podrobné informace o zjišťování datových prostředků naleznete v tématu [Jak zjistit datové datové prostředky.](data-catalog-how-to-discover.md) Další informace o syntaxi hledání naleznete v [tématu Odkaz na syntaxi hledání v katalogu dat](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Přidání poznámek k datovým assetům

V tomto cvičení použijete portál Katalog dat Azure k přidávání not (přidání informací, jako jsou popisy, značky nebo odborníci) existující datové prostředky v katalogu. Poznámky doplňují strukturální metadata extrahovaná ze zdroje dat během registrace. Díky anotaci je mnohem snazší zjistit a pochopit datové prostředky.

V tomto cvičení přidáte poznámky k jednomu datovému assetu (ProductPhoto). Doplníte k datovému assetu ProductPhoto popisný název a popis.  

1. Přejděte na [domovskou stránku katalogu dat Azure](https://www.azuredatacatalog.com) a vyhledejte `tags:product` datové prostředky, které jste zaregistrovali.

2. ve výsledcích vyhledávání vyberte **ProductModel.**  

3. Zadejte **Obrázky produktu** pro **Popisný název** a jako **Popis** zadejte **Fotografie produktu pro marketingové materiály**.

    ![Azure Data Catalog – popis ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **Popis** pomáhá ostatním zjistit datový asset a porozumět tomu, proč a jak vybraný datový asset používat. Můžete také přidat další značky a zobrazit sloupce. Zdroje dat můžete vyhledávat a filtrovat pomocí popisných metadat, která jste přidali do katalogu.

Na této stránce můžete také provést následující kroky:

* Přidání expertů k datovým assetům. voblasti **Experti** vyberte **Přidat.**

* Přidání značek na úrovni datové sady. v oblasti **Tagy** vyberte **Přidat.** Značka může být značka uživatele nebo značka glosáře. Data Catalog Standard Edition zahrnuje obchodní glosář, který pomáhá správcům katalogu definovat centrální obchodní taxonomii. Uživatelé katalogu mohou poté opatřit poznámkami datové assety pomocí termínů v glosáři. Další informace naleznete [v tématu Jak nastavit obchodní glosář pro řízené označování](data-catalog-how-to-business-glossary.md)

* Přidání značek na úrovni sloupce. V **Add** části **Tagy** pro sloupec, který chcete osvložit, vyberte Přidat v části Tagy.

* Přidání popisu na úrovni sloupce. Zadejte **Popis** pro sloupec. Můžete také zobrazit metadata popisu extrahovaná ze zdroje dat.

* Přidejte informace pro **Žádost o přístup**, které uživatelům dají návod, jak požádat o přístup k datovým assetům.
  
* Vyberte kartu **Dokumentace** a dodejte dokumentaci pro datový asset. Funkce dokumentace ve službě Azure Data Catalog vám umožňuje použít katalog dat jako úložiště obsahu. Můžete tak mít po ruce podrobné popisy a návody k registrovaným datovým assetům.
  
Můžete také přidat poznámku k více datovým assetům. Můžete například vybrat všechny datové assety, které jste zaregistrovali, a zadat k nim jednoho experta.

![Azure Data Catalog – přidání poznámky k více datovým assetům](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog podporuje crowdsourcingový přístup k přidávání poznámek. Každý uživatel katalogu dat může přidávat značky (uživatel nebo glosář), popisy a další metadata. Tímto způsobem uživatelé přidat pohled na datový prostředek a jeho použití a sdílet tuto perspektivu s ostatními uživateli.

Podrobné informace o přidávání poznámek k datovým assetům najdete v článku [Jak přidat poznámky k datovým assetům](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Připojení k datovým assetům

V tomto cvičení otevřete datové assety v integrovaném klientském nástroji (Excel) i neintegrovaném nástroji (SQL Server Management Studio) s použitím informací o připojení.

> [!NOTE]
> Je důležité pamatovat, že Azure Data Catalog vám nedává přístup k samotnému zdroji dat – pouze vám usnadňuje tento zdroj dat zjistit a porozumět jeho funkci. Když se připojíte ke zdroji dat, klientská aplikace, kterou si vyberte, použije podle potřeby vaše přihlašovací údaje systému Windows nebo vás vyzve k zadání přihlašovacích údajů. Pokud vám nebyl dříve udělen přístup k tomuto zdroji dat, bude vám muset být udělen, abyste se mohli připojit.

### <a name="connect-to-a-data-asset-from-excel"></a>Připojení k datovému assetu z Excelu

1. Ve výsledcích vyhledávání vyberte možnost **Product**. Na **panelu** nástrojů vyberte Otevřít v a vyberte **Excel**.

    ![Azure Data Catalog – připojení k datovému assetu](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. V rozbalovacím okně ke stažení vyberte **Otevřít.** Přesný postup a prostředí se může mírně lišit v závislosti na prohlížeči.

3. V okně **Oznámení o zabezpečení aplikace Microsoft Excel** vyberte možnost **Povolit**.

    ![Azure Data Catalog – automaticky otevřené okno zabezpečení Excelu](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Ponechte výchozí hodnoty v dialogovém okně **Importovat data** a vyberte **OK**.

    ![Azure Data Catalog – import dat aplikace Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Zobrazení zdroje dat v Excelu.

    ![Azure Data Catalog – tabulka produktů v Excelu](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

V tomto cvičení jste se připojili k datovým assetům zjištěným pomocí služby Azure Data Catalog. Portál služby Azure Data Catalog vám umožňuje připojit se přímo pomocí klientských aplikací integrovaných do jeho nabídky **Otevřít v aplikaci**. Také se ale můžete připojit pomocí jakékoli aplikace, kterou si vyberete, pomocí informací o umístění připojení zahrnutých v metadatech assetu. Například můžete použít SQL Server Management Studio pro připojení k databázi Azure SQL pro přístup k datům v datových prostředků registrovaných v tomto kurzu.

1. Otevřete **aplikaci SQL Server Management Studio**.

2. V dialogovém okně **Připojit k serveru** zadejte název serveru z podokna **Vlastnosti** na portálu Azure Data Catalog.

3. Použijte příslušné ověření a přihlašovací údaje pro přístup k datovému assetu. Pokud nemáte přístup, použijte informace uvedené v poli **Žádost o přístup** a vyžádejte si jej.

    ![Azure Data Catalog – žádost o přístup](media/register-data-assets-tutorial/data-catalog-request-access.png)

Vyberte **Zobrazit připojovací řetězce,** chcete-li zobrazit a zkopírovat ADO.NET, ODBC a OLEDB připojovací řetězce do schránky pro použití ve vaší aplikaci.

## <a name="manage-data-assets"></a>Správa datových assetů

V tomto kroku se dozvíte, jak nastavit zabezpečení datových assetů. Katalog dat neposkytuje uživatelům přístup k datům samotným. O udělení přístupu k datům rozhoduje vlastník zdroje dat.

Služba Data Catalog vám umožní zjistit zdroje dat a zobrazit metadata související se zdroji zaregistrovanými v katalogu. Mohou ovšem nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. V těchto scénářích můžete použít katalog dat k převzetí vlastnictví registrovaných datových prostředků a k řízení viditelnosti prostředků, které vlastníte.

> [!NOTE]
> Možnosti správy popsané v tomto cvičení jsou k dispozici pouze v Azure Data Catalog Standard Edition, nikoli v bezplatné Free Edition.
> Ve službě Azure Data Catalog může převzít vlastnictví datových prostředků, přidat spoluvlastníky k datovým prostředkům a nastavit viditelnost datových prostředků.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Převzetí vlastnictví datových assetů a omezení viditelnosti

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com). Do textového pole **Vyhledávání** zadejte `tags:cycles` a stiskněte klávesu **ENTER**.

2. vyberte položku v seznamu výsledků a na panelu nástrojů vyberte **Převzít vlastnictví.**

3. V části **Správa** panelu **Vlastnosti** vyberte **Převzít vlastnictví**.

    ![Azure Data Catalog – převzetí vlastnictví](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Chcete-li omezit viditelnost, vyberte v části **Viditelnost** **možnost Vlastníci & Tito uživatelé** a vyberte **Přidat**. Do textového pole zadejte e-mailové adresy uživatelů a stiskněte **ENTER**.

    ![Azure Data Catalog – omezení přístupu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Odstranění datových assetů

V tomto cvičení použijete portál služby Azure Data Catalog k odebrání náhledu dat z registrovaných datových assetů a k odstranění datových assetů z katalogu.

Ve službě Azure Data Catalog je možné odstranit jednotlivý asset nebo více assetů.

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com).

2. Do **Search** textového pole `tags:cycles` Hledat zadejte a vyberte **enter**.

3. Vyberte položku v seznamu výsledků a na panelu nástrojů vyberte **Odstranit,** jak je znázorněno na následujícím obrázku:

    ![Azure Data Catalog – odstranění položky mřížky](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Pokud používáte zobrazení seznamu, je zaškrtávací políčko vlevo od položky, jak je znázorněno na následujícím obrázku:

    ![Azure Data Catalog – odstranění položky seznamu](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Můžete také vybrat a odstranit více datových assetů, jak ukazuje následující obrázek:

    ![Azure Data Catalog – odstranění více datových assetů](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Výchozí chování katalogu je umožnit každému uživateli zaregistrovat libovolný zdroj dat a umožnit každému uživateli odstranit libovolný zaregistrovaný datový asset. Možnosti správy zahrnuté do Azure Data Catalog Standard Edition poskytují další možnosti pro převzetí vlastnictví assetů, omezení, kdo může assety zjistit, a omezení, kdo může assety odstranit.

## <a name="summary"></a>Souhrn

V tomto kurzu jste prozkoumali základní možnosti služby Azure Data Catalog včetně registrace, přidávání poznámek, zjišťování a správy datových assetů organizace. Nyní, když jste dokončili kurz, je čas začít. Začít můžete ještě dnes registrací zdrojů dat, na která spoléháte vy a váš tým, a pozváním kolegů k používání katalogu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Podporované zdroje dat](data-catalog-dsr.md)
