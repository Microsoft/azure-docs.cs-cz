---
title: Jak se připojit ke zdrojům dat v Katalogu dat Azure
description: Článek s postupem, který zvýrazňuje, jak se připojit ke zdrojům dat zjištěným pomocí katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976774"
---
# <a name="how-to-connect-to-data-sources"></a>Jak se připojit ke zdrojům dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, **Azure Data Catalog** je především o pomoci lidem objevovat, pochopit a používat zdroje dat a pomáhá organizacím získat větší hodnotu z jejich stávajících dat. Klíčovým aspektem tohoto scénáře je použití dat – jakmile uživatel zjistí zdroj dat a pochopí jeho účel, dalším krokem je připojení ke zdroji dat a použít jeho data.

## <a name="data-source-locations"></a>Umístění zdrojů dat
Během registrace zdroje dat **azure katalog dat** obdrží metadata o zdroji dat. Tato metadata obsahují podrobnosti o umístění zdroje dat. Podrobnosti o umístění se budou lišit od zdroje dat ke zdroji dat, ale vždy budou obsahovat informace potřebné k připojení. Například umístění pro tabulku serveru SQL Server obsahuje název serveru, název databáze, název schématu a název tabulky, zatímco umístění sestavy služby SQL Server Reporting Services obsahuje název serveru a cestu k sestavě. Jiné typy zdrojů dat budou mít umístění, která odrážejí strukturu a možnosti zdrojového systému.

## <a name="integrated-client-tools"></a>Integrované klientské nástroje
Nejjednodušší způsob, jak se připojit ke zdroji dat, je použít "Otevřít v..." na portálu **Katalog dat Azure.** Tato nabídka zobrazuje seznam možností připojení k vybranému datovému zdroji.
Při použití výchozího zobrazení dlaždic je tato nabídka k dispozici na každé dlaždici.

 ![Otevření tabulky serveru SQL Server v Excelu z dlaždice datového prostředku](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Při použití zobrazení seznamu je nabídka k dispozici na panelu hledání v horní části okna portálu.

 ![Otevření sestavy služby SQL Server Reporting Services ve Správci sestav](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Podporované klientské aplikace
Při použití "Otevřít v..." nabídka pro zdroje dat na portálu Katalog dat Azure, musí být v klientském počítači nainstalována správná klientská aplikace.

| Otevřít v aplikaci | Přípona souboru / protokol | Podporované verze aplikací |
| --- | --- | --- |
| Excel |Odc |Excel 2010 nebo novější |
| Excel (1000 nejlepších) |Odc |Excel 2010 nebo novější |
| Power Query |.xlsx |Excel 2016 nebo Excel 2010 nebo Excel 2013 s nainstalovaným doplňkem Power Query pro Excel |
| Power BI Desktop |.pbix |Power BI Desktop červenec 2016 nebo novější |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 nebo novější s nainstalovanými nástroji SQL Serveru |
| Správce sestav |http:// |Zobrazit [požadavky prohlížeče pro službu SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Vaše data, vaše nástroje
Možnosti dostupné v nabídce budou záviset na typu aktuálně vybraného datového prostředku. Samozřejmě, že ne všechny možné nástroje budou zahrnuty do "Open in..." v nabídce, ale je stále snadné se připojit ke zdroji dat pomocí libovolného klientského nástroje. Když je datový prostředek vybraný na portálu **Katalogu dat Azure,** zobrazí se v podokně vlastností úplné umístění.

 ![Informace o připojení pro tabulku serveru SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Podrobnosti informací o připojení se budou lišit od typu zdroje dat k typu zdroje dat, ale informace obsažené na portálu vám poskytnou vše, co potřebujete pro připojení ke zdroji dat v libovolném klientském nástroji. Uživatelé mohou zkopírovat podrobnosti o připojení pro zdroje dat, které zjistili pomocí **katalogu dat Azure**, což jim umožňuje pracovat s daty v nástroji, který si zvolí.

## <a name="connecting-and-data-source-permissions"></a>Oprávnění pro připojení a zdroj dat
Přestože **Azure Data Catalog** umožňuje zdroje dat zjistitelné, přístup k samotným datům zůstává pod kontrolou vlastníka nebo správce zdroje dat. Zjišťování zdroje dat v **Katalogu dat Azure** neposkytuje uživateli žádná oprávnění k přístupu ke zdroji dat samotnému.

Aby bylo snazší pro uživatele, kteří zjišťují zdroj dat, ale nemají oprávnění k přístupu k jeho datům, mohou uživatelé při opomíjení zdroje dat poskytnout informace ve vlastnosti Požádat o přístup. Zde uvedené informace – včetně odkazů na proces nebo kontaktní místo pro získání přístupu ke zdroji dat – jsou prezentovány společně s informacemi o poloze zdroje dat na portálu.

 ![Informace o připojení s poskytnutými pokyny k vyžádání](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Souhrn
Registrace zdroje dat pomocí **Katalogu dat Azure** umožňuje, aby tato data zjistitelné zkopírováním strukturální a popisné metadata ze zdroje dat do služby katalogu. Jakmile je zdroj dat zaregistrován a zjištěn, uživatelé se můžou ke zdroji dat připojit z portálu **katalogu dat Azure** "Otevřít v...". nebo pomocí svých datových nástrojů.

## <a name="see-also"></a>Viz také
* [Začínáme s kurzem Katalogu dat Azure,](data-catalog-get-started.md) kde nazemte podrobné informace o tom, jak se připojit ke zdrojům dat.
