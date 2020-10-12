---
title: Jak se připojit ke zdrojům dat v Azure Data Catalog
description: Článek s postupem, jak se připojit ke zdrojům dat zjištěným pomocí Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 53a861761f819c2127228e7ef688f04d91744d25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081249"
---
# <a name="how-to-connect-to-data-sources"></a>Jak se připojit ke zdrojům dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** je vše, co je potřeba k tomu, aby lidé mohli zjišťovat, pochopit a používat zdroje dat a pomáhat organizacím získat větší hodnotu z jejich stávajících dat. Klíčový aspekt tohoto scénáře používá data – Jakmile uživatel zjistí zdroj dat a porozumí jeho účelu, dalším krokem je připojit se ke zdroji dat a umístit jeho data na použití.

## <a name="data-source-locations"></a>Umístění zdrojů dat
Při registraci zdroje dat **Azure Data Catalog** přijímá metadata o zdroji dat. Tato metadata obsahují podrobnosti o umístění zdroje dat. Podrobnosti o umístění se budou lišit od zdroje dat až po zdroj dat, ale budou vždy obsahovat informace potřebné k připojení. Například umístění pro tabulku SQL Server obsahuje název serveru, název databáze, název schématu a název tabulky, zatímco umístění pro sestavu SQL Server Reporting Services zahrnuje název serveru a cestu k sestavě. Další typy zdrojů dat budou mít umístění, která odrážejí strukturu a možnosti zdrojového systému.

## <a name="integrated-client-tools"></a>Integrované nástroje klienta
Nejjednodušší způsob, jak se připojit ke zdroji dat, je použít možnost otevřít v... v nabídce portálu **Azure Data Catalog** . V této nabídce se zobrazí seznam možností pro připojení k vybranému datovému prostředku.
Při použití výchozího zobrazení dlaždice je tato nabídka k dispozici na každé dlaždici.

 ![Otevření tabulky SQL Server v aplikaci Excel na dlaždici datový Asset](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Při použití zobrazení seznamu je nabídka k dispozici na panelu hledání v horní části okna portálu.

 ![Otevření sestavy SQL Server Reporting Services ve Správci sestav](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Podporované klientské aplikace
Při použití možnosti otevřít v... v nabídce zdrojů dat na portálu Azure Data Catalog musí být na klientském počítači nainstalovaná správná klientská aplikace.

| Otevřít v aplikaci | Přípona souboru/protokol | Podporované verze aplikace |
| --- | --- | --- |
| Excel |. odc |Excel 2010 nebo novější |
| Excel (Top 1000) |. odc |Excel 2010 nebo novější |
| Power Query |.xlsx |Excel 2016 nebo Excel 2010 nebo Excel 2013 s nainstalovaným doplňkem Power Query for Excel |
| Power BI Desktop |. pbix |Power BI Desktop červenec 2016 nebo novější |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 nebo novější s nainstalovanou SQL Server nástrojů |
| Správce sestav |http:// |Viz [požadavky na prohlížeč pro SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Vaše data, vaše nástroje
Možnosti dostupné v nabídce budou záviset na typu aktuálně vybraného datového assetu. Samozřejmě nejsou všechny možné nástroje zahrnuté do příkazu otevřít v... , ale je stále snadné se připojit ke zdroji dat pomocí libovolného klientského nástroje. Po výběru datového assetu na portálu **Azure Data Catalog** se v podokně Vlastnosti zobrazí úplné umístění.

 ![Informace o připojení pro SQL Serverovou tabulku](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Podrobnosti o připojení se budou lišit od typu zdroje dat až po typ zdroje dat, ale informace obsažené na portálu vám poskytnou všechno, co potřebujete pro připojení ke zdroji dat v jakémkoli klientském nástroji. Uživatelé mohou kopírovat podrobnosti o připojení pro zdroje dat, které byly zjištěny pomocí **Azure Data Catalog**, a umožnit jim tak práci s daty v jejich výběru nástrojem.

## <a name="connecting-and-data-source-permissions"></a>Připojení a oprávnění zdrojů dat
I když **Azure Data Catalog** zpřístupňuje zdroje dat, zůstane přístup k samotným údajům pod kontrolou vlastníka nebo správce zdroje dat. Zjišťování zdroje dat v **Azure Data Catalog** neuděluje uživateli žádná oprávnění pro přístup ke zdroji dat.

Aby bylo snazší uživatelům, kteří si objevují zdroj dat, ale nemají oprávnění pro přístup k datům, můžou při přidávání poznámek ke zdroji dat poskytnout uživatelům informace ve vlastnosti žádosti o přístup. Zde uvedené informace – včetně odkazů na proces nebo kontaktní bod pro získání přístupu ke zdroji dat – se zobrazují společně s informacemi o umístění zdroje dat na portálu.

 ![Informace o připojení s poskytnutými pokyny pro přístup k žádosti](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Shrnutí
Registrace zdroje dat pomocí **Azure Data Catalog** zajišťuje, aby byla data zjistitelná zkopírováním strukturních a popisných metadat ze zdroje dat do služby katalogu. Po zaregistrování zdroje dat a zjištění, že se uživatelé mohou připojit ke zdroji dat z portálu **Azure Data Catalog** otevřít v... nebo pomocí svých nástrojů pro data dle výběru.

## <a name="see-also"></a>Viz také
* [Začínáme s Azure Data Catalog](data-catalog-get-started.md) kurzu pro podrobné informace o tom, jak se připojit ke zdrojům dat.
