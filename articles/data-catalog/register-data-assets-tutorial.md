---
title: Registrace datových assetů ve službě Azure Data Catalog
description: Postup při registraci datových assetů v katalogu dat Azure
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: tutorial
ms.date: 04/08/2019
ms.openlocfilehash: e89bd4806e2bb2369c100e948be51dcf32f2e123
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362343"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Kurz: Registrace datových assetů ve službě Azure Data Catalog

V tomto kurzu použijete nástroj pro registraci k registraci ve službě katalogu datové assety z ukázkové databáze Azure SQL. Registrace je proces extrahování klíčových strukturálních metadat – například názvy, typy a umístění – ze zdroje dat a assetů v tomto zdroji a zkopírování těchto metadat do katalogu. Zdroj dat a datové assety zůstanou tam, kde jsou, ale jejich metadata použije katalog k tomu, aby byly tyto objekty snadněji zjistitelné a srozumitelnější.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Registrace datových assetů 
> * Vyhledávání datových prostředků
> * Přidání poznámek k datovým assetům
> * Připojení k datovým assetům
> * Správa datových assetů
> * Odstranění datových assetů

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, musíte [rychlý Start](register-data-assets-tutorial.md).

* A [Microsoft Azure](https://azure.microsoft.com/) předplatného.
* Potřebujete mít vlastní [tenanta Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Nastavení katalogu dat, musí být vlastník nebo spoluvlastník předplatného Azure.

## <a name="register-data-assets"></a>Registrace datových assetů

### <a name="register-a-data-source"></a>Registrace zdroje dat

Zaregistrujete datové assety (tabulky) z [ukázkové databáze Azure SQL](../sql-database/sql-database-single-database-get-started.md), ale můžete použít libovolný podporovaný zdroj dat, pokud chcete raději pracovat s daty, která jsou známá a relevantní pro vaši roli. Seznam podporovaných zdrojů dat naleznete v tématu [Podporované zdroje dat](data-catalog-dsr.md).

V tomto kurzu používáme název databáze Azure SQL je *RLSTest*.

Nyní můžete pomocí Azure Data Catalog zaregistrovat datové assety z ukázkové databáze Azure SQL.

1. Přejděte [domovskou stránku Azure Data Catalog](http://azuredatacatalog.com) a vyberte **publikovat Data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Vyberte **spustit aplikaci** stažení, instalace a spuštění nástroje pro registraci ve vašem počítači.

   ![Azure Data Catalog – tlačítko Spustit](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na **úvodní** stránce **přihlášení** a zadejte svoje přihlašovací údaje.

    ![Azure Data Catalog – Úvodní stránka](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na **Microsoft Azure Data Catalog** stránce **systému SQL Server** a **Další**.

    ![Azure Data Catalog – zdroje dat](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Zadejte vlastnosti připojení serveru SQL pro vaše ukázková databáze Azure SQL a vyberte **připojit**.

   ![Azure Data Catalog – nastavení připojení k SQL Serveru](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zaregistrujte metadata datového assetu. V tomto příkladu zaregistrujete **produktu** objektů z oboru názvů ukázkové databáze Azure SQL:

    1. V **hierarchie serverů** stromovém zobrazení rozbalte vaše ukázková databáze Azure SQL a vyberte **SalesLT**.

    2. Vyberte **produktu**, **ProductCategory**, **ProductDescription**, a **ProductModel** stisknutím klávesy Ctrl a vyberte.

    3. Vyberte **přesunout vybranou šipku** (**>**). Tím se přesunou všechny vybrané objekty do seznamu **Objekty k registraci**.

          ![Kurz Azure Data Catalog – procházení a výběr objektů](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Vyberte možnost**Zahrnout náhled**, pokud chcete zahrnout snímek náhledu dat. Snímek zahrnuje až 20 záznamů z každé tabulky a zkopíruje se do katalogu.

    5. Vyberte možnost **Zahrnout profil dat**, pokud chcete zahrnout snímek statistik objektu pro profil dat (např.: minimální, maximální a průměrné hodnoty sloupce, počet řádků).

    6. V **přidat značky** zadejte **prodeje, produkt, azure sql**. Tím se přidají vyhledávací značky pro tyto datové assety. Značky jsou skvělý způsob, jak pomoci uživatelům najít registrovaný zdroj dat.

    7. Zadejte jméno **experta** na tyto data (volitelné).

          ![Kurz Azure Data Catalog – objekty k registraci](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Vyberte **ZAREGISTROVAT**. Služba Azure Data Catalog zaregistruje vaše vybrané objekty. V tomto cvičení jsou registrovány vybrané objekty z vaše ukázková databáze Azure SQL. Registrační nástroj vyextrahuje metadata z datového assetu a zkopíruje je do služby Azure Data Catalog. Data zůstanou, kde aktuálně zůstane. Data zůstávají pod kontrolou správců a zásad systému původu.

          ![Azure Data Catalog – registrované objekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Pokud chcete zobrazit vaše registrované objekty zdrojů dat, vyberte **zobrazit portál**. Na portálu Azure Data Catalog, zkontrolujte, jestli se všechny čtyři tabulky a databáze v mřížkovém zobrazení (Ověřte, zda je panel hledání vymazat).

        ![Objekty na portálu Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

V tomto cvičení jste zaregistrovali objekty z ukázkové databáze Azure SQL, tak, aby se snadno zjistit uživatelé ve vaší organizaci.

V dalším cvičení se dozvíte, jak zjistit registrované datové assety.

## <a name="discover-data-assets"></a>Zjišťování datových assetů

Funkce zjišťování ve službě Azure Data Catalog používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní charakteristiky, jako jsou experti, typ zdroje dat, typ objektu a značky, abyste zobrazili datové assety odpovídající těmto charakteristikám, a tak omezit výsledky vyhledávání.

Pomocí kombinace vyhledávání a filtrování vám umožní rychle procházet zdroje dat, která jsou registrovaná v Azure Data Catalog.

V tomto cvičení použijete portál služby Azure Data Catalog ke zjištění datových assetů, které jste zaregistrovali v předchozím cvičení. Podrobnosti o syntaxi vyhledávání naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

Následuje několik příkladů, jak zjistit datové assety v katalogu.  

### <a name="discover-data-assets-with-basic-search"></a>Zjištění datových assetů pomocí základního vyhledávání

Základní vyhledávání vám pomůže prohledat katalog pomocí jednoho nebo více hledaných výrazů. Ve výsledcích se zobrazí veškeré assety, které odpovídají jakékoli vlastnosti jednoho nebo více zadaných výrazů.

1. Vyberte **Domů** na portálu Azure Data Catalog. Pokud jste zavřeli webový prohlížeč, přejděte [domovskou stránku Azure Data Catalog](https://www.azuredatacatalog.com).

2. Do vyhledávacího pole zadejte `product` a stiskněte klávesu **ENTER**.

    ![Azure Data Catalog – základní textové vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Ověřte, že se všechny čtyři tabulky a databáze ve výsledcích. Můžete přepínat mezi **zobrazení mřížky** a **zobrazení seznamu** výběrem tlačítka na panelu nástrojů, jak je znázorněno na následujícím obrázku. Všimněte si, že hledané klíčové slovo je ve výsledcích vyhledávání zvýrazněno, protože možnost **Zvýraznit** je **ZAPNUTO**. Ve výsledcích vyhledávání můžete také upřesnit **počet výsledků na stránku**.

    ![Azure Data Catalog – výsledky základního textového vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Panel **Vyhledávání** se nachází na levé straně a panel **Vlastnosti** na pravé. Na panelu **Vyhledávání** lze změnit kritéria vyhledávání a filtrovat výsledky. Na panelu **Vlastnosti** se zobrazují vlastnosti vybraného objektu v mřížce nebo seznamu.

4. Vyberte **produktu** ve výsledcích hledání. Vyberte **ve verzi Preview**, **sloupce**, **profil dat**, a **dokumentaci** karty, nebo vyberte šipku a rozbalte spodní podokno.  

    ![Azure Data Catalog – spodní podokno](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Na kartě **Náhled** uvidíte náhled dat v tabulce **Produkt**.  
5. Vyberte **sloupce** kartu Podrobnosti o sloupcích (například **název** a **datový typ**) v datovém assetu.

6. Vyberte **profil dat** kartu a zobrazíte profilaci dat (například: počet řádků, velikost dat či minimální hodnota v sloupci) v datovém assetu.

### <a name="discover-data-assets-with-property-scoping"></a>Zjištění datových assetů pomocí zkoumání vlastností

Funkce zkoumání vlastností vám pomůže zjistit datové assety v případě, že se hledaný výraz shoduje se zadanou vlastností.

1. Ve části **Filtry** pod položkou **Typ objektu** vymažte filtr **Tabulka**.  

2. Do vyhledávacího pole zadejte `tags:product` a stiskněte klávesu **ENTER**. Všechny vlastnosti, které lze využít při vyhledávání v katalogu dat, naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

3. Zkontrolujte, jestli se tabulky a databáze ve výsledcích.  

    ![Data Catalog – výsledky vyhledávání funkce zkoumání vlastnost](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Uložení vyhledávání

1. V **hledání** v podokně **aktuální hledání** části, zadejte název pro hledání a vybrat **Uložit**.

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

3. Stiskněte **ENTER**.

4. Zkontrolujte, jestli se **produktu**, **ProductCategory**, a **ProductDescription** tabulky a Azure SQL database, které jste zaregistrovali v výsledky hledání.

    ![Azure Data Catalog – výsledky vyhledávání porovnání](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zobrazit [jak zjistit datové assety](data-catalog-how-to-discover.md) podrobné informace o zjišťování datových assetů. Další informace o syntaxi vyhledávání naleznete v tématu [referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Přidání poznámek k datovým assetům

V tomto cvičení použijete portál služby Azure Data Catalog umožňuje anotaci (přidat informace, jako jsou popisy, značky či experti) existující datové assety v katalogu. Poznámky doplňují strukturální metadata extrahovaná ze zdroje dat během registrace. Poznámka datové assety velmi usnadňuje zjišťování a pochopení.

V tomto cvičení přidáte poznámky k jednomu datovému assetu (ProductPhoto). Doplníte k datovému assetu ProductPhoto popisný název a popis.  

1. Přejděte [domovskou stránku Azure Data Catalog](https://www.azuredatacatalog.com) a vyhledejte pomocí výrazu `tags:product` do datové assety, které jste registrovali.

2. Vyberte **ProductModel** ve výsledcích hledání.  

3. Zadejte **Obrázky produktu** pro **Popisný název** a jako **Popis** zadejte **Fotografie produktu pro marketingové materiály**.

    ![Azure Data Catalog – popis ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **Popis** pomáhá ostatním zjistit datový asset a porozumět tomu, proč a jak vybraný datový asset používat. Můžete také přidat další značky a zobrazit sloupce. Můžete vyhledávat a filtrovat zdroje dat pomocí popisných metadat, jsme přidali do katalogu.

Můžete také provést následující kroky na této stránce:

* Přidání expertů k datovým assetům. Vyberte **přidat** v **odborníků** oblasti.

* Přidání značek na úrovni datové sady. Vyberte **přidat** v **značky** oblasti. Značka může být značka uživatele nebo značka glosáře. Data Catalog Standard Edition zahrnuje obchodní glosář, který pomáhá správcům katalogu definovat centrální obchodní taxonomii. Uživatelé katalogu mohou poté opatřit poznámkami datové assety pomocí termínů v glosáři. Další informace najdete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md)

* Přidání značek na úrovni sloupce. Vyberte **přidat** pod **značky** pro sloupec, který chcete přidat poznámku.

* Přidání popisu na úrovni sloupce. Zadejte **Popis** pro sloupec. Můžete také zobrazit metadata popisu extrahovaná ze zdroje dat.

* Přidejte informace pro **Žádost o přístup**, které uživatelům dají návod, jak požádat o přístup k datovým assetům.
  
* Vyberte kartu **Dokumentace** a dodejte dokumentaci pro datový asset. Funkce dokumentace ve službě Azure Data Catalog vám umožňuje použít katalog dat jako úložiště obsahu. Můžete tak mít po ruce podrobné popisy a návody k registrovaným datovým assetům.
  
Můžete také přidat poznámku k více datovým assetům. Můžete například vybrat všechny datové assety, které jste zaregistrovali, a zadat k nim jednoho experta.

![Azure Data Catalog – přidání poznámky k více datovým assetům](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog podporuje crowdsourcingový přístup k přidávání poznámek. Každý uživatel katalogu dat může přidat značky (uživatele nebo glosáře), popisy a další metadata. Díky tomu uživatelé přidat pohledu na datový prostředek a jeho použití a s ostatními uživateli sdílet tuto perspektivu.

Podrobné informace o přidávání poznámek k datovým assetům najdete v článku [Jak přidat poznámky k datovým assetům](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Připojení k datovým assetům

V tomto cvičení otevřete datové assety v integrovaném klientském nástroji (Excel) i neintegrovaném nástroji (SQL Server Management Studio) s použitím informací o připojení.

> [!NOTE]
> Je důležité pamatovat, že Azure Data Catalog vám nedává přístup k samotnému zdroji dat – pouze vám usnadňuje tento zdroj dat zjistit a porozumět jeho funkci. Když se připojíte ke zdroji dat, klientská aplikace, kterou si vyberte, použije podle potřeby vaše přihlašovací údaje systému Windows nebo vás vyzve k zadání přihlašovacích údajů. Pokud vám nebyl dříve udělen přístup k tomuto zdroji dat, bude vám muset být udělen, abyste se mohli připojit.

### <a name="connect-to-a-data-asset-from-excel"></a>Připojení k datovému assetu z Excelu

1. Ve výsledcích vyhledávání vyberte možnost **Product**. Vyberte **Open In** na panelu nástrojů a vyberte **Excel**.

    ![Azure Data Catalog – připojení k datovému assetu](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Vyberte **otevřít** v automaticky otevřeném okně stahování. Přesný postup a prostředí se může mírně lišit v závislosti na prohlížeči.

3. V **upozornění zabezpečení Microsoft Excel** okně **povolit**.

    ![Azure Data Catalog – automaticky otevřené okno zabezpečení Excelu](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Ponechte výchozí nastavení **Import dat** dialogové okno a vyberte **OK**.

    ![Azure Data Catalog – import dat aplikace Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Zobrazení zdroje dat v Excelu.

    ![Azure Data Catalog – tabulka produktů v Excelu](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

V tomto cvičení jste se připojili k datovým assetům zjištěným pomocí služby Azure Data Catalog. Portál služby Azure Data Catalog vám umožňuje připojit se přímo pomocí klientských aplikací integrovaných do jeho nabídky **Otevřít v aplikaci**. Také se ale můžete připojit pomocí jakékoli aplikace, kterou si vyberete, pomocí informací o umístění připojení zahrnutých v metadatech assetu. Například můžete použít SQL Server Management Studio pro připojení k databázi Azure SQL pro přístup k datům v datových assetech zaregistrovali v tomto kurzu.

1. Otevřete **SQL Server Management Studio**.

2. V dialogovém okně **Připojit k serveru** zadejte název serveru z podokna **Vlastnosti** na portálu Azure Data Catalog.

3. Použijte příslušné ověření a přihlašovací údaje pro přístup k datovému assetu. Pokud nemáte přístup, použijte informace uvedené v poli **Žádost o přístup** a vyžádejte si jej.

    ![Azure Data Catalog – žádost o přístup](media/register-data-assets-tutorial/data-catalog-request-access.png)

Vyberte **zobrazit připojovací řetězce** k zobrazení a zkopírování do schránky a použít ve své aplikaci připojovací řetězce ADO.NET, ODBC a OLEDB.

## <a name="manage-data-assets"></a>Správa datových assetů

V tomto kroku se dozvíte, jak nastavit zabezpečení datových assetů. Data Catalog nedává uživatelům přístup k samotným datům. O udělení přístupu k datům rozhoduje vlastník zdroje dat.

Služba Data Catalog vám umožní zjistit zdroje dat a zobrazit metadata související se zdroji zaregistrovanými v katalogu. Mohou ovšem nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. Pro tyto případy můžete pomocí služby Data Catalog převzít vlastnictví registrovaných datových assetů a řídit viditelnost assetů, které vlastníte.

> [!NOTE]
> Možnosti správy popsané v tomto cvičení jsou k dispozici pouze v Azure Data Catalog Standard Edition, nikoli v bezplatné Free Edition.
> Ve službě Azure Data Catalog může převzít vlastnictví datových prostředků, přidat spoluvlastníky k datovým prostředkům a nastavit viditelnost datových prostředků.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Převzetí vlastnictví datových assetů a omezení viditelnosti

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com). Do textového pole **Vyhledávání** zadejte `tags:cycles` a stiskněte klávesu **ENTER**.

2. v seznamu výsledků vyberte položku a vyberte **převzít vlastnictví** na panelu nástrojů.

3. V **správu** část **vlastnosti** panelu, vyberte **převzít vlastnictví**.

    ![Azure Data Catalog – převzetí vlastnictví](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Chcete-li omezit viditelnost, zvolte **vlastníci a tito uživatelé** v **viditelnost** a vyberte **přidat**. Do textového pole zadejte e-mailové adresy uživatelů a stiskněte **ENTER**.

    ![Azure Data Catalog – omezení přístupu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Odstranění datových assetů

V tomto cvičení použijete portál služby Azure Data Catalog k odebrání náhledu dat z registrovaných datových assetů a k odstranění datových assetů z katalogu.

Ve službě Azure Data Catalog je možné odstranit jednotlivý asset nebo více assetů.

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com).

2. V **hledání** textové pole, zadejte `tags:cycles` a vyberte **ENTER**.

3. V seznamu výsledků vyberte položku a vyberte **odstranit** na panelu nástrojů, jak je znázorněno na následujícím obrázku:

    ![Azure Data Catalog – odstranění položky mřížky](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Pokud používáte zobrazení seznamu, je zaškrtávací políčko nalevo od položky, jak je znázorněno na následujícím obrázku:

    ![Azure Data Catalog – odstranění položky seznamu](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Můžete také vybrat a odstranit více datových assetů, jak ukazuje následující obrázek:

    ![Azure Data Catalog – odstranění více datových assetů](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Výchozí chování katalogu je umožnit každému uživateli zaregistrovat libovolný zdroj dat a umožnit každému uživateli odstranit libovolný zaregistrovaný datový asset. Možnosti správy zahrnuté do Azure Data Catalog Standard Edition poskytují další možnosti pro převzetí vlastnictví assetů, omezení, kdo může assety zjistit, a omezení, kdo může assety odstranit.

## <a name="summary"></a>Souhrn

V tomto kurzu jste prozkoumali základní možnosti služby Azure Data Catalog včetně registrace, přidávání poznámek, zjišťování a správy datových assetů organizace. Nyní, když jste dokončili kurz, je čas začít. Začít můžete ještě dnes registrací zdrojů dat, na která spoléháte vy a váš tým, a pozváním kolegů k používání katalogu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Podporované zdroje dat](data-catalog-dsr.md)