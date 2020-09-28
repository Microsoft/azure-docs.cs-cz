---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401045"
---
Následující omezení se vztahují na data při přesunu do Azure.

- Doporučujeme, aby více než jedno zařízení nepsalo do stejného kontejneru.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB nebo soubor), který má stejný název jako objekt, který se právě kopíruje, zařízení přepíše soubor v cloudu.
- Prázdná hierarchie adresářů (bez souborů) vytvořená v části sdílet složky není nahrána do kontejnerů objektů BLOB.
- Data můžete kopírovat pomocí přetažení pomocí Průzkumníka souborů nebo pomocí příkazového řádku. Pokud je agregovaná velikost kopírovaných souborů větší než 10 GB, doporučujeme použít program hromadného kopírování, například Robocopy nebo rsync. Nástroje hromadného kopírování zopakují operaci kopírování pro občasné chyby a zajištění vyšší odolnosti.
- Pokud sdílená složka přidružená k kontejneru úložiště Azure nahraje objekty blob, které neodpovídají typu objektů BLOB definovaných pro sdílenou složku v době vytváření, takové objekty BLOB se neaktualizují. Například na zařízení vytvoříte sdílenou složku objektů blob bloku. Přidružte sdílenou složku ke stávajícímu kontejneru cloudu, který má objekty blob stránky. Aktualizujte tuto sdílenou složku a Stáhněte soubory. Upravte některé obnovené soubory, které jsou už uložené jako objekty blob stránky v cloudu. Zobrazí se chyba nahrávání.
- Po vytvoření souboru ve sdílených složkách se přejmenování souboru nepodporuje.
- Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
- Pokud ke kopírování dat používáte rsync, pak není `rsync -a` možnost podporována.

