---
title: Postup dokumentování zdrojů dat v Azure Data Catalog
description: Článek s postupem popisuje, jak dokumentovat datové assety v Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 21168e403ffa8fb1472a5bfcac1fcd2079a52e2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674916"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Postup dokumentování zdrojů dat v Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** je vše, co je potřeba k tomu, aby lidé mohli zjišťovat, *pochopit* a používat zdroje dat a pomáhat organizacím získat větší hodnotu z jejich stávajících dat.

Pokud je zdroj dat zaregistrován ve **Azure Data Catalog**, jeho metadata jsou zkopírována a indexována službou, ale tento scénář nekončí. **Azure Data Catalog** také uživatelům umožňuje poskytnout svou vlastní úplnou dokumentaci, která může popsat použití a běžné scénáře pro zdroj dat.

Při [přidávání poznámek ke zdrojům dat](data-catalog-how-to-annotate.md)se dozvíte, že odborníci, kteří znají zdroj dat, si ho můžou opatřit poznámkami pomocí značek a popisu. Portál **Azure Data Catalog** obsahuje editor formátovaného textu, aby uživatelé mohli plně dokumentovat datové assety a kontejnery. Editor obsahuje formátování odstavců, jako jsou nadpisy, formátování textu, seznamy s odrážkami, číslované seznamy a tabulky.

Značky a popisy jsou skvělé pro jednoduché poznámky. Pokud ale chcete uživatelům s daty lépe pochopit použití zdroje dat a obchodních scénářů pro zdroj dat, odborník může poskytnout kompletní a podrobnou dokumentaci. Zdroj dat můžete snadno zdokumentovat. Vyberte datový Asset nebo kontejner a zvolte možnost **dokumentace**.

![Karta dokumentace v Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentování datových prostředků
Výhoda **Azure Data Catalog** dokumentace vám umožní používat Data Catalog jako úložiště obsahu k vytvoření kompletního mluveného komentáře k datovým assetům. Můžete prozkoumat podrobný obsah, který popisuje kontejnery a tabulky. Pokud již máte obsah v jiném úložišti obsahu, jako je například SharePoint nebo sdílená složka, můžete přidat do dokumentace k dokumentaci assetu, která odkazuje na tento stávající obsah. Tato funkce umožňuje, aby byly vaše stávající dokumenty více zjistitelné.

> [!NOTE]
> Dokumentace není obsažena v indexu vyhledávání.
>

![Karta dokumentace a hypertextový odkaz na webový odkaz](media/data-catalog-documentation/data-catalog-documentation2.png)

Úroveň dokumentace může být v rozsahu od popisu vlastností a hodnoty kontejneru datových assetů k podrobnému popisu schématu tabulky v rámci kontejneru. Poskytnutá úroveň dokumentace by měla být řízena vašimi obchodními potřebami. Obecně platí, že tady je několik odborníků a nevýhody dokumentování datových assetů:

* Dokumentuje jenom kontejner: veškerý obsah je na jednom místě, ale nemusí mít potřebné podrobnosti pro uživatele, aby mohli kvalifikovaně rozhodnout.
* Dokumentuje pouze tabulky: obsah je specifický pro daný objekt, ale uživatelé mají více míst pro dokumenty.
* Kontejnery a tabulky dokumentů: nejucelenější přístup, ale mohou zavádět větší údržbu dokumentů.

## <a name="summary"></a>Souhrn
Dokumentování zdrojů dat pomocí **Azure Data Catalog** může vytvořit mluvený komentář k vašim datovým assetům, a to co nejvíce podrobností.  Pomocí odkazů můžete propojit obsah uložený v existujícím úložišti obsahu, který přináší existující dokumentaci a datové prostředky dohromady. Jakmile uživatelé zjišťují příslušné datové prostředky, mohou mít úplnou sadu dokumentace.
