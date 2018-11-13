---
title: Začínáme s Azure Data Catalog
description: Koncový kurz představující scénáře a možnosti služby Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: dd3cb108844cd009d74de42630511073021d94b1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238484"
---
# <a name="get-started-with-azure-data-catalog"></a>Začínáme s Azure Data Catalog
Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém pro registraci a zjišťování podnikových datových assetů. Podrobný přehled naleznete v části [Co je Azure Data Catalog?](data-catalog-what-is-data-catalog.md).

V tomto kurzu se naučíte pracovat se službou Azure Data Catalog. V tomto kurzu si vyzkoušíte následující postupy:

| Postup | Popis |
|:--- |:--- |
| [Zřízení datového katalogu](#provision-data-catalog) |V tomto postupu zřídíte nebo nastavíte službu Azure Data Catalog. Tento krok provedete pouze v případě, že jste tento katalog dosud nenastavili. Je povolen pouze jeden katalog dat na organizaci (doména služby Microsoft Azure Active Directory), a to i tehdy, když je k vašemu účtu Azure přiřazeno více předplatných. |
| [Registrace datových prostředků](#register-data-assets) |V tomto postupu zaregistrujete datové assety z ukázkové databáze AdventureWorks2014 do katalogu dat. Registrace je proces extrakce klíčových strukturálních metadat – například názvy, typy a umístění – ze zdroje dat a zkopírování těchto metadat do katalogu. Zdroj dat a datové assety zůstanou tam, kde jsou, ale jejich metadata použije katalog k tomu, aby byly tyto objekty snadněji zjistitelné a srozumitelnější. |
| [Zjišťování datových prostředků](#discover-data-assets) |V tomto postupu použijete portál služby Azure Data Catalog ke zjištění datových assetů, které jste v předchozím kroku zaregistrovali. Po registraci zdroje dat ve službě Azure Data Catalog budou jeho metadata službou indexována, aby mohli uživatelé potřebná data snáze najít. |
| [Přidání poznámek k datovým prostředkům](#annotate-data-assets) |V tomto postupu přidáte k datovým assetům poznámky (údaje jako popisy, značky, dokumentace a odborníci na daný zdroj). Tyto údaje doplňují metadata extrahovaná přímo ze zdroje dat a činí tak zdroj dat srozumitelnějším pro více lidí. |
| [Připojení k datovým prostředkům](#connect-to-data-assets) |V tomto postupu otevřete datové assety v integrovaných klientských nástrojích (např. Excel a SQL Server Data Tools) i neintegrovaném nástroji (SQL Server Management Studio). |
| [Správa datových prostředků](#manage-data-assets) |V tomto postupu nastavíte zabezpečení svých datových assetů. Služba Data Catalog neposkytuje uživatelům přístup k samotným datům. O udělení přístupu k datům rozhoduje vlastník zdroje dat. <br/><br/> Služba Data Catalog vám umožní zjistit zdroje dat a zobrazit **metadata** související se zdroji registrovanými v katalogu. Mohou ovšem nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. Pro tyto případy můžete pomocí služby Data Catalog převzít vlastnictví registrovaných datových assetů v katalogu a následně řídit viditelnost assetů, které teď vlastníte. |
| [Odebrání datových prostředků](#remove-data-assets) |V tomto postupu se naučíte, jak odebrat datové assety z katalogu dat. |

## <a name="tutorial-prerequisites"></a>Požadavky kurzu
### <a name="azure-subscription"></a>Předplatné Azure
Pokud chcete nastavit službu Azure Data Catalog, musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.

Předplatná Azure vám pomohou organizovat přístup k prostředkům cloudové služby, jako je Azure Data Catalog. Zároveň vám pomohou řídit způsob, jak je používání prostředků vykazováno, fakturováno a placeno. Každé předplatné může mít jiné nastavení fakturace a plateb. Můžete tedy mít jiná předplatná a jiné plány pro různá oddělení, projekty, regionální pobočky a podobně. Každá cloudová služba patří k nějakému předplatnému. Před nastavením služby Azure Data Catalog tedy potřebujete mít předplatné. Více informací naleznete v tématu [Správa účtů, předplatných a správních rolí](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Podrobnosti viz [bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

### <a name="azure-active-directory"></a>Azure Active Directory
Pokud chcete nastavit službu Azure Data Catalog, musíte se přihlásit pomocí uživatelského účtu Azure Active Directory (Azure AD). Musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.  

Azure AD umožní vaší firmě snadnou správu identity a přístupu, a to jak v cloudu, tak i místně. Pomocí jednoho pracovního nebo školního účtu se můžete přihlásit k jakékoli cloudové nebo místní webové aplikaci. Služba Azure Data Catalog ověřuje přihlášení pomocí Azure AD. Další informace naleznete v tématu [Co je Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Konfigurace zásad Azure Active Directory
Může nastat situace, že se budete moci přihlásit k portálu Azure Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat narazíte na chybovou zprávu, která vám přihlášení neumožní. K této chybě může dojít tehdy, když se nacházíte v podnikové síti nebo když se připojujete z místa mimo podnikovou síť.

Nástroj pro registraci používá *ověřování pomocí formulářů*, aby ověřil přihlášení uživatelů vůči službě Azure Active Directory. K úspěšnému přihlášení je nutné, aby správce Azure Active Directory v *zásadách globálního ověřování* povolil možnost ověřování pomocí formulářů.

Pomocí zásad globálního ověřování můžete povolovat ověřování zvlášť pro připojení z intranetu a extranetu, jak ukazuje následující obrázek. Pokud není povoleno ověřování pomocí formulářů pro síť, ze které se připojujete, může docházet k chybám přihlášení.

 ![Zásady globálního ověřování Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Další informace naleznete v článku o [konfiguraci zásad ověřování](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Zřízení datového katalogu
Můžete zřídit pouze jeden katalog dat na organizaci (doména Azure Active Directory). Pokud již tedy vlastník nebo spoluvlastník předplatného Azure, který patří k této doméně Azure Active Directory, katalog vytvořil, nebudete moci vytvořit nový katalog, přestože máte více předplatných Azure. Pokud chcete otestovat, jestli byl ve vaší doméně Azure Active Directory uživatelem vytvořen katalog dat, přejděte na [domovskou stránku Azure Data Catalog ](http://azuredatacatalog.com) a podívejte, jestli se vám tu katalog zobrazuje. Pokud byl pro vás už katalog vytvořen, přeskočte následující postup a přejděte do další sekce.    

1. Přejděte na [stránku služby Data Catalog](https://azure.microsoft.com/services/data-catalog) a klikněte na možnost **Začínáme**.
   
    ![Azure Data Catalog – marketingová cílová stránka](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Přihlaste se pomocí uživatelského účtu, který patří vlastníkovi nebo spoluvlastníkovi předplatného Azure. Po přihlášení se zobrazí následující stránka.
   
    ![Azure Data Catalog – zřízení katalogu dat](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Zadejte **název** katalogu dat,**předplatné**, které chcete použít, a **umístění** katalogu.
4. Rozbalte položku **Ceny** a vyberte **edici** služby Azure Data Catalog (Free nebo Standard).
    ![Azure Data Catalog – výběr edice](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Rozbalte položku **Uživatelé katalogu** a kliknutím na tlačítko **Přidat** přidejte pro katalog dat uživatele. Jste automaticky přidáni do této skupiny.
    ![Azure Data Catalog – uživatelé](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Rozbalte položku **Správci katalogu** a kliknutím na tlačítko**Přidat**přidejte pro katalog dat další správce. Jste automaticky přidáni do této skupiny.
    ![Azure Data Catalog – správci](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Kliknutím na tlačítko **Vytvořit katalog** vytvořte katalog dat pro svou organizaci. Po vytvoření katalogu dat se vám zobrazí jeho domovská stránka.
    ![Azure Data Catalog – vytvořeno](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Vyhledání datového katalogu na webu Azure Portal
1. Na samostatné kartě nebo v samostatném okně webového prohlížeče přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí stejného účtu, který jste v předchozím kroku použili k vytvoření katalogu dat.
2. Vyberte možnost **Procházet** a klikněte na**Data Catalog**.
   
    ![Azure Data Catalog – procházení Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Vidíte katalog dat, který jste vytvořili.
   
    ![Azure Data Catalog – zobrazení katalogu v seznamu](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Klikněte na katalog, který jste vytvořili. Na portálu se vám zobrazí okno **Data Catalog**.
   
   ![Azure Data Catalog – okno na portálu ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Můžete zobrazit vlastnosti katalogu dat a aktualizovat je. Můžete například kliknout na **Cenová úroveň** a změnit edici.
   
    ![Azure Data Catalog – cenová úroveň](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Ukázková databáze Adventure Works
V tomto kurzu zaregistrujete datové assety (tabulky) z ukázkové databáze AdventureWorks2014 pro databázový stroj SQL Server. Můžete ale použít jakýkoli jiný podporovaný zdroj dat, pokud si přejete pracovat s daty, která dobře znáte a jsou relevantní pro vaši roli. Seznam podporovaných zdrojů dat naleznete v tématu [Podporované zdroje dat](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Instalace databáze Adventure Works 2014 OLTP
Databáze Adventure Works podporuje scénáře standardního online zpracování transakcí pro smyšleného výrobce jízdních kol (Adventure Works Cycles), což zahrnuje produkty, prodej a nákup. V tomto kurzu zaregistrujete informace o produktech do služby Azure Data Catalog.

Postup instalace databáze Adventure Works:

1. Stáhněte soubor [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) na webu CodePlex.
2. Pokud chcete obnovit databázi na svém počítači, řiďte se pokyny v tématu [Obnovení zálohy databáze pomocí služby SQL Server Management Studio](https://msdn.microsoft.com/library/ms177429.aspx) nebo postupujte takto:
   1. Otevřete SQL Server Management Studio a připojte se k databázovému stroji SQL Server.
   2. Klikněte pravým tlačítkem myši na **Databáze** a vyberte možnost **Obnovit databázi**.
   3. V části **Obnovit databázi** vyberte pro **Zdroj** možnost **Zařízení** a klikněte na **Procházet**.
   4. V části **Výběr zálohovacích zařízení** klikněte na **Přidat**.
   5. Přejděte do složky, do níž jste uložili soubor **AdventureWorks2014.bak**, vyberte jej a kliknutím na tlačítko **OK** zavřete dialogové okno **Najít soubor zálohy**.
   6. Kliknutím na tlačítko **OK** zavřete dialogové okno **Výběr zálohovacích zařízení**.    
   7. Kliknutím na tlačítko **OK** zavřete dialogové okno **Obnovit databázi**.

Teď můžete pomocí služby Azure Data Catalog zaregistrovat datové assety z ukázkové databáze Adventure Works.

## <a name="register-data-assets"></a>Registrace datových assetů
V tomto cvičení použijete registrační nástroj k registraci datových assetů z databáze Adventure Works do katalogu. Registrace je proces extrahování klíčových strukturálních metadat – například názvy, typy a umístění – ze zdroje dat a assetů v tomto zdroji a zkopírování těchto metadat do katalogu. Zdroj dat a datové assety zůstanou tam, kde jsou, ale jejich metadata použije katalog k tomu, aby byly tyto objekty snadněji zjistitelné a srozumitelnější.

### <a name="register-a-data-source"></a>Registrace zdroje dat
1. Přejděte na [domovskou stránku služby Azure Data Catalog](http://azuredatacatalog.com) a klikněte na tlačítko **Publikovat data**.
   
   ![Azure Data Catalog – tlačítko Publikovat data](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Klikněte na tlačítko **Spustit aplikaci** a stáhněte, nainstalujte a spusťte na svém počítači registrační nástroj.
   
   ![Azure Data Catalog – tlačítko Spustit](media/data-catalog-get-started/data-catalog-launch-application.png)
3. Na stránce **Vítejte** klikněte na tlačítko **Přihlášení** a zadejte své přihlašovací údaje.     
   
    ![Azure Data Catalog – Úvodní stránka](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. Na stránce **Microsoft Azure Data Catalog** klikněte na **SQL Server** a poté na **Další**.
   
    ![Azure Data Catalog – zdroje dat](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Zadejte vlastnosti připojení systému SQL Server pro databázi **AdventureWorks2014** (viz následující příklad) a klikněte na **PŘIPOJIT**.
   
   ![Azure Data Catalog – nastavení připojení k SQL Serveru](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Zaregistrujte metadata datového assetu. V tomto příkladu zaregistrujete objekty **Production/Product** z oboru názvů AdventureWorks Production:
   
   1. Ve stromové struktuře **Hierarchie serveru** rozbalte **AdventureWorks2014** a klikněte na **Production**.
   2. Stisknutím klávesy Ctrl a kliknutím vyberte postupně **Product**, **ProductCategory**, **ProductDescription** a **ProductPhoto**.
   3. Klikněte na **šipku přesunutí výběru** (**>**). Tím se přesunou všechny vybrané objekty do seznamu **Objekty k registraci**.
      
      ![Kurz Azure Data Catalog – procházení a výběr objektů](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Vyberte možnost**Zahrnout náhled**, pokud chcete zahrnout snímek náhledu dat. Snímek zahrnuje až 20 záznamů z každé tabulky a je zkopírován do katalogu.
   5. Vyberte možnost **Zahrnout profil dat**, pokud chcete zahrnout snímek statistik objektu pro profil dat (např.: minimální, maximální a průměrné hodnoty sloupce, počet řádků).
   6. Do pole **Přidat značky** zadejte**adventure works, cycles**. Tím se přidají vyhledávací značky pro tyto datové assety. Značky jsou skvělý způsob, jak pomoci uživatelům najít registrovaný zdroj dat.
   7. Zadejte jméno **experta** na tyto data (volitelné).
      
      ![Kurz Azure Data Catalog – objekty k registraci](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Klikněte na tlačítko **ZAREGISTROVAT**. Služba Azure Data Catalog zaregistruje vaše vybrané objekty. V tomto cvičení jsou zaregistrovány vybrané objekty z podniku Adventure Works. Registrační nástroj vyextrahuje metadata z datového assetu a zkopíruje je do služby Azure Data Catalog. Data zůstanou uložena tam, kde v současnosti jsou, a zůstávají pod kontrolou správců a zásad aktuálního systému.
      
      ![Azure Data Catalog – registrované objekty](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Registrované objekty zdrojů dat zobrazíte kliknutím na tlačítko **Zobrazit portál**. Zkontrolujte na portálu Azure Data Catalog, jestli se vám zobrazují všechny čtyři tabulky a databáze v zobrazení mřížky.
      
      ![Objekty na portálu Azure Data Catalog ](media/data-catalog-get-started/data-catalog-view-portal.png)

V tomto cvičení jste zaregistrovali objekty z ukázkové databáze Adventure Works, aby je uživatelé ve vaší organizaci mohli snadno zjistit. V dalším cvičení se dozvíte, jak zjistit registrované datové assety.

## <a name="discover-data-assets"></a>Zjišťování datových assetů
Funkce zjišťování ve službě Azure Data Catalog používá dva primární mechanismy: vyhledávání a filtrování.

Vyhledávání je koncipováno tak, aby bylo jak intuitivní, tak výkonné. Ve výchozím nastavení se vyhledává shoda hledaných výrazů s libovolnou vlastností v katalogu včetně poznámek přidaných uživatelem.

Filtrování je koncipováno jako doplněk k vyhledávání. Můžete vybrat konkrétní charakteristiky, jako jsou experti, typ zdroje dat, typ objektu a značky, abyste zobrazili datové assety odpovídající těmto charakteristikám, a tak omezit výsledky vyhledávání.

Kombinace vyhledávání a filtrování vám umožní rychle procházet zdroje dat, které jste zaregistrovali pomocí služby Azure Data Catalog, a zjistit tak potřebné datové assety.

V tomto cvičení použijete portál služby Azure Data Catalog ke zjištění datových assetů, které jste zaregistrovali v předchozím cvičení. Podrobnosti o syntaxi vyhledávání naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

Následuje několik příkladů, jak zjistit datové assety v katalogu.  

### <a name="discover-data-assets-with-basic-search"></a>Zjištění datových assetů pomocí základního vyhledávání
Základní vyhledávání vám pomůže prohledat katalog pomocí jednoho nebo více hledaných výrazů. Ve výsledcích se zobrazí veškeré assety, které odpovídají jakékoli vlastnosti jednoho nebo více zadaných výrazů.

1. Na portálu služby Azure Data Catalog klikněte na možnost **Domů**. Pokud jste zavřeli webový prohlížeč, přejděte na [domovskou stránku Azure Data Catalog](https://www.azuredatacatalog.com).
2. Do vyhledávacího pole zadejte `cycles` a stiskněte klávesu **ENTER**.
   
    ![Azure Data Catalog – základní textové vyhledávání](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Potvrďte, že se vám ve výsledcích zobrazují všechny čtyři tabulky a databáze (AdventureWorks2014). Můžete přepínat mezi **zobrazením mřížky** a **zobrazením seznamu** kliknutím na příslušné tlačítko na panelu, jak ukazuje následující obrázek. Všimněte si, že hledané klíčové slovo je ve výsledcích vyhledávání zvýrazněno, protože možnost **Zvýraznit** je **ZAPNUTO**. Ve výsledcích vyhledávání můžete také upřesnit **počet výsledků na stránku**.
   
    ![Azure Data Catalog – výsledky základního textového vyhledávání](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    Panel **Vyhledávání** se nachází na levé straně a panel **Vlastnosti** na pravé. Na panelu **Vyhledávání** lze změnit kritéria vyhledávání a filtrovat výsledky. Na panelu **Vlastnosti** se zobrazují vlastnosti vybraného objektu v mřížce nebo seznamu.
4. Ve výsledcích vyhledávání klikněte na **Product**. Klikněte na karty **Náhled**, **Sloupce**, **Profil dat** a **Dokumentace** nebo klikněte na šipku a rozbalte spodní podokno.  
   
    ![Azure Data Catalog – spodní podokno](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    Na kartě **Náhled** uvidíte náhled dat v tabulce **Produkt**.  
5. Kliknutím na kartu **Sloupce** zobrazíte podrobnosti o sloupcích (např. **název** a **typ dat**) v datovém assetu.
6. Kliknutím na kartu **Profil dat** a zobrazíte profilaci dat (například počet řádků, velikost dat či minimální hodnota v sloupci) v datovém assetu.
7. Pomocí části **Filtry** na levé straně můžete filtrovat výsledky. Klikněte například na možnost **Tabulka** pro **Typ objektu** a zobrazíte pouze čtyři tabulky, nikoli databázi.
   
    ![Azure Data Catalog – filtrování výsledků vyhledávání](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Zjištění datových assetů pomocí zkoumání vlastností
Funkce zkoumání vlastností vám pomůže zjistit datové assety v případě, že se hledaný výraz shoduje se zadanou vlastností.

1. Ve části **Filtry** pod položkou **Typ objektu** vymažte filtr **Tabulka**.  
2. Do vyhledávacího pole zadejte `tags:cycles` a stiskněte klávesu **ENTER**. Všechny vlastnosti, které lze využít při vyhledávání v katalogu dat, naleznete v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).
3. Potvrďte, že se vám ve výsledcích zobrazují všechny čtyři tabulky a databáze (AdventureWorks2014).  
   
    ![Data Catalog – výsledky vyhledávání funkce zkoumání vlastnost](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Uložení vyhledávání
1. V podokně **Vyhledávání** v části **Aktuální vyhledávání** zadejte název vyhledávání a klikněte na **Uložit**.
   
    ![Azure Data Catalog – uložení vyhledávání](media/data-catalog-get-started/data-catalog-save-search.png)
2. Zkontrolujte, jestli se uložené vyhledávání zobrazuje v sekci **Uložená vyhledávání**.
   
    ![Azure Data Catalog – uložená vyhledávání](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Vyberte jednu z akcí, které lze s uloženým vyhledáváním provést (**Přejmenovat**, **Odstranit**, **Uložit jako výchozí**).
   
    ![Azure Data Catalog – možnosti uložených vyhledávání](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Logické operátory
Své vyhledávání můžete rozšířit nebo zúžit pomocí logických operátorů.

1. Do vyhledávacího pole zadejte `tags:cycles AND objectType:table` a stiskněte klávesu **ENTER**.
2. Zkontrolujte, jestli se ve výsledcích zobrazují pouze tabulky (ne databáze).  
   
    ![Azure Data Catalog – logické operátory ve vyhledávání](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Seskupování pomocí závorek
Závorky lze použít k seskupení částí dotazu za účelem logické izolace, zejména ve spojení s logickými operátory.

1. Do vyhledávacího pole zadejte `name:product AND (tags:cycles AND objectType:table)` a stiskněte klávesu **ENTER**.
2. Zkontrolujte, jestli se vám ve výsledcích vyhledávání zobrazuje pouze tabulka **Product**.
   
    ![Azure Data Catalog – vyhledávání seskupení](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operátory porovnání
S pomocí operátorů porovnání lze použít porovnávání jiné než rovnost pro vlastnosti, které mají typ dat číslo nebo datum.

1. Do vyhledávacího pole zadejte `lastRegisteredTime:>"06/09/2016"`.
2. Pod položkou **Typ objektu** vymažte filtr **Tabulka**.
3. Stiskněte **ENTER**.
4. Zkontrolujte, jestli se vám ve výsledcích vyhledávání zobrazují pouze tabulky **Product**, **ProductCategory**, **ProductDescription** a **ProductPhoto** a databáze AdventureWorks2014, kterou jste zaregistrovali.
   
    ![Azure Data Catalog – výsledky vyhledávání porovnání](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Podrobnosti o zjišťování datových assetů najdete v článku [Jak zjistit datové assety](data-catalog-how-to-discover.md). O syntaxi vyhledávání se dozvíte víc v článku [Referenční příručka syntaxe vyhledávání ve službě Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="annotate-data-assets"></a>Přidání poznámek k datovým assetům
V tomto cvičení použijete portál Azure Data Catalog k přidání poznámek (jako jsou popisy, značky či experti) k datovým assetům, které jste dříve zaregistrovali do katalogu. Poznámky doplňují a vylepšují strukturální metadata extrahovaná ze zdroje dat během registrace a zjednodušují zjišťování a pochopení datových assetů.

V tomto cvičení přidáte poznámky k jednomu datovému assetu (ProductPhoto). Doplníte k datovému assetu ProductPhoto popisný název a popis.  

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com) a vyhledejte pomocí výrazu `tags:cycles` datové assety, které jste zaregistrovali.  
2. Ve výsledcích vyhledávání klikněte na **ProductPhoto**.  
3. Zadejte **Obrázky produktu** pro **Popisný název** a jako **Popis** zadejte **Fotografie produktu pro marketingové materiály**.
   
    ![Azure Data Catalog – popis ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    **Popis** pomáhá ostatním zjistit datový asset a porozumět tomu, proč a jak vybraný datový asset používat. Můžete také přidat další značky a zobrazit sloupce. Teď můžete zkusit vyhledávání a filtrování, abyste zjistili datové assety pomocí popisných metadat, která jste přidali do katalogu.

Na této stránce můžete provést také následující akce:

* Přidání expertů k datovým assetům. V oblasti **Odborníci** klikněte na možnost **Přidat**.
* Přidání značek na úrovni datové sady. V oblasti **Značky** klikněte na možnost **Přidat**. Značka může být značka uživatele nebo značka glosáře. Data Catalog Standard Edition zahrnuje obchodní glosář, který pomáhá správcům katalogu definovat centrální obchodní taxonomii. Uživatelé katalogu mohou poté opatřit poznámkami datové assety pomocí termínů v glosáři. Další informace najdete v článku [Jak nastavit obchodní glosář řízeným přidáváním značek](data-catalog-how-to-business-glossary.md)
* Přidání značek na úrovni sloupce. V části **Značky** klikněte na možnost **Přidat** a vyberte sloupec, ke kterému chcete přidat poznámky.
* Přidání popisu na úrovni sloupce. Zadejte **Popis** pro sloupec. Můžete také zobrazit metadata popisu extrahovaná ze zdroje dat.
* Přidejte informace pro **Žádost o přístup**, které uživatelům dají návod, jak požádat o přístup k datovým assetům.
  
    ![Azure Data Catalog – přidání značek, popisů](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Vyberte kartu **Dokumentace** a dodejte dokumentaci pro datový asset. Funkce dokumentace ve službě Azure Data Catalog vám umožňuje použít katalog dat jako úložiště obsahu. Můžete tak mít po ruce podrobné popisy a návody k registrovaným datovým assetům.
  
    ![Azure Data Catalog – karta Dokumentace](media/data-catalog-get-started/data-catalog-documentation.png)

Můžete také přidat poznámku k více datovým assetům. Můžete například vybrat všechny datové assety, které jste zaregistrovali, a zadat k nim jednoho experta.

![Azure Data Catalog – přidání poznámky k více datovým assetům](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog podporuje crowdsourcingový přístup k přidávání poznámek. Každý uživatel katalogu dat může přidat značky (uživatele nebo glosáře), popisy a další metadata. Jakýkoli uživatel, který má nějaké informace a postřehy k datovému assetu, může zaznamenat své poznámky a zpřístupnit je pro jiné uživatele.

Podrobné informace o přidávání poznámek k datovým assetům najdete v článku [Jak přidat poznámky k datovým assetům](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Připojení k datovým assetům
V tomto cvičení otevřete datové assety v integrovaném klientském nástroji (Excel) i neintegrovaném nástroji (SQL Server Management Studio) s použitím informací o připojení.

> [!NOTE]
> Je důležité pamatovat, že Azure Data Catalog vám nedává přístup k samotnému zdroji dat – pouze vám usnadňuje tento zdroj dat zjistit a porozumět jeho funkci. Když se připojíte ke zdroji dat, klientská aplikace, kterou si vyberte, použije podle potřeby vaše přihlašovací údaje systému Windows nebo vás vyzve k zadání přihlašovacích údajů. Pokud vám nebyl dříve udělen přístup k tomuto zdroji dat, bude vám muset být udělen, abyste se mohli připojit.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Připojení k datovému assetu z Excelu
1. Ve výsledcích vyhledávání vyberte možnost **Product**. Na panelu klikněte na nabídku **Otevřít v aplikaci** a pak vyberte **Excel**.
   
    ![Azure Data Catalog – připojení k datovému assetu](media/data-catalog-get-started/data-catalog-connect1.png)
2. V automaticky otevřeném okně stahování klikněte na **Otevřít**. Přesný postup a prostředí se může mírně lišit v závislosti na prohlížeči.
   
    ![Azure Data Catalog – stažený soubor připojení pro Excel](media/data-catalog-get-started/data-catalog-download-open.png)
3. V okně **Oznámení o zabezpečení aplikace Microsoft Excel** klikněte na tlačítko **Povolit**.
   
    ![Azure Data Catalog – automaticky otevřené okno zabezpečení Excelu](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. V dialogovém okně **Import dat** ponechte výchozí nastavení a klikněte na tlačítko **OK**.
   
    ![Azure Data Catalog – import dat aplikace Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Zobrazení zdroje dat v Excelu.
   
    ![Azure Data Catalog – tabulka produktů v Excelu](media/data-catalog-get-started/data-catalog-connect2.png)

V tomto cvičení jste se připojili k datovým assetům zjištěným pomocí služby Azure Data Catalog. Portál služby Azure Data Catalog vám umožňuje připojit se přímo pomocí klientských aplikací integrovaných do jeho nabídky **Otevřít v aplikaci**. Také se ale můžete připojit pomocí jakékoli aplikace, kterou si vyberete, pomocí informací o umístění připojení zahrnutých v metadatech assetu. Můžete například použít SQL Server Management Studio a připojit se k databázi AdventureWorks2014, abyste přistupovali k datům v datových assetech, které jste zaregistrovali v tomto kurzu.

1. Otevřete **SQL Server Management Studio**.
2. V dialogovém okně **Připojit k serveru** zadejte název serveru z podokna **Vlastnosti** na portálu Azure Data Catalog.
3. Použijte příslušné ověření a přihlašovací údaje pro přístup k datovému assetu. Pokud nemáte přístup, použijte informace uvedené v poli **Žádost o přístup** a vyžádejte si jej.
   
    ![Azure Data Catalog – žádost o přístup](media/data-catalog-get-started/data-catalog-request-access.png)

Kliknutím na možnost **Zobrazit připojovací řetězce** zobrazte připojovací řetězce pro ADF.NET, ODBC a OLEDB, které pak můžete zkopírovat do schránky a použít ve své aplikaci.

## <a name="manage-data-assets"></a>Správa datových assetů
V tomto kroku se dozvíte, jak nastavit zabezpečení datových assetů. Služba Data Catalog neposkytuje uživatelům přístup k samotným datům. O udělení přístupu k datům rozhoduje vlastník zdroje dat.

Služba Data Catalog vám umožní zjistit zdroje dat a zobrazit metadata související se zdroji zaregistrovanými v katalogu. Mohou ovšem nastat situace, kdy mají být zdroje dat viditelné pouze pro určité uživatele nebo členy určitých skupin. Pro tyto případy můžete pomocí služby Data Catalog převzít vlastnictví registrovaných datových assetů v katalogu a následně řídit viditelnost assetů, které teď vlastníte.

> [!NOTE]
> Možnosti správy popsané v tomto cvičení jsou k dispozici pouze v Azure Data Catalog Standard Edition, nikoli v bezplatné Free Edition.
> Ve službě Azure Data Catalog může převzít vlastnictví datových prostředků, přidat spoluvlastníky k datovým prostředkům a nastavit viditelnost datových prostředků.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Převzetí vlastnictví datových assetů a omezení viditelnosti
1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com). Do textového pole **Vyhledávání** zadejte `tags:cycles` a stiskněte klávesu **ENTER**.
2. Klikněte na položku v seznamu výsledků a na panelu nástrojů klikněte na možnost **Převzít vlastnictví**.
3. Na panelu **Vlastnosti** v části **Správa** klikněte na tlačítko **Převzít vlastnictví**.
   
    ![Azure Data Catalog – převzetí vlastnictví](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Pokud chcete omezit viditelnost, v části **Viditelnost** vyberte možnost **Vlastníci a tito uživatelé** a klikněte na tlačítko **Přidat**. Do textového pole zadejte e-mailové adresy uživatelů a stiskněte **ENTER**.
   
    ![Azure Data Catalog – omezení přístupu](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Odstranění datových assetů
V tomto cvičení použijete portál služby Azure Data Catalog k odebrání náhledu dat z registrovaných datových assetů a k odstranění datových assetů z katalogu.

Ve službě Azure Data Catalog je možné odstranit jednotlivý asset nebo více assetů.

1. Přejděte na [domovskou stránku služby Azure Data Catalog](https://www.azuredatacatalog.com).
2. Do textového pole **Vyhledávání** zadejte `tags:cycles` a stiskněte klávesu **ENTER**.
3. V seznamu výsledků vyberte položku a na panelu nástrojů klikněte na **Odstranit**, jak je znázorněno na následujícím obrázku:
   
    ![Azure Data Catalog – odstranění položky mřížky](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Pokud používáte zobrazení seznamu, je zaškrtávací políčko nalevo od položky, jak je znázorněno na následujícím obrázku:
   
    ![Azure Data Catalog – odstranění položky seznamu](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Můžete také vybrat a odstranit více datových assetů, jak ukazuje následující obrázek:
   
    ![Azure Data Catalog – odstranění více datových assetů](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> Výchozí chování katalogu je umožnit každému uživateli zaregistrovat libovolný zdroj dat a umožnit každému uživateli odstranit libovolný zaregistrovaný datový asset. Možnosti správy zahrnuté do Azure Data Catalog Standard Edition poskytují další možnosti pro převzetí vlastnictví assetů, omezení, kdo může assety zjistit, a omezení, kdo může assety odstranit.
> 
> 

## <a name="summary"></a>Souhrn
V tomto kurzu jste prozkoumali základní možnosti služby Azure Data Catalog včetně registrace, přidávání poznámek, zjišťování a správy datových assetů organizace. Nyní, když jste dokončili kurz, je čas začít. Začít můžete ještě dnes registrací zdrojů dat, na která spoléháte vy a váš tým, a pozváním kolegů k používání katalogu.

## <a name="references"></a>Odkazy
* [Registrace datových prostředků](data-catalog-how-to-register.md)
* [Zjišťování datových prostředků](data-catalog-how-to-discover.md)
* [Přidání poznámek k datovým prostředkům](data-catalog-how-to-annotate.md)
* [Dokumentování datových prostředků](data-catalog-how-to-documentation.md)
* [Jak se připojit k datovým prostředkům](data-catalog-how-to-connect.md)
* [Jak spravovat datové prostředky](data-catalog-how-to-manage.md)

