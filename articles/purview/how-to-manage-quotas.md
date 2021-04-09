---
title: Správa prostředků a kvót
titleSuffix: Azure Purview
description: Přečtěte si o kvótách a omezeních prostředků pro Azure dosah a o tom, jak se zvýší kvóta.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938830"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Správa a zvýšení kvót pro prostředky pomocí Azure dosah
 
Azure dosah je cloudová služba, kterou můžou používat uživatelé dat. Azure dosah slouží k centrální správě zásad správného řízení dat napříč vašimi datovými podstatami, které jsou rozložená v cloudu i v Prem prostředích. Služba umožňuje obchodním analytikům vyhledat relevantní data pomocí smysluplných obchodních podmínek. Pokud chcete zvýšit limity až do maximálního počtu pro vaše předplatné, obraťte se na podporu.
 
## <a name="azure-purview-limits"></a>Omezení Azure Purview
 
|**Prostředek**|  **Výchozí omezení**  |**Maximální limit**|
|---|---|---|
|Účty dosah na oblast, na každého tenanta (všechna předplatná jsou kombinovaná)|3|Kontaktování podpory|
|Virtuální jádra k dispozici ke skenování na účet *|160|160|
|Souběžné kontroly na účet v daném okamžiku. Limit je založen na typu prohledávaných zdrojů dat *|5 | 10 |
|Maximální doba, po kterou lze spustit kontrolu|7 dní|7 dní|
|Volání rozhraní API, na účet|10 milionů rozhraní API/měsíc pro velikost platformy 4 kapacity <br>40M API/měsíc pro velikost platformy s 16 jednotkami kapacity |10 milionů rozhraní API/měsíc pro velikost platformy 4 kapacity <br>40M API/měsíc pro velikost platformy s 16 jednotkami kapacity|
|Úložiště, na účet|10 GB pro velikost platformy na 4 jednotky kapacity <br>40 GB pro velikost platformy s 16 jednotkami kapacity |10 GB pro velikost platformy na 4 jednotky kapacity <br> 40 GB pro velikost platformy s 16 jednotkami kapacity |
|Velikost prostředků na účet|100 milionů fyzické prostředky |Kontaktování podpory|
|Maximální velikost prostředku v katalogu|2 MB|2 MB|
|Maximální délka názvu prostředku a názvu klasifikace|4 kB|4 kB|
|Maximální délka názvu a hodnoty vlastnosti assetu|32 KB|32 KB|
|Maximální délka názvu a hodnoty atributu klasifikace|32 KB|32 KB|
|Maximální počet pojmů Glosář na účet|100K|100K|
 
* Scénáře prostředí Integration runtime v místním prostředí jsou mimo obor pro limity definované ve výše uvedené tabulce. 
 
## <a name="next-steps"></a>Další kroky
 
> [!div class="nextstepaction"]
>[Kurz: prohledávání dat pomocí Azure dosah](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Kurz: navigace na domovské stránce a vyhledání assetu](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Kurz: procházení assetů a zobrazení jejich vydaných řádků](tutorial-browse-and-view-lineage.md)
