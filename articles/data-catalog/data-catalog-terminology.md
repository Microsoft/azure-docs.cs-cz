---
title: Azure Data Catalog terminologie
description: Tento článek poskytuje Úvod k konceptům a pojmům, které se používají v dokumentaci Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68736294"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog terminologie

Tento článek poskytuje Úvod k konceptům a pojmům, které se používají v dokumentaci Azure Data Catalog.

## <a name="catalog"></a>Katalog

Azure Data Catalog je cloudové úložiště metadat, ve kterém se dají registrovat zdroje dat a datové assety. Katalog slouží jako centrální umístění úložiště pro strukturální metadata extrahovaná ze zdrojů dat a pro popisná metadata přidaná uživateli.

## <a name="data-source"></a>Zdroj dat

Zdroj dat je systém nebo kontejner, který spravuje datové assety. Mezi příklady patří databáze SQL Server, databáze Oracle, databáze SQL Server Analysis Services (tabulkové nebo multidimenzionální) a servery SQL Server Reporting Services.

## <a name="data-asset"></a>Datový Asset

Datové assety jsou objekty obsažené ve zdrojích dat, které je možné zaregistrovat v katalogu. Mezi příklady patří SQL Server tabulky a zobrazení, tabulky a zobrazení Oracle, SQL Server Analysis Services míry, dimenze a klíčové ukazatele výkonu a sestavy SQL Server Reporting Services.

## <a name="data-asset-location"></a>Umístění datového assetu

Katalog ukládá umístění zdroje dat nebo datového assetu, který se dá použít k připojení ke zdroji pomocí klientské aplikace. Formát a podrobnosti umístění se liší v závislosti na typu zdroje dat. Například SQL Server tabulka může být identifikována pomocí jejího názvu čtyř částí – název serveru, název databáze, název schématu, název objektu – zatímco SQL Server Reporting Services sestavu lze identifikovat pomocí adresy URL.

## <a name="structural-metadata"></a>Strukturovaná metadata

Strukturální metadata jsou metadata extrahovaná ze zdroje dat, který popisuje strukturu datového assetu. To zahrnuje umístění prostředků, jeho název a typ objektu a další vlastnosti specifické pro typ. Například strukturální metadata pro tabulky a zobrazení obsahují názvy a datové typy pro sloupce objektu.

## <a name="descriptive-metadata"></a>Popisná metadata

Popisná metadata jsou metadata, která popisují účel nebo záměr datového assetu. Uživatelé katalogu obvykle přidávají popisné metadata pomocí portálu Azure Data Catalog, ale je možné je také extrahovat ze zdroje dat během registrace. Například nástroj pro registraci Azure Data Catalog extrahuje popisy z vlastnosti Description v SQL Server Analysis Services a SQL Server Reporting Services a v [MS_Description rozšířené vlastnosti](https://technet.microsoft.com/library/ms190243.aspx) v SQL Server databázích, pokud byly tyto vlastnosti naplněny hodnotami.

## <a name="request-access"></a>Vyžádání přístup

Popisná metadata datového assetu můžou obsahovat informace o tom, jak požádat o přístup k datovému assetu nebo ke zdroji dat. Tyto informace se zobrazují v umístění datového assetu a mohou obsahovat jednu nebo více z následujících možností:

* E-mailová adresa uživatele nebo týmu, který je zodpovědný za udělení přístupu ke zdroji dat.
* Adresa URL dokumentovaného procesu, který musí uživatelé dodržovat pro získání přístupu ke zdroji dat.
* Adresa URL nástroje pro správu identit a přístupu (například Microsoft Identity Manager), který lze použít k získání přístupu ke zdroji dat.
* Bezplatná položka, která popisuje, jak můžou uživatelé získat přístup ke zdroji dat.

## <a name="preview"></a>Preview

Verze Preview v Azure Data Catalog je snímek až 20 záznamů, které se dají extrahovat ze zdroje dat během registrace a uložit v katalogu s metadaty datového assetu. Verze Preview může uživatelům, kteří objevují datový Asset, lépe pochopit jeho funkci a účel. Jinými slovy, zobrazení ukázkových dat může být výhodnější než zobrazení pouze názvů sloupců a datových typů.
Verze Preview jsou podporovány pouze pro tabulky a zobrazení a při registraci je musí explicitně vybrat uživatel.

## <a name="data-profile"></a>Profil dat

Datový profil v Azure Data Catalog je snímek metadat na úrovni tabulky a sloupce o registrovaných datových assetech, které se dají extrahovat ze zdroje dat během registrace a uložit v katalogu s metadaty datového assetu. Datový profil může uživatelům, kteří objevují datový Asset, lépe pochopit jeho funkci a účel. Podobně jako verze Preview musí být profily dat explicitně vybrané uživatelem během registrace.

> [!NOTE]
> Extrakce datového profilu může být nákladná operace pro velké tabulky a zobrazení a může významně prodloužit dobu potřebnou k registraci zdroje dat.


## <a name="user-perspective"></a>Perspektiva uživatele

V Azure Data Catalog může libovolný uživatel poskytnout popisné metadata pro registrovaný datový Asset. Každý uživatel má odlišnou perspektivu pro data a jejich použití. Správce zodpovědný za server může například poskytnout podrobné informace o smlouvě o úrovni služeb (SLA) nebo systému Windows Backup. data Steward mohou poskytnout odkazy na dokumentaci pro obchodní procesy, které data podporují. a analytik může v rámci podmínek, které jsou relevantní pro jiné analytiky, poskytnout popis, který může být nejužitečnější pro uživatele, kteří potřebují zjistit a pochopit data.

Každé z těchto perspektiv je ze své podstaty cenné a s Azure Data Catalog může každý uživatel poskytnout informace, které jsou pro ně smysluplné, zatímco všichni uživatelé mohou tyto informace pochopit a jejich účel využít.

## <a name="expert"></a>Odborník

Odborník je uživatel, který byl identifikován jako s ohledem na perspektivu "expert" pro datový Asset. Každý uživatel může jako odborník na prostředek přidat sami sebe nebo jiného uživatele. V seznamu jako odborník nenesou žádná další oprávnění v Azure Data Catalog; umožňuje uživatelům snadno najít tyto perspektivy, které jsou pravděpodobně užitečné při kontrole popisných metadat assetu.

## <a name="owner"></a>Vlastník

Vlastník je uživatel, který má další oprávnění ke správě datového assetu v Azure Data Catalog. Uživatelé můžou převzít vlastnictví registrovaných datových assetů a vlastníci můžou přidat další uživatele jako spoluvlastníky. Další informace najdete v tématu [Správa datových prostředků](data-catalog-how-to-manage.md) .  

> [!NOTE]
> Vlastnictví a správa jsou k dispozici pouze v edici Standard Azure Data Catalog.

## <a name="registration"></a>Registrace

Registrace je Act z extrakce metadat datového assetu ze zdroje dat a jeho zkopírováním do služby Azure Data Catalog. Datové prostředky, které jsou zaregistrované, je pak možné opatřit poznámkami a zjistit.

## <a name="next-steps"></a>Další kroky

[Rychlý Start: vytvoření Azure Data Catalog](data-catalog-get-started.md) 
