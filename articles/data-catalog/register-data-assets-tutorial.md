---
title: 'Kurz: registrace datových assetů v Azure Data Catalog'
description: V tomto kurzu se dozvíte, jak zaregistrovat datové assety v Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675001"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Kurz: registrace datových assetů v Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

V tomto kurzu pomocí registračního nástroje zaregistrujete datové assety z ukázkové databáze pomocí katalogu. Registrace je proces extrahování klíčových strukturálních metadat – například názvy, typy a umístění – ze zdroje dat a assetů v tomto zdroji a zkopírování těchto metadat do katalogu. Zdroj dat a datové assety zůstanou tam, kde jsou, ale jejich metadata použije katalog k tomu, aby byly tyto objekty snadněji zjistitelné a srozumitelnější.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Registrace datových assetů 
> * Prohledat datové prostředky
> * Přidání poznámek k datovým assetům
> * Připojení k datovým assetům
> * Správa datových assetů
> * Odstranění datových assetů

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, je nutné dokončit [rychlé](register-data-assets-tutorial.md)zprovoznění.

* Předplatné [Microsoft Azure](https://azure.microsoft.com/)
* Musíte mít vlastního [klienta Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Pokud chcete nastavit Data Catalog, musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.

## <a name="register-data-assets"></a>Registrace datových assetů

### <a name="register-a-data-source"></a>Registrace zdroje dat

Zaregistrujete datové assety (tabulky) z [ukázkové databáze](../azure-sql/database/single-database-create-quickstart.md) pro Azure SQL Database, ale můžete použít libovolný podporovaný zdroj dat, pokud dáváte přednost práci s daty, která jsou známá a jsou relevantní pro vaši roli. Seznam podporovaných zdrojů dat naleznete v tématu [Podporované zdroje dat](data-catalog-dsr.md).

Název databáze, který používáme v tomto kurzu, je *RLSTest*.

Datové assety z ukázkové databáze teď můžete registrovat pomocí Azure Data Catalog.

1. Přejít na [domovskou stránku Azure Data Catalog](http://azuredatacatalog.com) a vyberte **publikovat data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Vyberte možnost **Spustit aplikaci** a stáhněte, nainstalujte a spusťte registrační nástroj na svém počítači.

   ![Azure Data Catalog – tlačítko Spustit](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na **úvodní** stránce vyberte **Přihlásit** se a zadejte svoje přihlašovací údaje.

    ![Azure Data Catalog – Úvodní stránka](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na stránce **Microsoft Azure Data Catalog** vyberte **SQL Server** a **Další**.

    ![Azure Data Catalog – zdroje dat](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Zadejte vlastnosti připojení SQL Server pro ukázku databáze v Azure SQL Database a vyberte **připojit**.

   ![Azure Data Catalog – nastavení připojení k SQL Serveru](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zaregistrujte metadata datového assetu. V tomto příkladu zaregistrujete objekty **produktu** z ukázkového oboru názvů:

   1. Ve stromové struktuře **hierarchie serveru** rozbalte ukázku databáze a vyberte **tabulky SalesLT**.

   2. Pomocí kombinace kláves CTRL + SELECT vyberte **Product**, **ProductCategory**, **ProductDescription** a **ProductModel** .

   3. Vyberte **šipku vybrané pro přesun** ( **>** ). Tím se přesunou všechny vybrané objekty do seznamu **Objekty k registraci**.

      ![Kurz Azure Data Catalog – procházení a výběr objektů](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. Vyberte možnost **Zahrnout náhled**, pokud chcete zahrnout snímek náhledu dat. Snímek obsahuje až 20 záznamů z každé tabulky a zkopíruje se do katalogu.

   5. Vyberte možnost **Zahrnout profil dat**, pokud chcete zahrnout snímek statistik objektu pro profil dat (např.: minimální, maximální a průměrné hodnoty sloupce, počet řádků).

   6. Do pole **Přidat značky** zadejte **prodej, produkt a Azure SQL**. Tím se přidají vyhledávací značky pro tyto datové assety. Značky jsou skvělý způsob, jak pomoci uživatelům najít registrovaný zdroj dat.

   7. Zadejte jméno **experta** na tyto data (volitelné).

      ![Kurz Azure Data Catalog – objekty k registraci](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. Vyberte **Registrovat**. Služba Azure Data Catalog zaregistruje vaše vybrané objekty. V tomto cvičení jsou zaregistrovány vybrané objekty z ukázkové databáze. Registrační nástroj vyextrahuje metadata z datového assetu a zkopíruje je do služby Azure Data Catalog. Data zůstanou tam, kde se aktuálně zůstanou. Data zůstávají pod kontrolou správců a zásad systému původu.

      ![Azure Data Catalog – registrované objekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Chcete-li zobrazit registrované objekty zdroje dat, vyberte možnost **Zobrazit portál**. Na portálu Azure Data Catalog potvrďte, že se v zobrazení tabulky zobrazí všechny čtyři tabulky a databáze (ověřte, zda je panel hledání jasný).

       ![Objekty na portálu Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

V tomto cvičení jste zaregistrovali objekty z ukázkové databáze pro Azure SQL Database tak, aby je uživatelé ve vaší organizaci mohli snadno zjistit.

V dalším cvičení se dozvíte, jak zjistit registrované datové assety.

## <a name="discover-data-assets"></a>Zjišťování datových assetů

Funkce zjišťování ve službě Azure Data Catalog používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní charakteristiky, jako jsou experti, typ zdroje dat, typ objektu a značky, abyste zobrazili datové assety odpovídající těmto charakteristikám, a tak omezit výsledky vyhledávání.

Pomocí kombinace hledání a filtrování můžete rychle přejít ke zdrojům dat, které jsou zaregistrované v Azure Data Catalog.

V tomto cvičení použijete portál služby Azure Data Catalog ke zjištění datových assetů, které jste zaregistrovali v předchozím cvičení. Podrobnosti o syntaxi vyhledávání naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

Následuje několik příkladů, jak zjistit datové assety v katalogu.  

### <a name="discover-data-assets-with-basic-search"></a>Zjištění datových assetů pomocí základního vyhledávání

Základní vyhledávání vám pomůže prohledat katalog pomocí jednoho nebo více hledaných výrazů. Ve výsledcích se zobrazí veškeré assety, které odpovídají jakékoli vlastnosti jednoho nebo více zadaných výrazů.

1. Na Azure Data Catalogovém portálu vyberte **Domů** . Pokud jste zavřeli webový prohlížeč, přejdete na [stránku Azure Data Catalog domovské stránce](https://www.azuredatacatalog.com).

2. Do vyhledávacího pole zadejte `product` a stiskněte klávesu **ENTER**.

   ![Azure Data Catalog – základní textové vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Ověřte, že jsou ve výsledcích zobrazeny všechny čtyři tabulky a databáze. Můžete přepínat mezi **zobrazením mřížky** a **zobrazením seznamu** výběrem tlačítek na panelu nástrojů, jak je znázorněno na následujícím obrázku. Všimněte si, že hledané klíčové slovo je ve výsledcích vyhledávání zvýrazněno, protože možnost **Zvýraznit** je **ZAPNUTO**. Ve výsledcích vyhledávání můžete také upřesnit **počet výsledků na stránku**.

   ![Azure Data Catalog – výsledky základního textového vyhledávání](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   Panel **Vyhledávání** se nachází na levé straně a panel **Vlastnosti** na pravé. Na panelu **Vyhledávání** lze změnit kritéria vyhledávání a filtrovat výsledky. Na panelu **Vlastnosti** se zobrazují vlastnosti vybraného objektu v mřížce nebo seznamu.

4. Ve výsledcích hledání vyberte **produkt** . Vyberte kartu **Náhled**, **sloupce**, **profil dat** a **dokumentace** , nebo výběrem šipky rozbalte dolní podokno.  

   ![Azure Data Catalog – spodní podokno](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   Na kartě **Náhled** uvidíte náhled dat v tabulce **Produkt**.

5. Vyberte kartu **sloupce** pro vyhledání podrobností o sloupcích (například **název** a **datový typ**) v datovém assetu.

6. Vyberte kartu **profil dat** a zobrazte si profilaci dat (například počet řádků, velikost dat nebo minimální hodnotu ve sloupci) v datovém assetu.

### <a name="discover-data-assets-with-property-scoping"></a>Zjištění datových assetů pomocí zkoumání vlastností

Funkce zkoumání vlastností vám pomůže zjistit datové assety v případě, že se hledaný výraz shoduje se zadanou vlastností.

1. Ve části **Filtry** pod položkou **Typ objektu** vymažte filtr **Tabulka**.  

2. Do vyhledávacího pole zadejte `tags:product` a stiskněte klávesu **ENTER**. Všechny vlastnosti, které lze využít při vyhledávání v katalogu dat, naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

3. Ověřte, že se ve výsledcích zobrazují tabulky a databáze.  

   ![Data Catalog – výsledky vyhledávání funkce zkoumání vlastnost](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Uložení vyhledávání

1. V podokně **hledání** v části **aktuální vyhledávání** zadejte název hledání a vyberte **Uložit**.

   ![Azure Data Catalog – uložení vyhledávání](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Zkontrolujte, jestli se uložené vyhledávání zobrazuje v sekci **Uložená vyhledávání**.

3. Vyberte jednu z akcí, které lze s uloženým vyhledáváním provést (**Přejmenovat**, **Odstranit**, **Uložit jako výchozí**).

### <a name="grouping-with-parentheses"></a>Seskupování pomocí závorek

Závorky lze použít k seskupení částí dotazu za účelem logické izolace, zejména ve spojení s logickými operátory.

1. Do vyhledávacího pole zadejte `name:product AND (tags:product AND objectType:table)` a stiskněte klávesu **ENTER**.

2. Zkontrolujte, jestli se vám ve výsledcích vyhledávání zobrazuje pouze tabulka **Product**.

   ![Azure Data Catalog – vyhledávání seskupení](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Relační operátory

S pomocí operátorů porovnání lze použít porovnávání jiné než rovnost pro vlastnosti, které mají typ dat číslo nebo datum.

1. Do vyhledávacího pole zadejte `lastRegisteredTime:>"06/09/2016"`.

2. Pod položkou **Typ objektu** vymažte filtr **Tabulka**.

3. Stiskněte klávesu **ENTER**.

4. Potvrďte, že jste viděli tabulky **Product**, **ProductCategory** a **ProductDescription** a SQL Database, kterou jste zaregistrovali ve výsledcích hledání.

   ![Azure Data Catalog – výsledky vyhledávání porovnání](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Podrobné informace o zjišťování datových assetů najdete v tématu [Jak zjistit datové assety](data-catalog-how-to-discover.md) . Další informace o syntaxi hledání naleznete v tématu [Data Catalog reference syntaxe vyhledávání](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Přidání poznámek k datovým assetům

V tomto cvičení použijete portál Azure Data Catalog k přidávání poznámek (přidávání informací, jako jsou popisy, značky nebo odborníky) existujících datových assetů v katalogu. Poznámky doplňují strukturální metadata extrahovaná ze zdroje dat během registrace. Anotace usnadňuje zjišťování a pochopení datových prostředků.

V tomto cvičení přidáte poznámky k jednomu datovému assetu (ProductPhoto). Doplníte k datovému assetu ProductPhoto popisný název a popis.  

1. Přejít na [domovskou stránku Azure Data Catalog](https://www.azuredatacatalog.com) a vyhledat `tags:product` datové assety, které jste zaregistrovali.

2. Ve výsledcích hledání vyberte **ProductModel** .  

3. Zadejte **Obrázky produktu** pro **Popisný název** a jako **Popis** zadejte **Fotografie produktu pro marketingové materiály**.

    ![Azure Data Catalog – popis ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **Popis** pomáhá ostatním zjistit datový asset a porozumět tomu, proč a jak vybraný datový asset používat. Můžete také přidat další značky a zobrazit sloupce. Zdroje dat můžete hledat a filtrovat pomocí popisných metadat, která jste přidali do katalogu.

Na této stránce můžete také provést následující kroky:

* Přidání expertů k datovým assetům. v oblasti **expertů** vyberte **Přidat** .

* Přidání značek na úrovni datové sady. v oblasti **značky** vyberte **Přidat** . Značka může být značka uživatele nebo značka glosáře. Data Catalog Standard Edition zahrnuje obchodní glosář, který pomáhá správcům katalogu definovat centrální obchodní taxonomii. Uživatelé katalogu mohou poté opatřit poznámkami datové assety pomocí termínů v glosáři. Další informace najdete v tématu [Nastavení podnikového glosáře pro řízení označování](data-catalog-how-to-business-glossary.md) .

* Přidání značek na úrovni sloupce. u sloupce, který chcete opatřit poznámkami, vyberte **Přidat** pod **značky** .

* Přidání popisu na úrovni sloupce. Zadejte **Popis** pro sloupec. Můžete také zobrazit metadata popisu extrahovaná ze zdroje dat.

* Přidejte informace pro **Žádost o přístup**, které uživatelům dají návod, jak požádat o přístup k datovým assetům.
  
* Vyberte kartu **Dokumentace** a dodejte dokumentaci pro datový asset. Funkce dokumentace ve službě Azure Data Catalog vám umožňuje použít katalog dat jako úložiště obsahu. Můžete tak mít po ruce podrobné popisy a návody k registrovaným datovým assetům.
  
Můžete také přidat poznámku k více datovým assetům. Můžete například vybrat všechny datové assety, které jste zaregistrovali, a zadat k nim jednoho experta.

![Azure Data Catalog – přidání poznámky k více datovým assetům](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog podporuje crowdsourcingový přístup k přidávání poznámek. Každý Data Catalog uživatel může přidávat značky (uživatele nebo Glosář), popisy a další metadata. Díky tomu mohou uživatelé přidat perspektivu k datovému assetu a jeho použití a sdílet tuto perspektivu s ostatními uživateli.

Podrobné informace o přidávání poznámek k datovým assetům najdete v článku [Jak přidat poznámky k datovým assetům](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Připojení k datovým assetům

V tomto cvičení otevřete datové assety v integrovaném klientském nástroji (Excel) i neintegrovaném nástroji (SQL Server Management Studio) s použitím informací o připojení.

> [!NOTE]
> Je důležité pamatovat, že Azure Data Catalog vám nedává přístup k samotnému zdroji dat – pouze vám usnadňuje tento zdroj dat zjistit a porozumět jeho funkci. Když se připojíte ke zdroji dat, klientská aplikace, kterou si vyberte, použije podle potřeby vaše přihlašovací údaje systému Windows nebo vás vyzve k zadání přihlašovacích údajů. Pokud vám nebyl dříve udělen přístup k tomuto zdroji dat, bude vám muset být udělen, abyste se mohli připojit.

### <a name="connect-to-a-data-asset-from-excel"></a>Připojení k datovému assetu z Excelu

1. Ve výsledcích vyhledávání vyberte možnost **Product**. na panelu nástrojů vyberte **otevřít** a vyberte **Excel**.

   ![Azure Data Catalog – připojení k datovému assetu](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. V automaticky otevíraném okně stahování vyberte **otevřít** . Přesný postup a prostředí se může mírně lišit v závislosti na prohlížeči.

3. V okně **Upozornění zabezpečení aplikace Microsoft Excel** vyberte možnost **Povolit**.

   ![Azure Data Catalog – automaticky otevřené okno zabezpečení Excelu](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. V dialogovém okně **Import dat** ponechte výchozí hodnoty a vyberte **OK**.

   ![Azure Data Catalog – import dat aplikace Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Zobrazení zdroje dat v Excelu.

   ![Azure Data Catalog – tabulka produktů v Excelu](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

V tomto cvičení jste se připojili k datovým assetům zjištěným pomocí služby Azure Data Catalog. Portál služby Azure Data Catalog vám umožňuje připojit se přímo pomocí klientských aplikací integrovaných do jeho nabídky **Otevřít v aplikaci**. Také se ale můžete připojit pomocí jakékoli aplikace, kterou si vyberete, pomocí informací o umístění připojení zahrnutých v metadatech assetu. Můžete například použít SQL Server Management Studio pro připojení k Azure SQL Database pro přístup k datům v datových assetech zaregistrovaných v tomto kurzu.

1. Otevřete **SQL Server Management Studio**.

2. V dialogovém okně **Připojit k serveru** zadejte název serveru z podokna **Vlastnosti** na portálu Azure Data Catalog.

3. Použijte příslušné ověření a přihlašovací údaje pro přístup k datovému assetu. Pokud nemáte přístup, použijte informace uvedené v poli **Žádost o přístup** a vyžádejte si jej.

   ![Azure Data Catalog – žádost o přístup](media/register-data-assets-tutorial/data-catalog-request-access.png)

Vyberte **Zobrazit připojovací řetězce** k zobrazení a zkopírování připojovacích řetězců ADO.NET, ODBC a OLEDB do schránky pro použití ve vaší aplikaci.

## <a name="manage-data-assets"></a>Správa datových assetů

V tomto kroku se dozvíte, jak nastavit zabezpečení datových assetů. Data Catalog nedává uživatelům přístup k samotným datům. O udělení přístupu k datům rozhoduje vlastník zdroje dat.

Služba Data Catalog vám umožní zjistit zdroje dat a zobrazit metadata související se zdroji zaregistrovanými v katalogu. Mohou ovšem nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. V těchto scénářích můžete použít Data Catalog k převzetí vlastnictví registrovaných datových assetů a řízení viditelnosti prostředků, které vlastníte.

> [!NOTE]
> Možnosti správy popsané v tomto cvičení jsou k dispozici pouze v Azure Data Catalog Standard Edition, nikoli v bezplatné Free Edition.
> Ve službě Azure Data Catalog může převzít vlastnictví datových prostředků, přidat spoluvlastníky k datovým prostředkům a nastavit viditelnost datových prostředků.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Převzetí vlastnictví datových assetů a omezení viditelnosti

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com). Do textového pole **hledání** zadejte `tags:cycles` a stiskněte klávesu **ENTER**.

2. V seznamu výsledků vyberte položku a na panelu nástrojů vyberte **převzít vlastnictví** .

3. V části **Správa** na panelu **vlastnosti** vyberte **převzít vlastnictví**.

    ![Azure Data Catalog – převzetí vlastnictví](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Chcete-li omezit viditelnost, zvolte **vlastníci & tito uživatelé** v části **viditelnost** a vyberte **Přidat**. Do textového pole zadejte e-mailové adresy uživatelů a stiskněte **ENTER**.

    ![Azure Data Catalog – omezení přístupu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Odstranění datových assetů

V tomto cvičení použijete portál služby Azure Data Catalog k odebrání náhledu dat z registrovaných datových assetů a k odstranění datových assetů z katalogu.

Ve službě Azure Data Catalog je možné odstranit jednotlivý asset nebo více assetů.

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com).

2. Do textového pole **hledání** zadejte `tags:cycles` a vyberte **ENTER**.

3. V seznamu výsledků vyberte položku a na panelu nástrojů vyberte **Odstranit** , jak je znázorněno na následujícím obrázku:

   ![Azure Data Catalog – odstranění položky mřížky](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   Pokud používáte zobrazení seznamu, je zaškrtávací políčko nalevo od položky, jak je znázorněno na následujícím obrázku:

   ![Azure Data Catalog – odstranění položky seznamu](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   Můžete také vybrat a odstranit více datových assetů, jak ukazuje následující obrázek:

   ![Azure Data Catalog – odstranění více datových assetů](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Výchozí chování katalogu je umožnit každému uživateli zaregistrovat libovolný zdroj dat a umožnit každému uživateli odstranit libovolný zaregistrovaný datový asset. Možnosti správy zahrnuté do Azure Data Catalog Standard Edition poskytují další možnosti pro převzetí vlastnictví assetů, omezení, kdo může assety zjistit, a omezení, kdo může assety odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Postupujte podle kroků [odebrat datové prostředky](#remove-data-assets) a vyčistěte všechny prostředky, které jste mohli použít při použití tohoto kurzu.

## <a name="summary"></a>Souhrn

V tomto kurzu jste prozkoumali základní možnosti služby Azure Data Catalog včetně registrace, přidávání poznámek, zjišťování a správy datových assetů organizace. Nyní, když jste dokončili kurz, je čas začít. Začít můžete ještě dnes registrací zdrojů dat, na která spoléháte vy a váš tým, a pozváním kolegů k používání katalogu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Podporované zdroje dat](data-catalog-dsr.md)
