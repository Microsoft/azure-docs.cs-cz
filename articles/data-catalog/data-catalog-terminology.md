---
title: Terminologie katalogu dat Azure
description: Tento článek obsahuje úvod k konceptům a termínům používaným v dokumentaci k Katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736294"
---
# <a name="azure-data-catalog-terminology"></a>Terminologie katalogu dat Azure

Tento článek obsahuje úvod k konceptům a termínům používaným v dokumentaci k Katalogu dat Azure.

## <a name="catalog"></a>Katalog

Katalog dat Azure je cloudové úložiště metadat, ve kterém lze zaregistrovat zdroje dat a datové prostředky. Katalog slouží jako centrální úložiště pro strukturální metadata extrahovaná ze zdrojů dat a pro popisná metadata přidaná uživateli.

## <a name="data-source"></a>Zdroj dat

Zdroj dat je systém nebo kontejner, který spravuje datové prostředky. Příklady zahrnují databáze serveru SQL Server, databáze Oracle, databáze služby SQL Server Analysis Services (tabulkové nebo vícerozměrné) a servery SQL Server Reporting Services.

## <a name="data-asset"></a>Datový majetek

Datové prostředky jsou objekty obsažené ve zdrojích dat, které lze zaregistrovat v katalogu. Příklady zahrnují tabulky a zobrazení serveru SQL Server, tabulky a zobrazení oracle, míry služby SQL Server Analysis Services, dimenze a licence ke virtuálním nastavením a sestavy služby SQL Server Reporting Services.

## <a name="data-asset-location"></a>Umístění datového prostředku

Katalog ukládá umístění zdroje dat nebo datového prostředku, který lze použít k připojení ke zdroji pomocí klientské aplikace. Formát a podrobnosti umístění se liší v závislosti na typu zdroje dat. Například tabulku SQL Server lze identifikovat podle jeho čtyři název součásti – název serveru, název databáze, název schématu, název objektu – zatímco SQL Server Reporting Services Sestavy lze identifikovat jeho URL.

## <a name="structural-metadata"></a>Strukturální metadata

Strukturální metadata jsou metadata extrahovaná ze zdroje dat, která popisují strukturu datového prostředku. To zahrnuje umístění datových zdrojů, jeho název a typ objektu a další charakteristiky specifické pro daný typ. Například strukturální metadata pro tabulky a zobrazení zahrnují názvy a datové typy pro sloupce objektu.

## <a name="descriptive-metadata"></a>Popisná metadata

Popisná metadata jsou metadata, která popisují účel nebo záměr datového prostředku. Obvykle popisná metadata se přidává uživatelé katalogu pomocí portálu Katalog dat Azure, ale může být také extrahována ze zdroje dat během registrace. Například nástroj registrace katalogu dat Azure extrahne popisy z vlastnosti Description ve službě SQL Server Analysis Services a služby SQL Server Reporting Services a z [ms_description rozšířené vlastnosti](https://technet.microsoft.com/library/ms190243.aspx) v databázích serveru SQL Server, pokud byly tyto vlastnosti naplněny hodnotami.

## <a name="request-access"></a>Vyžádání přístup

Popisná metadata datového prostředku mohou obsahovat informace o tom, jak požádat o přístup k datovému prostředku nebo zdroji dat. Tyto informace jsou uvedeny s umístěním datového prostředku a mohou obsahovat jednu nebo více z následujících možností:

* E-mailová adresa uživatele nebo týmu odpovědného za udělení přístupu ke zdroji dat.
* Adresa URL zdokumentovaného procesu, který musí uživatelé sledovat, aby získali přístup ke zdroji dat.
* Adresa URL nástroje pro správu identit a přístupu (například Správce identit společnosti Microsoft), který lze použít k získání přístupu ke zdroji dat.
* Volnoformátová položka, která popisuje, jak mohou uživatelé získat přístup ke zdroji dat.

## <a name="preview"></a>Preview

Náhled v katalogu dat Azure je snímek až 20 záznamů, které lze extrahovat ze zdroje dat během registrace a uloženy v katalogu s metadaty datového prostředku. Náhled může uživatelům, kteří objeví datový prostředek, pomoci lépe pochopit jeho funkci a účel. Jinými slovy zobrazení ukázkových dat může být cennější než zobrazení pouze názvů sloupců a datových typů.
Náhledy jsou podporovány pouze pro tabulky a zobrazení a musí být explicitně vybrány uživatelem během registrace.

## <a name="data-profile"></a>Profil dat

Datový profil v katalogu dat Azure je snímek metadat na úrovni tabulky a sloupce o registrovaném datovém prostředku, který lze extrahovat ze zdroje dat během registrace a uložit v katalogu s metadaty datového prostředku. Datový profil může pomoci uživatelům, kteří objeví datový prostředek lépe pochopit jeho funkci a účel. Podobně jako náhledy musí být profily dat explicitně vybrány uživatelem během registrace.

> [!NOTE]
> Extrahování datového profilu může být nákladnou operací pro velké tabulky a zobrazení a může výrazně prodloužit dobu potřebnou k registraci zdroje dat.


## <a name="user-perspective"></a>Uživatelská perspektiva

V katalogu dat Azure může každý uživatel poskytnout popisná metadata pro registrovaný datový prostředek. Každý uživatel má odlišný pohled na data a jejich použití. Správce odpovědný za server může například poskytnout podrobnosti o své smlouvě o úrovni služeb (SLA) nebo záložních oknech. správce údajů může poskytnout odkazy na dokumentaci pro obchodní procesy, které údaje podporují; a analytik může poskytnout popis v podmínkách, které jsou nejdůležitější pro ostatní analytiky a které mohou být nejcennější pro ty uživatele, kteří potřebují zjistit a pochopit data.

Každá z těchto perspektiv je ze své podstaty cenná a s Azure Data Catalog může každý uživatel poskytnout informace, které jsou pro ně smysluplné, zatímco všichni uživatelé mohou tyto informace použít k pochopení dat a jejich účelu.

## <a name="expert"></a>Odborník

Odborník je uživatel, u kterého bylo zjištěno, že má informovanou perspektivu "odborníka" pro datový zdroj. Každý uživatel může přidat sebe nebo jiného uživatele jako odborníka na datový zdroj. Být uveden jako odborník nevyjadřuje žádná další oprávnění v Azure Data Catalog; umožňuje uživatelům snadno vyhledat ty perspektivy, které jsou s největší pravděpodobností užitečné při kontrole popisných metadat datového zdroje.

## <a name="owner"></a>Vlastník

Vlastník je uživatel, který má další oprávnění pro správu datového prostředku v katalogu dat Azure. Uživatelé mohou převzít vlastnictví registrovaných datových prostředků a vlastníci mohou přidávat další uživatele jako spoluvlastníky. Další informace naleznete v tématu [Jak spravovat datové prostředky.](data-catalog-how-to-manage.md)  

> [!NOTE]
> Vlastnictví a správa jsou dostupné jenom ve standardní edici katalogu dat Azure.

## <a name="registration"></a>Registrace

Registrace je akt extrahování metadat datového prostředku ze zdroje dat a jejich kopírování do služby Azure Data Catalog. Datové prostředky, které byly zaregistrovány, pak mohou být anotovány a objeveny.

## <a name="next-steps"></a>Další kroky

[Úvodní příručka: Vytvoření katalogu dat Azure](data-catalog-get-started.md) 
