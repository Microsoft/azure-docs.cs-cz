---
title: Postup dokumentování zdrojů dat ve službě Azure Data Catalog
description: Článek zvýraznění dokumentování datových prostředků ve službě Azure Data Catalog.
services: data-catalog
author: spelluru
ms.author: spelluru
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4d3dfac425caf36e9ca9a4d3593bba336b18b7a3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053750"
---
# <a name="document-data-sources"></a>Dokumentování zdrojů dat
## <a name="introduction"></a>Úvod
**Microsoft Azure Data Catalog** je plně spravovaná Cloudová služba, která slouží jako systém registrace a systém zjišťování pro podnikové zdroje dat. Jinými slovy **Azure Data Catalog** se točí kolem pomáhá lidem objevit, *pochopit*a použije zdroje dat a pomáhá organizacím vytěžit více z jejich stávajících dat.

Když je zdroj dat zaregistrován ve službě **Azure Data Catalog**, se zkopíruje a službou indexována, jeho metadata, ale scénář nekončí existuje. **Azure Data Catalog** také umožňuje uživatelům zadat vlastní ucelenou dokumentaci popisující použití a běžné scénáře pro zdroj dat.

V [postup přidání poznámek ke zdrojům dat](data-catalog-how-to-annotate.md), přečtěte si, že odborníky, kteří znají zdroj dat můžete opatřit poznámkami ho pomocí značek a popis. **Azure Data Catalog** portál obsahuje editor formátovaného textu, takže uživatelé mohou plně dokumentu datových assetů a kontejnerů. Editor obsahuje formátování odstavce, jako je například záhlaví, formátování textu, seznamy s odrážkami, číslované seznamy a tabulky.

Značky a popisy se skvěle hodí pro jednoduché poznámky. Abychom spotřebitelé dat lépe porozumět zdroji dat a obchodní scénáře použití pro zdroj dat, ale odborníky může poskytovat kompletní a podrobné dokumentaci. Je snadné zdroji dat dokumentu. Vyberte datový prostředek nebo kontejner a zvolte **dokumentaci**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentování datových prostředků
Výhodou **Azure Data Catalog** dokumentace ke službě umožňuje použít katalog dat jako úložiště obsahu k vytvoření kompletní příběh vašich datových prostředků. Můžete procházet podrobné informace, které popisují kontejnery a tabulky. Pokud už máte obsah v jiném úložišti obsahu, jako je SharePoint nebo sdílenou složku, můžete je přidat odkazy na dokumentaci asset odkazovat na tento existující obsah. Díky této funkci můžete vaše stávající dokumenty lepší dostupnost.

> [!NOTE]
> Dokumentace ke službě není součástí indexu vyhledávání.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

Úroveň dokumentace může být v rozsahu od popisující vlastnosti a hodnoty datového assetu kontejneru na podrobný popis schéma tabulky v rámci kontejneru. Úroveň poskytnutá dokumentace by měl vycházet podle vašich obchodních potřeb. Ale obecně platí, tady je pár výhody a nevýhody dokumentování datových prostředků:

* Zdokumentujte jenom kontejner: veškerý obsah, který je na jednom místě, ale pravděpodobně chybí nezbytné podrobnosti uživatelé provést informované rozhodnutí.
* Zdokumentujte pouze tabulky: obsah je specifické pro daný objekt, ale vaši uživatelé mají více místa pro dokumenty.
* Zdokumentujte kontejnerů a tabulek: nejkomplexnější přístup, ale může způsobit další údržby dokumenty.

## <a name="summary"></a>Souhrn
Dokumentování zdrojů dat s **Azure Data Catalog** můžete vytvořit příběh o datových prostředcích v co nejvíce podrobností, podle potřeby.  Pomocí odkazů můžete propojit k obsahu uloženému v existující úložiště obsahu, který spojuje existující dokumenty a datových assetů. Jakmile uživatelé zjistit příslušné datové assety, můžou mít úplnou sadu dokumentace.
