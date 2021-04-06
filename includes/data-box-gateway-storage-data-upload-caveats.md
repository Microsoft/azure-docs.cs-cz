---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581764"
---
Následující omezení se vztahují na data při přesunu do Azure.

- Doporučujeme, aby více než jedno zařízení nepsalo do stejného kontejneru.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB nebo soubor), který má stejný název jako objekt, který se právě kopíruje, zařízení přepíše soubor v cloudu.
- Prázdná hierarchie adresářů (bez souborů) vytvořená v části sdílet složky není nahrána do kontejnerů objektů BLOB.
- Data můžete kopírovat pomocí přetažení pomocí Průzkumníka souborů nebo pomocí příkazového řádku. Pokud je agregovaná velikost kopírovaných souborů větší než 10 GB, doporučujeme použít program hromadného kopírování, například `Robocopy` nebo `rsync` . Nástroje hromadného kopírování opakují operace kopírování pro občasné chyby a zajištění vyšší odolnosti.
- Pokud sdílená složka přidružená k kontejneru úložiště Azure nahraje objekty blob, které neodpovídají typu objektů BLOB definovaných pro sdílenou složku v době vytváření, takové objekty BLOB se neaktualizují. Pokud třeba na zařízení vytvoříte sdílenou složku objektů blob bloku, přiřaďte ji ke stávajícímu kontejneru cloudu, který má objekty blob stránky, aktualizujte tuto sdílenou složku a Stáhněte soubory a pak upravte některé aktualizované soubory, které už jsou uložené jako objekty blob stránky v cloudu, a zobrazí se chyby nahrávání.
- Po vytvoření souboru ve sdílených složkách se přejmenování souboru nepodporuje.
- Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
- Pokud použijete `rsync` ke kopírování dat, `rsync -a` možnost není podporovaná.