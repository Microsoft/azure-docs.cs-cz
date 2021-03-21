---
title: Azure Data Catalog nejčastějších dotazech
description: Nejčastější dotazy týkající se Azure Data Catalog, včetně možností pro zjišťování zdrojů dat, anotaci a správu.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674678"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Data Catalog nejčastějších dotazech

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co je Azure Data Catalog?
Data Catalog je plně spravovaná služba, která je hostovaná v Microsoft Azure, která slouží jako systém registrace a zjišťování pro podnikové zdroje dat. S Data Catalog může každý uživatel, od analytiků až po odborníky na data a vývojáře, registrovat, zjišťovat, pochopit a využívat zdroje dat.

## <a name="what-customer-challenges-does-it-solve"></a>Jaké problémy se zákazníky vyřeší?
Data Catalog řeší výzvy zjišťování zdrojů dat a "tmavě data", aby uživatelé mohli zjišťovat a rozumět podnikovým zdrojům dat.

## <a name="what-are-its-target-audiences"></a>Jaké jsou cílové skupiny?
Data Catalog je navržená pro technické a netechnické uživatele, včetně těchto:

* Vývojáři dat a odborníci na BI a analytika: lidé, kteří zodpovídají za vytváření dat a analytického obsahu pro jiné využívání.
* Data Stewards: lidé, kteří mají znalosti o datech, co to znamená a jak se mají použít.
* Spotřebitelé dat: lidé, kteří potřebují mít možnost snadno zjišťovat, rozumět a připojovat se k datům, která potřebují ke své práci, pomocí nástroje podle svého výběru.
* Střed IT: lidé, kteří potřebují udělat stovky zdrojů dat zjistitelných obchodními uživateli a kteří potřebují zajistit dohled nad tím, jak se data používají a kdy.

## <a name="what-is-its-availability-by-region"></a>Jaká je její dostupnost podle oblastí?
Služba Data Catalog Services je aktuálně dostupná v následujících datových centrech:

* USA – západ
* East US
* West Europe
* Severní Evropa
* Austrálie – východ
* Southeast Asia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jaké jsou omezení počtu datových assetů?
Bezplatná edice Data Catalog je omezená na 5 000 registrovaných datových assetů.

Edice Standard Data Catalog podporuje až 100 000 registrovaných datových assetů.

Libovolný objekt zaregistrovaný v Data Catalog, jako jsou tabulky, zobrazení, soubory a sestavy, se počítá jako datový Asset.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jaké jsou podporované zdroje dat a typy prostředků?
Seznam aktuálně podporovaných zdrojů dat najdete v tématu [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Návody požádat o podporu pro jiný zdroj dat?
Pokud chcete odesílat žádosti o funkce a další zpětnou vazbu, na webu [fóra o zpětné vazbě Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources)klikněte na Data Catalog.

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Proč se mi při pokusu o vytvoření nového katalogu objeví *katalog* chyb?

Když si koupíte Office 365 E5 s licencí Power BI Pro, Microsoft automaticky vytvoří výchozí katalog v oblasti předplatného. Tento katalog používá bezplatnou SKLADOVOU položku. Licence na uživatele Office 365/Power BI je spravovaná na stránce Správa. 

Tento typ katalogu dat však nemá **možnost Správce** a není zobrazen v **Azure Portal**. Tento typ katalogu dat nelze odstranit. Podobně nemáte oprávnění k přejmenování katalogu Data Catalog a nemůžete ho přesunout do jiné oblasti. 

Uživatelům, kteří mají přiřazenou licenci Power BI Pro, mají automaticky přístup k datovému katalogu z důvodu licenční smlouvy, pokud se zaregistrovali k Office 365 E5 s licencí Power BI Pro. Tento typ uživatele má úplný přístup k prostředkům katalogu Data Catalog bez oprávnění správce. Tento druh uživatele *není součástí role* **uživatele katalogu** v Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Návody začít s Data Catalog?
Nejlepším způsobem, jak začít, je [Začínáme s Data Catalog](data-catalog-get-started.md). Tento článek je ucelený přehled funkcí služby.

## <a name="how-do-i-register-my-data"></a>Návody zaregistrovat moje data?
Chcete-li zaregistrovat data v Data Catalog:
1. Na portálu Azure Data Catalog v oblasti **publikovat** spusťte nástroj Azure Data Catalog Registration Tool. 
2. V nástroji pro registraci zdroje dat Data Catalog se přihlaste se pomocí stejných přihlašovacích údajů, které používáte pro přístup k portálu Data Catalog.
3. Vyberte zdroj dat a konkrétní prostředky, které chcete zaregistrovat.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Jaké vlastnosti extrahují pro datové prostředky, které jsou zaregistrované?
Konkrétní vlastnosti se liší od zdroje dat až po zdroj dat, ale obecně platí, že služba publikování Data Catalog extrahuje následující informace:

* Název assetu
* Typ prostředku
* Popis prostředku
* Názvy atributů a sloupců
* Datové typy atributu/sloupce
* Popis atributu/sloupce

> [!IMPORTANT]
> Registrace datových assetů pomocí Data Catalog nepřesouvá ani nekopírují data do cloudu. Registrace prostředků ze zdroje dat zkopíruje metadata assets do Azure, ale data zůstanou ve stávajícím umístění zdroje dat. Výjimkou z tohoto pravidla je, pokud se rozhodnete odeslat záznamy verze Preview nebo datový profil při registraci prostředků. Když zahrnete náhled, z každého assetu se zkopíruje až 20 záznamů a uloží se jako snímek v Data Catalog. Když zahrnete profil dat, vypočítávají se agregované informace a budou zahrnuty do metadat uložených v katalogu. Souhrnné informace mohou zahrnovat velikost tabulek, procento hodnot null na sloupec nebo minimální, maximální a průměrné hodnoty pro sloupce. 
>
>

> [!NOTE]
> Pro zdroje dat, jako je například Služba Analysis Services serveru SQL, které mají vlastnost **Popis** první třídy, nástroj pro registraci zdroje dat Data Catalog extrahuje hodnotu této vlastnosti. U *místních* SQL Server relačních databází, které neobsahují vlastnost **popisu** první třídy, nástroj pro registraci zdroje dat Data Catalog extrahuje hodnotu z **MS_Description** rozšířené vlastnosti pro objekty a sloupce. Tato vlastnost není podporována pro SQL Azure. Další informace najdete v tématu [použití rozšířených vlastností u databázových objektů](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak dlouho by se mělo provést, aby se nově zaregistrované prostředky zobrazovaly v katalogu?
Po registraci prostředků pomocí Data Catalog může trvat 5 až 10 sekund, než se budou zobrazovat na portálu Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Návody opatřit poznámkami a rozšířit metadata pro moje registrované datové prostředky?
Nejjednodušší způsob, jak poskytnout metadata registrovaným assetům, je vybrat Asset na portálu Data Catalog a pak zadat hodnoty v podokně vlastnosti nebo schématu pro vybraný objekt.

Během procesu registrace můžete také poskytnout některá metadata, například odborníky a značky. Hodnoty, které zadáte ve službě Data Catalog Publishing Service, se vztahují na všechny prostředky, které jsou v daném čase registrovány. Chcete-li zobrazit nedávno registrované objekty na portálu pro další anotaci, vyberte tlačítko **Zobrazit portál** na poslední obrazovce nástroje pro registraci zdroje dat Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Návody odstranit moje registrované datové objekty?
Objekt můžete z Data Catalog odstranit tak, že ho vyberete na portálu a pak kliknete na tlačítko **Odstranit** . Odebrání objektu odebere jeho metadata z Data Catalog, ale nemá vliv na podkladový zdroj dat.

## <a name="what-is-an-expert"></a>Co je odborník?
Odborník je osoba, která má k datovému objektu starosti perspektivu. Objekt může mít více expertů. Odborník nemusí být vlastníkem objektu, ale je prostě někoho, kdo ví, jak můžou být tato data a měla by se používat.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Návody sdílet informace s týmem Data Catalog, pokud se setkáte s problémy?
Pokud chcete nahlásit problémy, sdílet informace a klást otázky, navštivte [fórum Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Pracuje katalog s jiným zdrojem dat, který mi zajímám?
Aktivně pracujeme na přidání dalších zdrojů dat do Data Catalog. Pokud chcete zobrazit konkrétní podporovaný zdroj dat, Navrhněte ho (nebo hlaste podporu, pokud už je navržený), na webu [fóra pro názory Azure na Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jaká oprávnění potřebuji k registraci prostředků pomocí Data Catalog?
Chcete-li spustit nástroj pro registraci Data Catalog, potřebujete oprávnění ke zdroji dat, který umožňuje číst metadata ze zdroje. Chcete-li také zahrnout verzi Preview, musíte mít oprávnění, která vám umožní číst data z registrovaných objektů.

Data Catalog taky správcům katalogu umožňuje omezit, kteří uživatelé a skupiny můžou do katalogu přidávat metadata. Další informace najdete v tématu [zabezpečení přístupu ke katalogu dat a datovým assetům](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Bude Data Catalog k dispozici také pro místní nasazení?
Data Catalog je cloudová služba, která může spolupracovat s cloudovým i místním zdrojem dat pro zajištění řešení pro zjišťování zdrojového zdroje dat. V tuto chvíli nejsou k dispozici žádné plány pro verzi Data Catalog služby, která běží místně.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Můžu extrahovat větší nebo rozsáhlejší metadata ze zdrojů dat, které zaregistruju?
Aktivně pracujeme na rozšiřování možností Data Catalog. Pokud chcete, aby se další metadata extrahovaná ze zdroje dat během registrace mohla navrhnout (nebo hlasovat pro IT, pokud už byla navržená) v [Data Catalog na fórech Azure pro názory](https://feedback.azure.com/forums/906052-data-catalog). 

Pokud chcete zahrnout metadata sloupce nebo schématu, náhledy nebo datové profily, pro zdroje dat, kde tato metadata nejsou extrahovány nástrojem pro registraci zdroje dat, můžete tato metadata přidat pomocí rozhraní Data Catalog API. Další informace najdete v tématu [Azure Data Catalog REST API](/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Návody omezit viditelnost registrovaných datových assetů, aby je mohli zjistit jenom někteří lidé?
V Data Catalog vyberte datové assety a pak klikněte na tlačítko **převzít vlastnictví** . Vlastníci datových assetů v Data Catalog mohou změnit nastavení viditelnosti tak, aby všichni uživatelé mohli vyhledat vlastněné prostředky nebo omezit viditelnost konkrétních uživatelů. Další informace najdete v tématu [Správa datových assetů v Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Návody aktualizujte registraci datového assetu tak, aby se změny ve zdroji dat projevily v katalogu?
Chcete-li aktualizovat metadata datových assetů, které jsou již v katalogu registrovány, stačí znovu zaregistrovat zdroj dat, který obsahuje prostředky. Jakékoli změny ve zdroji dat, jako jsou sloupce přidané nebo odebrané z tabulek nebo zobrazení, jsou v katalogu aktualizované, ale všechny poznámky poskytované uživateli jsou zachovány.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Na moji otázku se tady neodpoví. Kde můžu přejít na odpovědi?
Přejít na [fórum Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Otázky, na které se žádá, najdete tady.