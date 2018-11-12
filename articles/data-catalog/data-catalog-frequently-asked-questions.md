---
title: Nejčastější dotazy k Azure Data Catalog
description: Nejčastější dotazy týkající se Azure Data Catalog, včetně možnosti zjišťování zdrojů dat, poznámky a správu.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: f08825c17c1ba61fc09edc1d8f2c226af8d4266f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231460"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Nejčastější dotazy k Azure Data Catalog
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co je Azure Data Catalog?
Data Catalog je plně spravovaná služba, hostované v Microsoft Azure, který slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Pomocí katalogu Data Catalog každý uživatel, od analytiků datových vědců a vývojářů, může zaregistrovat, zjišťovat, pochopit a využívat zdroje dat.

## <a name="what-customer-challenges-does-it-solve"></a>Jaké zákazníka vyzve udělá vyřešit?
Data Catalog řeší výzvy "nejasná data" a zdroj dat zjišťování tak, aby uživatelé mohli objevit a porozumět zdrojům dat organizace.

## <a name="what-are-its-target-audiences"></a>Jaké jsou jeho cílové skupiny?
Katalog data Catalog je určen pro technická a Netechnická uživatele, včetně:

* Data vývojáři a odborníky na BI a analytických: uživatelů, kteří zodpovídají za vytváření obsahu dat a analýz pro ostatní využívají.
* Data stewards: uživatelé, kteří mají znalosti o datech, co to znamená, a jak je určena pro použití.
* Spotřebitelé dat: lidí, kteří potřebují mít možnost snadno zjišťovat, pochopit a připojte se k datům, která potřebují pro výkon své práce s použitím nástroje podle vlastní volby.
* Centrální IT: lidé zjistitelnost stovkám zdrojů dat podnikoví uživatelé, kteří potřebují, a kteří potřebují spravovat dohledu nad jak se data používají a kým.

## <a name="what-is-its-availability-by-region"></a>Co je její dostupnost podle oblasti?
Data katalogu služeb jsou aktuálně dostupné v následujících datových centrech:

* USA – západ
* USA – východ
* Západní Evropa
* Severní Evropa
* Austrálie – východ
* Jihovýchodní Asie

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jaká jsou její omezení na počet datových prostředků?
Bezplatná edice Data Catalog je omezené na 5000 registrovaných datových assetů.

Standardní edice Data Catalog podporuje až 100000 registrovaných datových assetů.

Libovolný objekt zaregistrovaný ve službě Data Catalog, jako jsou tabulky, zobrazení, soubory a sestavy, počty jako k datovému assetu.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jaké jsou její podporované datové typy zdroje a prostředků?
Seznam aktuálně podporovaných zdrojů dat naleznete v tématu [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Jak můžu požádat o podporu pro jiný zdroj dat?
K odeslání žádosti o funkce a jinou zpětnou vazbu, přejděte [zpětnou vazbu fóra Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Jak mám začít s katalogem Data Catalog?
Nejlepší způsob, jak začít, je tak, že přejdete do [Začínáme s katalogem Data Catalog](data-catalog-get-started.md). Tento článek je přehled možností ve službě začátku do konce.

## <a name="how-do-i-register-my-data"></a>Jak se zaregistrovat Moje data?
Registrace vašich dat ve službě Data Catalog:
1. Na portálu Azure Data Catalog ve **publikovat** oblasti, spusťte nástroj pro registraci Azure Data Catalog. 
2. V nástroj registrace zdroje dat katalogu Data Catalog Přihlaste se pomocí stejných přihlašovacích údajů, které umožňují přístup k portálu pro Data Catalog.
3. Vyberte zdroj dat a konkrétní prostředky, které chcete zaregistrovat.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Jeho vlastností extrahovat datových assetů, které jsou registrovány?
Specifické vlastnosti se liší od zdroje dat pro zdroj dat, ale obecně publikování služby Data Catalog extrahuje následující informace:

* Název assetu
* Typ assetu
* Popis prostředku
* Názvy atributů a sloupců
* Atribut nebo sloupce datové typy
* Popis atributu/sloupce

> [!IMPORTANT]
> Registrace datových assetů pomocí katalogu Data Catalog přesunutí nebo zkopírování vašich dat do cloudu. Registrace prostředky ze zdroje dat zkopíruje metadat prostředky do Azure, ale data zůstanou v existujícím zdroji dat umístění. Výjimkou z tohoto pravidla je, pokud se rozhodnete nahrát záznamy ve verzi preview nebo profil dat při registraci prostředky. Pokud zahrnete ve verzi preview, až 20 záznamů z každé asset zkopírují a uloží jako snímku ve službě Data Catalog. Po zahrnutí dat profilu agregované informace se počítá a zahrnutých v metadatech, která je uložena v katalogu. Agregované informace může patřit velikost tabulky, procento z hodnot null ve sloupci nebo minimální, maximální a průměrné hodnoty pro sloupce. 
>
>

> [!NOTE]
> U zdrojů dat, jako je například SQL Server Analysis Services, které mají prvotřídní **popis** extrahuje hodnotu vlastnosti, nástroj registrace zdroje dat katalogu Data Catalog. Pro relační databáze SQL serveru, které nemají prvotřídní **popis** vlastnost, nástroj registrace zdroje dat katalogu Data Catalog extrahuje hodnotu ze **ms_description** rozšířená vlastnost pro objekty a sloupců. Další informace najdete v tématu [pomocí rozšířené vlastnosti na databázových objektech](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak dlouho by měl trvat pro nově registrovaných aktiv se zobrazí v katalogu?
Až dokončíte registraci prostředky s katalogem Data Catalog, může být určité 5 až 10 sekund, než se zobrazují na portálu pro Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Jak opatřit poznámkami a rozšiřovat metadata pro moje registrovaných datových assetů?
Nejjednodušší způsob, jak poskytování metadat pro registrovaných aktiv je vyberte asset portálu katalogu dat a pak zadejte hodnoty v okně Vlastnosti nebo schéma pro vybraný objekt.

Některá metadata, jako je například expertů a značky, můžete zadat také během procesu registrace. Hodnoty, které jste zadali v publikování služby Data Catalog se vztahuje na všechny prostředky, které jsou registrovány v daném čase. Chcete-li zobrazit poslední registrované objekty na portálu pro další poznámky, vyberte **zobrazit portál** tlačítko na finální obrazovce nástroj registrace zdroje dat katalogu Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Jak odstraním registrované datové objekty
Při odstraňování objektu z katalogu dat výběrem objektu v portálu a potom kliknutím na **odstranit** tlačítko. Odebírá objekt odebere jeho metadata z katalogu Data Catalog, ale nemá vliv na podkladový zdroj dat.

## <a name="what-is-an-expert"></a>Co je odborník?
Jste odborník je osoba, která má informovaně perspektivu o datový objekt. Objekt může mít několik odborníků. Jste odborník nemusí být "vlastník" pro objekt, ale je jednoduše kdokoli, kdo ví, jak a by měla sloužit data.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Jak se dá sdílet informace s týmem katalogu Data Catalog Pokud I nastat potíže?
Chcete-li zprávy o problémech, sdílet informace a zeptejte se a přejděte na [fóru služby Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Funguje s jiným zdrojem dat, které zajímá mě katalogu?
Aktivně pracujeme na přidání více zdrojů dat do služby Data Catalog. Pokud se chcete podívat na konkrétnímu zdroji dat nepodporuje, navrhněte ji (nebo hlasové vaši podporu, pokud již byla navržena) tak, že přejdete [zpětnou vazbu fóra Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Jak Azure Data Catalog souvisí se katalog dat v Power BI pro Office 365?
Azure Data Catalog si můžete představit jako vývojem představ o katalogu dat v Power BI. Od spring 2017 Azure Data Catalog slouží k povolení sdílení a zjišťování dotazů v Excelu 2016 a Power Query pro Excel. Možnosti katalogu data Catalog v aplikaci Excel jsou dostupné pro uživatele s licencí Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jaká oprávnění potřebuji k registraci prostředky ve službě Data Catalog?
Ke spuštění nástroje pro registraci katalogu Data Catalog, budete potřebovat oprávnění na zdroji dat, který umožňuje číst metadata ze zdroje. Zahrnout také ve verzi preview, musíte mít oprávnění, které umožňují čtení dat z objektů registrována.

Data Catalog také umožňuje správcům katalogu omezit, kteří uživatelé a skupiny mohl přidat metadata do katalogu. Další informace najdete v tématu [jak zabezpečit přístup ke katalogu dat a datové assety](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog budou dostupné pro místní nasazení i?
Data Catalog je Cloudová služba, které může fungovat cloudových a místních zdrojů dat k zajištění řešení hybridní zdroj dat zjišťování. Aktuálně nejsou žádné plány pro verzi služby Data Catalog, na kterém běží v místním prostředí.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Můžete extrahovat další nebo bohatší metadata ze zdroje dat, které se dá zaregistrovat?
Aktivně pracujeme na rozšíření možností katalogu Data Catalog. Pokud chcete mít další metadata extrahovaná přímo ze zdroje dat během registrace, navrhněte ji (nebo hlasovat pro něj, pokud již byla navržena) v [zpětnou vazbu fóra Azure Data Catalog](https://feedback.azure.com/forums/906052-data-catalog). 

Pokud byste chtěli zahrnují metadata sloupce/schématu, verze Preview nebo profily data pro zdroje dat, kde se tato metadata extrahovaná modulem nástroj registrace zdroje dat slouží k přidání těchto metadat rozhraní API katalogu Data Catalog. Další informace najdete v tématu [REST API služby Azure Data Catalog](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Jak mohu omezit viditelnost registrovaných datových assetů, takže pouze určité osoby najdou?
Vyberte datové assety v katalogu dat a klikněte **převzít vlastnictví** tlačítko. Vlastníci datové assety v katalogu Data Catalog můžete změnit nastavení viditelnosti buď umožňuje všem uživatelům zjišťovat prostředky vlastněné nebo omezit viditelnost konkrétním uživatelům. Další informace najdete v tématu [spravovat datové assety v Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Jak můžu aktualizovat registrace k datovému assetu, tak, aby změny ve zdroji dat se projeví v katalogu?
Pro aktualizaci metadat pro datové assety, které jsou již zaregistrovány v katalogu, jednoduše znovu registrovat zdroj dat, která obsahuje prostředky. Všechny změny ve zdroji dat, jako například přidávání nebo odebírání z tabulek nebo zobrazení sloupce se aktualizují v katalogu, ale zachovají jakékoliv anotace poskytované uživatelům.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Můj dotaz jsem zde nenalezl odpověď. Kam se můžu obrátit pro odpovědi?
Přejděte [fóru služby Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Otázky, že se najdou svou cestu tady.
