---
title: Jak dokumentovat zdroje dat v Katalogu dat Azure
description: Článek s postupem, který zvýrazňuje, jak dokumentovat datové prostředky v katalogu dat Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950187"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Jak dokumentovat zdroje dat v Katalogu dat Azure

## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná cloudová služba, která slouží jako systém registrace a zjišťování pro zdroje podnikových dat. Jinými slovy, **Azure Data Catalog** je především o pomoci lidem objevovat, *pochopit*a používat zdroje dat a pomáhá organizacím získat větší hodnotu z jejich stávajících dat.

Když je zdroj dat registrovaný v **Katalogu dat Azure**, jeho metadata se zkopírují a indexují službou, ale příběh tím nekončí. **Azure Data Catalog** také umožňuje uživatelům poskytovat vlastní úplnou dokumentaci, která může popisovat využití a běžné scénáře pro zdroj dat.

V [části Jak oznamovat zdroje dat](data-catalog-how-to-annotate.md)pomocí poznámek se dozvíte, že odborníci, kteří znají zdroj dat, jej mohou oznamovat značkami a popisem. Portál **Katalog u dat Azure** obsahuje editor s formátovaným textem, takže uživatelé mohou plně dokumentovat datové prostředky a kontejnery. Editor obsahuje formátování odstavců, například nadpisy, formátování textu, seznamy s odrážkami, číslované seznamy a tabulky.

Značky a popisy jsou skvělé pro jednoduché poznámky. Aby však spotřebitelé dat lépe porozuměli použití zdroje dat a obchodním scénářům pro zdroj dat, může odborník poskytnout úplnou podrobnou dokumentaci. Dokumentování zdroje dat je snadné. Vyberte datový prostředek nebo kontejner a zvolte **Dokumentace**.

![Karta Dokumentace v katalogu dat](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentace datových prostředků
Výhoda dokumentace **k Katalogu dat Azure** umožňuje použít katalog dat jako úložiště obsahu k vytvoření úplného vyprávění o vašich datových prostředcích. Můžete prozkoumat podrobný obsah, který popisuje kontejnery a tabulky. Pokud už máte obsah v jiném úložišti obsahu, jako je SharePoint nebo sdílená složka, můžete přidat do odkazů na dokumentaci datového zdroje odkazy na tento existující obsah. Díky této funkci budou stávající dokumenty snadněji zjistitelné.

> [!NOTE]
> Dokumentace není zahrnuta v indexu vyhledávání.
>

![Karta Dokumentace a hypertextový odkaz na webový odkaz](media/data-catalog-documentation/data-catalog-documentation2.png)

Úroveň dokumentace může sahat od popisu charakteristiky a hodnoty kontejneru datového prostředku na podrobný popis schématu tabulky v rámci kontejneru. Úroveň poskytované dokumentace by měla být řízena vašimi obchodními potřebami. Ale obecně, zde je několik klady a zápory dokumentování datových aktiv:

* Dokument ujte pouze kontejner: Veškerý obsah je na jednom místě, ale může postrádat potřebné podrobnosti, aby uživatelé mohli učinit informované rozhodnutí.
* Dokument pouze tabulky: Obsah je specifický pro daný objekt, ale uživatelé mají pro dokumenty více míst.
* Kontejnery dokumentů a tabulky: Nejkomplexnější přístup, ale může zavést další údržbu dokumentů.

## <a name="summary"></a>Souhrn
Dokumentování zdrojů dat pomocí **Katalogu dat Azure** může vytvořit vyprávění o vašich datových prostředkůch co nejpodrobněji, kolik potřebujete.  Pomocí odkazů můžete propojit obsah uložený v existujícím úložišti obsahu, který sdružuje vaše stávající dokumenty a datové datové prostředky. Jakmile uživatelé zjistí příslušné datové prostředky, mohou mít úplnou sadu dokumentace.
