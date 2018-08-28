---
title: Jak se připojit ke zdrojům dat ve službě Azure Data Catalog
description: Článek zvýraznění jak se připojit ke zdrojům dat zjištěným pomocí služby Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 759758c9f3d0f1dadf2048e8ef15eeab8a77ef07
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053465"
---
# <a name="how-to-connect-to-data-sources"></a>Jak se připojit ke zdrojům dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** se točí kolem lidé zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím vytěžit více z jejich stávajících dat. Klíčovým prvkem tohoto scénáře používá údaje – Jakmile uživatel zjistí zdroje dat a rozumí jeho účel, dalším krokem je připojení ke zdroji dat do jeho data se mají použít.

## <a name="data-source-locations"></a>Umístění zdroje dat.
Během registrace zdroje dat **Azure Data Catalog** přijímá metadata o zdroji dat. Tato metadata obsahují podrobnosti o umístění zdroje dat. Podrobnosti o umístění závisí ze zdroje dat pro zdroj dat, ale vždy bude obsahovat informace potřebné pro připojení. Umístění pro tabulku SQL serveru patří třeba název serveru, název databáze, název schématu a název tabulky, přestože umístění pro sestavy SQL Server Reporting Services obsahuje název serveru a cestu k sestavě. Další typy zdrojů dat budou mít umístění, které odrážejí strukturu a funkce zdrojového systému.

## <a name="integrated-client-tools"></a>Integrovaných klientských nástrojích
Nejjednodušší způsob, jak se připojit ke zdroji dat je použít "Otevřít v..." v nabídce **Azure Data Catalog** portálu. Tato nabídka zobrazí seznam možností pro připojení k vybraný datový asset.
Při použití výchozí zobrazení tile, tato nabídka je k dispozici na každé dlaždici.

 ![Otevřete tabulku SQL serveru v aplikaci Excel na dlaždici datový asset](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Pokud používáte zobrazení seznamu, v nabídce je k dispozici na panelu hledání v horní části okna portálu.

 ![Otevření sestavy služby SQL Server Reporting Services ve správci sestav z panelu hledání](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Podporované klientské aplikace
Při použití "Otevřít v..." nabídky pro zdroje dat na portálu Azure Data Catalog, správné klientská aplikace musí být nainstalován na klientském počítači.

| Otevřít v aplikaci | Přípona souboru / protokol | Verze podporované aplikace |
| --- | --- | --- |
| Excel |ODC |Aplikace Excel 2010 nebo novější |
| Excel (prvních 1000) |ODC |Aplikace Excel 2010 nebo novější |
| Power Query |XLSX |Nainstalované aplikace Excel 2016 a Excel 2010 nebo Excel 2013 pomocí Power Query pro Excel add-in |
| Power BI Desktopu |.pbix |Power BI Desktopu z července 2016 nebo novější |
| SQL Server Data Tools |vsweb: / / |Visual Studio 2013 Update 4 nebo novější pomocí nástrojů SQL Server nainstalovaný |
| Správce sestav |http:// |Zobrazit [požadavky na prohlížeč pro SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Vaše data, vaše nástroje
Možnosti dostupné v nabídce bude záviset na typu aktuálně vybraný datový asset. Samozřejmě, ne všechny možné nástroje se zahrne "Otevřít v..." nabídky, je však stále snadné připojení ke zdroji dat využít libovolný nástroj klienta. Pokud je vybrána k datovému assetu v **Azure Data Catalog** portálu, dokončení umístění se zobrazí v podokně vlastností.

 ![Informace o připojení pro tabulku SQL serveru](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Podrobné informace o připojení se bude lišit od typ zdroje dat pro typ zdroje dat, ale informace na portálu získáte všechno, co potřebujete k připojení ke zdroji dat v jakékoli nástroji klienta. Uživatelé mohou kopírovat podrobnosti o připojení pro zdroje dat, které jsou zjištěny pomocí **Azure Data Catalog**, což jim umožňuje pracovat s daty v upřednostňovaném nástroji.

## <a name="connecting-and-data-source-permissions"></a>Oprávnění k připojení a dat zdroje
I když **Azure Data Catalog** činí zdroje dat zjistitelné, přístup k datům samotné zůstane pod kontrolou vlastník zdroje dat nebo správce. Zjišťování zdroje dat v **Azure Data Catalog** neuděluje žádná oprávnění pro přístup ke zdroji dat, samotného uživatele.

Aby bylo snazší pro uživatelé objeví zdroj dat, ale nemáte příslušná oprávnění pro přístup k jeho datům, uživatelé mohou poskytovat informace ve vlastnosti žádost o přístup při zadávání poznámek ke zdroji dat. Informace, včetně odkazů na příslušný proces nebo kontaktní bod pro získání přístupu ke zdroji dat – tady se zobrazí vedle umístění zdroje dat na portálu.

 ![Informace o připojení pomocí žádosti o přístup pokynů](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Souhrn
Registrace zdroje dat s **Azure Data Catalog** díky těmto datům zjistitelné zkopírováním popisný a strukturální metadata ze zdroje dat ve službě katalogu. Jakmile se zdroje dat má byl zaregistrován a zjištění, uživatelé se mohou připojit ke zdroji dat z **Azure Data Catalog** portál "Otevřít v..." " nabídky nebo pomocí nástrojů svá data podle výběru.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurz pro podrobné informace o tom, jak se připojit ke zdrojům dat.
