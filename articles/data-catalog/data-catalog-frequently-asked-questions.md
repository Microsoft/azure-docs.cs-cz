---
title: Nejčastější dotazy katalogu dat Azure
description: Nejčastější dotazy týkající se katalogu dat Azure, včetně možností zjišťování, anotace a správy zdrojů dat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409015"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Nejčastější dotazy katalogu dat Azure
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co je Azure Data Catalog?
Katalog dat je plně spravovaná služba hostovaná v Microsoft Azure, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Pomocí katalogu dat může každý uživatel, od analytiků po datové vědce a vývojáře, registrovat, zjišťovat, chápat a využívat zdroje dat.

## <a name="what-customer-challenges-does-it-solve"></a>Jaké výzvy zákazníků řeší?
Katalog dat řeší problémy zjišťování zdrojů dat a "tmavých dat", aby uživatelé mohli zjišťovat a chápat zdroje podnikových dat.

## <a name="what-are-its-target-audiences"></a>Jaké jsou jeho cílové skupiny?
Katalog dat je určen pro technické a netechnické uživatele, včetně:

* Vývojáři dat a odborníci na BI a analytiku: Lidé, kteří jsou zodpovědní za vytváření dat a analytického obsahu, který mohou ostatní využívat.
* Správci dat: Lidé, kteří mají znalosti o datech, o tom, co znamenají a jak mají být použita.
* Spotřebitelé dat: Lidé, kteří potřebují být schopni snadno objevit, pochopit a připojit se k datům, které potřebují k práci, pomocí nástroje podle svého výběru.
* Centrální IT: Lidé, kteří potřebují, aby byly stovky zdrojů dat zjistitelné podnikovými uživateli a kteří potřebují udržovat dohled nad tím, jak jsou data využívána a kým.

## <a name="what-is-its-availability-by-region"></a>Jaká je jeho dostupnost podle regionů?
Služby katalogu dat jsou v současné době k dispozici v následujících datových centrech:

* USA – západ
* USA – východ
* Západní Evropa
* Severní Evropa
* Austrálie – východ
* Jihovýchodní Asie

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jaké jsou jeho limity pro počet datových prostředků?
Bezplatná edice katalogu dat je omezena na 5 000 registrovaných datových prostředků.

Standardní edice katalogu dat podporuje až 100 000 registrovaných datových prostředků.

Všechny objekty registrované v katalogu dat, jako jsou tabulky, zobrazení, soubory a sestavy, se počítají jako datový datový zdroj.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jaké jsou jeho podporované zdroje dat a typy prostředků?
Seznam aktuálně podporovaných zdrojů dat naleznete v [tématu Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Jak mohu požádat o podporu pro jiný zdroj dat?
Pokud chcete odeslat žádosti o funkce a další zpětnou vazbu, přejděte [do katalogu dat na fórech pro zpětnou vazbu Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Proč se při pokusu o vytvoření nového katalogu *již zobrazuje chyba Katalogu?*

Když si koupíte Office 365 E5 s licencí Power BI Pro, Microsoft automaticky vytvoří výchozí katalog v oblasti předplatného. Tento katalog používá volnou skladovou položku. Uživatelská licence Office 365 / Power BI se spravuje na stránce Správa Office 365. 

Tento typ katalogu dat však nemá **možnost správce** a není viditelný na webu **Azure Portal**. Tento typ katalogu dat nelze odstranit. Podobně není povoleno přejmenovat katalog dat a nelze jej přesunout do jiné oblasti. 

Účty uživatelů, kterým je přiřazena automatická licence Power BI Pro, mají přístup ke katalogu dat kvůli licenční smlouvě, když si zaregistrovali Office 365 E5 pomocí licence Power BI Pro. Tento typ uživatele má úplný přístup k prostředkům katalogu dat bez oprávnění správce. Tento druh uživatele *není* součástí role **uživatele katalogu** v katalogu dat Azure.


## <a name="how-do-i-get-started-with-data-catalog"></a>Jak začít s katalogem dat?
Nejlepší způsob, jak začít, je jít do [části Začínáme s katalogem dat](data-catalog-get-started.md). Tento článek je komplexní přehled funkcí ve službě.

## <a name="how-do-i-register-my-data"></a>Jak mohu zaregistrovat své údaje?
Registrace dat v katalogu dat:
1. Na portálu Katalog dat Azure v oblasti **Publikování** spusťte nástroj registrace katalogu dat Azure. 
2. V nástroji pro registraci zdroje dat katalogu dat se přihlaste pomocí stejných přihlašovacích údajů, které používáte pro přístup k portálu katalogu dat.
3. Vyberte zdroj dat a konkrétní datové zdroje, které chcete zaregistrovat.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Jaké vlastnosti extrahuje pro datové prostředky, které jsou registrovány?
Specifické vlastnosti se liší od zdroje dat ke zdroji dat, ale obecně služba publikování katalogu dat extrahuje následující informace:

* Název majetku
* Typ majetku
* Popis majetku
* Názvy atributů/sloupců
* Datové typy atributů/sloupců
* Popis atributu/sloupce

> [!IMPORTANT]
> Registrace datových prostředků v katalogu dat nepřesune ani nezkopíruje vaše data do cloudu. Registrace datových zdrojů ze zdroje dat zkopíruje metadata datových zdrojů do Azure, ale data zůstanou v existujícím umístění zdroje dat. Výjimkou z tohoto pravidla je, pokud se rozhodnete nahrát náhled záznamů nebo datový profil při registraci datových zdrojů. Když zahrnete náhled, z každého datového zdroje se zkopíruje až 20 záznamů a uloží se jako snímek v katalogu dat. Když zahrnete datový profil, agregované informace se počítají a zahrnují do metadat, která jsou uložena v katalogu. Souhrnné informace mohou zahrnovat velikost tabulek, procento hodnot null na sloupec nebo minimální, maximální a průměrné hodnoty pro sloupce. 
>
>

> [!NOTE]
> Pro zdroje dat, jako je například SQL Server Analysis Services, které mají vlastnost **Class Description,** nástroj pro registraci zdroje dat katalogu dat extrahuje tuto hodnotu vlastnosti. Pro *místní* sql server relační databáze, které postrádají vlastnost **Class Description,** nástroj pro registraci zdroje dat katalogu dat extrahuje hodnotu z **ms_description** rozšířené vlastnosti pro objekty a sloupce. Tato vlastnost není podporována pro SQL Azure. Další informace naleznete [v tématu Using Extended Properties on Database Objects](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak dlouho by mělo trvat, než se nově registrovaný majetek zobrazí v katalogu?
Po registraci datových zdrojů v katalogu dat může být období 5 až 10 sekund, než se zobrazí na portálu katalogu dat.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Jak označím a obohatím metadata pro své registrované datové prostředky?
Nejjednodušší způsob, jak poskytnout metadata pro registrované datové zdroje, je vybrat datový zdroj na portálu Katalog dat a pak zadat hodnoty do podokna vlastností nebo podokna schématu pro vybraný objekt.

Během procesu registrace můžete také zadat některá metadata, například odborníky a značky. Hodnoty, které zadáte ve službě publikování katalogu dat, se vztahují na všechny datové zdroje registrované v té době. Chcete-li zobrazit nedávno registrované objekty na portálu pro další poznámky, vyberte tlačítko **Zobrazit portál** na poslední obrazovce nástroje registrace zdroje dat katalogu dat.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Jak odstraním své registrované datové objekty?
Objekt můžete odstranit z katalogu dat tak, že vyberete objekt na portálu a potom kliknete na tlačítko **Odstranit.** Odebráním objektu odeberete jeho metadata z katalogu dat, ale neovlivní základní zdroj dat.

## <a name="what-is-an-expert"></a>Co je to expert?
Odborník je osoba, která má informovanou perspektivu datového objektu. Objekt může mít více odborníků. Odborník nemusí být "vlastníkem" pro objekt, ale je prostě někdo, kdo ví, jak mohou a měly by být použity údaje.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Jak mohu sdílet informace s týmem katalogu dat, pokud dojde k problémům?
Pokud chcete nahlásit problémy, sdílet informace a klást otázky, přejděte na [fórum Katalog dat Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Pracuje katalog s jiným zdrojem dat, který mě zajímá?
Aktivně pracujeme na přidávání dalších zdrojů dat do katalogu dat. Pokud chcete, aby byl konkrétní zdroj dat podporován, navrhněte ho (nebo hlasujte svou podporu, pokud už byl navržen) na webu [Azure Feedback Forums](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jaká oprávnění potřebuji k registraci datových zdrojů v katalogu dat?
Chcete-li spustit nástroj pro registraci katalogu dat, potřebujete oprávnění ke zdroji dat, která vám umožní číst metadata ze zdroje. Chcete-li také zahrnout náhled, musíte mít oprávnění, která vám umožní číst data z registrovaných objektů.

Katalog dat také umožňuje správcům katalogu omezit, kteří uživatelé a skupiny mohou do katalogu přidávat metadata. Další informace naleznete v [tématu Jak zabezpečit přístup ke katalogu dat a datovým prostředkům](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Bude katalog dat k dispozici také pro místní nasazení?
Katalog dat je cloudová služba, která může pracovat s cloudovými i místními zdroji dat a poskytovat hybridní řešení zjišťování zdrojů dat. V současné době neexistují žádné plány pro verzi služby Katalog dat, která běží místně.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Mohu extrahovat více nebo bohatší metadata ze zdrojů dat, které registruji?
Aktivně pracujeme na rozšíření možností katalogu dat. Pokud chcete mít další metadata extrahovaná ze zdroje dat během registrace, navrhněte je (nebo hlasujte pro něj, pokud již byl navržen) v [katalogu dat na fórech azure feedback](https://feedback.azure.com/forums/906052-data-catalog). 

Pokud chcete zahrnout metadata sloupce nebo schématu, náhledy nebo profily dat pro zdroje dat, kde tato metadata nejsou extrahována nástrojem pro registraci zdroje dat, můžete k přidání těchto metadat použít rozhraní API pro katalog dat. Další informace najdete v [tématu Azure Data Catalog REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Jak omezím viditelnost registrovaných datových prostředků, aby je mohli objevit jen někteří lidé?
Vyberte datové datové prostředky v katalogu dat a klikněte na tlačítko **Převzít vlastnictví.** Vlastníci datových prostředků v katalogu dat můžete změnit nastavení viditelnosti buď umožnit všem uživatelům zjistit vlastněný majetek nebo omezit viditelnost na konkrétní uživatele. Další informace najdete [v tématu Správa datových prostředků v Katalogu dat Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Jak lze aktualizovat registraci datového prostředku tak, aby se změny ve zdroji dat projevily v katalogu?
Chcete-li aktualizovat metadata pro datové prostředky, které jsou již registrovány v katalogu, jednoduše znovu zaregistrovat zdroj dat, který obsahuje prostředky. Všechny změny ve zdroji dat, jako jsou sloupce přidané nebo odebrané z tabulek nebo zobrazení, jsou aktualizovány v katalogu, ale všechny poznámky poskytované uživateli jsou zachovány.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Moje otázka zde není zodpovězena. Kde mohu jít pro odpovědi?
Přejděte na [fórum katalogu dat Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Otázky, které tam byly položeny, najdou cestu sem.
