---
title: Postup připojení ke zdrojům dat | Microsoft Docs
description: 'Postupy: článek zvýraznění jak se připojit ke zdrojům dat zjištěným s Azure Data Catalog.'
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: b4dfc7a900e42b348c416d2d8c05b73fb15847ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-connect-to-data-sources"></a>Jak se připojit ke zdrojům dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém pro zjišťování zdrojů dat organizace. Jinými slovy **Azure Data Catalog** se točí kolem osoby zjišťovat, pochopit a používat zdroje dat a pomáhá organizacím získat větší hodnotu z jejich stávající data. Klíčovým prvkem tento scénář používá data – po uživatele zjistí zdroje dat a porozuměl jim svůj účel, dalším krokem je pro připojení ke zdroji dat se umístí jeho data se mají použít.

## <a name="data-source-locations"></a>Umístění zdroje dat.
Během registrace zdroje dat **Azure Data Catalog** obdrží metadata o zdroji dat. Tato metadata obsahuje podrobnosti o zdroji dat umístění. Podrobnosti o umístění bude lišit ze zdroje dat pro zdroj dat, ale bude vždy obsahovat informace potřebné pro připojení. Umístění pro tabulku systému SQL Server například obsahuje název serveru, název databáze, název schématu a název tabulky při umístění pro sestavy SQL Server Reporting Services obsahuje název serveru a cestu k sestavě. Ostatní typy zdrojů dat bude mít umístění, která odráží strukturu a možnosti zdrojového systému.

## <a name="integrated-client-tools"></a>Integrovaného klienta nástroje
Nejjednodušší způsob, jak se připojit ke zdroji dat se má používat "Otevřít v..." v nabídce **Azure Data Catalog** portálu. Tato nabídka zobrazí seznam možností pro připojení k vybrané datové prostředky.
Při použití výchozí zobrazení dlaždice, tato nabídka je k dispozici na každou dlaždici.

 ![Otevírání tabulku systému SQL Server v aplikaci Excel na dlaždici asset dat](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Pokud používáte zobrazení seznamu, je k dispozici na panelu Hledat v horní části okna portálu na v nabídce.

 ![Otevření sestavy služby SQL Server Reporting Services ve správci sestav z panelu Hledat](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Podporované klientské aplikace
Při použití "Otevřít v..." nabídky pro zdroje dat na portálu Azure Data Catalog správné klientská aplikace musí být nainstalován na klientském počítači.

| Otevřít v aplikaci | Příponu souboru / protocol | Verze podporované aplikace |
| --- | --- | --- |
| Excel |ODC |Excel 2010 nebo novější |
| Aplikace Excel (horní 1000) |ODC |Excel 2010 nebo novější |
| Power Query |XLSX |Nainstalované aplikace Excel 2016 nebo aplikaci Excel 2010 nebo Excel 2013 doplněk Power Query pro doplněk aplikace Excel |
| Power BI Desktop |.pbix |Power BI Desktop července 2016 nebo novější |
| SQL Server Data Tools |vsweb: / / |Visual Studio 2013 Update 4 nebo novější s nástrojů systému SQL Server nainstalován |
| Správce sestav |http:// |V tématu [požadavky na prohlížeč pro SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Data, vaše nástroje
Možnosti dostupné v nabídce bude záviset na typu dat asset aktuálně vybrané. Samozřejmě, ne všechny možné nástroje bude součástí "Otevřít v..." nabídce, ale je stále snadné připojení ke zdroji dat pomocí libovolného nástroje klienta. Vybrání datový prostředek v **Azure Data Catalog** portálu umístění dokončení se zobrazí v podokně vlastností.

 ![Informace o připojení pro tabulku systému SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Podrobnosti o informace o připojení se liší od typ zdroje dat na typ zdroje dat, ale informace obsažené v portálu získáte vše potřebné pro připojení ke zdroji dat v nástroji žádné klienta. Uživatelé mohou kopírovat podrobnosti připojení zdroje dat, které budou zjištěny pomocí **Azure Data Catalog**, tím jim umožníte pracovat s daty v upřednostňovaném nástroji.

## <a name="connecting-and-data-source-permissions"></a>Připojení a data zdroje oprávnění
I když **Azure Data Catalog** díky zdroje dat zjistitelný, přístup k datům samotné zůstane pod kontrolou vlastník zdroj dat nebo správce. Zjišťování zdroje dat v **Azure Data Catalog** uživatele neposkytuje žádné oprávnění k přístupu ke zdroji dat, sám sebe.

Aby bylo snazší pro uživatele, kteří zjistit zdroje dat, ale nemáte příslušná oprávnění pro přístup k jeho data, uživatelé mohou poskytovat informace ve vlastnosti žádosti o přístup, při zadávání poznámek ke zdroji dat. Informace, které jsou tady uvedené – včetně odkazy na proces nebo kontaktní bod pro získání přístup ke zdroji dat – jsou poskytovány společně se umístění zdroje dat na portálu.

 ![Informace o připojení se žádost o přístup pokynů](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Souhrn
Registrace k datovému zdroji prostřednictvím **Azure Data Catalog** díky dat zjistitelný zkopírováním strukturální a popisný metadat ze zdroje dat ve službě katalogu. Jakmile zdroj dat má byl zaregistrován a zjistit, uživatelé se mohou připojit ke zdroji dat z **Azure Data Catalog** portál "Otevřít v..." " nabídky nebo pomocí nástrojů jejich data výběru.

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurz podrobné informace o tom, jak se připojit ke zdrojům dat.
