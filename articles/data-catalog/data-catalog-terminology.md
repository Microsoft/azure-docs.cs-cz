---
title: Terminologie služby Azure Data Catalog
description: Tento článek obsahuje přehled konceptů a termínů používaných v dokumentaci k Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 0ef83af3df942b456d0036321cf88e4a1ce7afa7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407752"
---
# <a name="azure-data-catalog-terminology"></a>Terminologie služby Azure Data Catalog
## <a name="catalog"></a>Katalog
Azure Data Catalog je úložiště založené na cloudu metadat, při kterém jsou data mohou být registrovány zdrojům a data prostředků. V katalogu slouží jako centrální úložiště pro strukturální metadata extrahovaná ze zdroje dat a popisných metadat, přidali uživateli.

## <a name="data-source"></a>Zdroj dat
Zdroj dat je systém nebo kontejner, který spravuje datových assetů. Mezi příklady patří databáze systému SQL Server, databáze Oracle, databáze SQL Server Analysis Services (tabulková nebo multidimenzionální) a serverů SQL Server Reporting Services.

## <a name="data-asset"></a>Datovému assetu
Datové prostředky jsou objekty obsažené v rámci zdroje dat, které mohou být zaregistrované v katalogu. Mezi příklady patří tabulek systému SQL Server a zobrazení, Oracle tabulek a zobrazení, SQL Server Analysis Services opatření, dimenze a klíčové ukazatele výkonu a sestav služby SQL Server Reporting Services.

## <a name="data-asset-location"></a>Umístění prostředku dat
Katalog ukládá umístění zdroje dat nebo datovému prostředku, který slouží k připojení ke zdroji pomocí klientské aplikace. Formát a podrobnosti o umístění se liší v závislosti na typu zdroje dat. Například tabulky serveru SQL Server lze identifikovat podle názvu čtyř částí – název serveru, název databáze, název schématu, název objektu –, zatímco SQL Server Reporting Services sestavu lze identifikovat podle její adresy URL.

## <a name="structural-metadata"></a>Strukturální metadata
Strukturální metadata jsou metadata extrahovaná ze zdroje dat, který popisuje strukturu datového prostředku. To zahrnuje umístění prostředků, název objektu a typ a další vlastnosti specifické pro typ. Například strukturální metadata pro tabulky a zobrazení obsahuje názvy a datové typy sloupců objektu.

## <a name="descriptive-metadata"></a>Popisná metadata
Popisnými metadaty je metadat, který popisuje účel nebo záměr k datovému assetu. Obvykle popisnými metadaty je přidaný uživatelé katalogu na portálu Azure Data Catalog, ale to může také být extrahována ze zdroje dat během registrace. Například nástroj pro registraci Azure Data Catalog, který extrahuje popisy z vlastnosti popis v SQL Server Analysis Services a SQL Server Reporting Services a z [ms_description rozšířená vlastnost](https://technet.microsoft.com/library/ms190243.aspx) v SQL Databáze serveru, pokud tyto vlastnosti jsou vyplněna pomocí hodnoty.

## <a name="request-access"></a>Vyžádat si přístup
K datovému assetu popisnými metadaty může zahrnovat informace o tom, jak požádat o přístup ke zdroji dat nebo datovému assetu. Tyto informace se asset umístění dat a může obsahovat jeden nebo více z následujících možností:

* E-mailovou adresu uživatele nebo tým odpovědný za udělení přístupu ke zdroji dat.
* Adresa URL zdokumentovaných proces, který uživatelé musí postupovat podle pokynů k získání přístupu ke zdroji dat.
* Adresa URL identit a přístupu nástroj pro správu (jako je například Microsoft Identity Manager), který můžete použít k získání přístupu ke zdroji dat.
* Položka prostého textu, který popisuje, jak uživatelé můžou získat přístup ke zdroji dat.

## <a name="preview"></a>Preview
Verze preview ve službě Azure Data Catalog je snímek až 20 záznamů, které můžete extrahovaná ze zdroje dat během registrace a uloženy v katalogu s metadaty datový asset. Verzi preview může pomoci uživatelům, kteří je zjistili k datovému assetu lépe porozumět jeho funkci a účel. Jinými slovy zobrazení ukázkových dat může být větší hodnotu než když vidíte pouze názvy sloupců a datové typy.
Verze Preview se podporují jenom pro tabulky a zobrazení a musí být explicitně vybraný uživatel během registrace.

## <a name="data-profile"></a>Profil dat
Profil dat ve službě Azure Data Catalog je snímek úrovni tabulek a sloupců metadata o registrované datové prostředky, můžete extrahovaná ze zdroje dat během registrace a uloženy v katalogu s metadaty datový asset. Profil dat může pomoci uživatelům, kteří je zjistili k datovému assetu lépe porozumět jeho funkci a účel. Podobně jako verze Preview, data profily musí explicitně vybrat uživatele během registrace.

> [!NOTE]
> Extrahování dat profilu může být nákladná operace pro velké tabulky a zobrazení a můžou výrazně zvýšit dobu potřebnou k registraci zdroje dat.
>
>

## <a name="user-perspective"></a>Pohledu uživatele
Ve službě Azure Data Catalog může každý uživatel poskytnout popisnými metadaty pro registrované datové prostředky. Každý uživatel má odlišné perspektivou na data a jeho použití. Například může správcem odpovědným za serveru zadejte podrobnosti o jeho smlouvu o úrovni služeb (SLA) nebo zálohování systému windows. společnosti steward data mohou poskytnout odkazy na dokumentaci pro obchodní procesy dat podporuje; a analytik může také popis v podmínkách, které jsou nejrelevantnější pro ostatní analytici a může být pro uživatele, kteří potřebují zjišťování a pochopení dat vás nejpřínosnější.

Každá z těchto perspektiv jsou ze své podstaty důležité a pomocí služby Azure Data Catalog může každý uživatel poskytnout informace, která má smysl, zatímco všichni uživatelé tyto informace slouží k pochopení dat a jeho účel.

## <a name="expert"></a>Expert
Jste odborník je uživatel, který byl identifikován jako s informovaně "odborné" perspektivy pro datový asset. Každý uživatel může přidat sobě nebo jinému uživateli jako odborník pro určitý prostředek. Uvedeno jako odborník neznamená žádná další oprávnění ve službě Azure Data Catalog; umožňuje uživatelům snadno najít tyto perspektivy, které jsou nejvíce mohly být užitečné při kontrole popisnými metadaty prostředků.

## <a name="owner"></a>Vlastník
Vlastníkem je uživatel, který má dodatečná oprávnění pro správu k datovému assetu ve službě Azure Data Catalog. Uživatelé můžou převzít vlastnictví registrovaných datových assetů a vlastníci můžete přidat další uživatele jako spoluvlastníky. Další informace najdete v části [Správa datových prostředků](data-catalog-how-to-manage.md)  

> [!NOTE]
> Vlastnictví a správa jsou k dispozici pouze v nástroje Azure Data Catalog Standard Edition.
>
>

## <a name="registration"></a>Registrace
Registrace je proces extrahování dat asset metadata ze zdroje dat a zkopírování do služby Azure Data Catalog. Datové assety, které jsou zaregistrovány můžete pak s poznámkami a zjistit.

## <a name="see-also"></a>Další informace najdete v tématech
* [Co je Azure Data Catalog?](data-catalog-what-is-data-catalog.md) – Tento článek obsahuje přehled služby Azure Data Catalog, hodnota, kterou poskytuje a scénáře, které podporuje.
* [Začínáme s Azure Data Catalog](data-catalog-get-started.md) – Tento článek poskytuje začátku do konce kurz, který ukazuje, jak pomocí služby Azure Data Catalog pro zjišťování zdrojů dat.  
