---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175462"
---
Následující omezení se vztahují na data při přesunu do Azure.

- Doporučujeme, aby více než jedno zařízení nepsalo do stejného kontejneru.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB nebo soubor), který má stejný název jako objekt, který se právě kopíruje, zařízení přepíše soubor v cloudu.
- Prázdná hierarchie adresářů (bez souborů) vytvořená v části sdílet složky není nahrána do kontejnerů objektů BLOB.
- Data můžete kopírovat pomocí přetažení pomocí Průzkumníka souborů nebo pomocí příkazového řádku. Pokud je agregovaná velikost kopírovaných souborů větší než 10 GB, doporučujeme použít program hromadného kopírování, například Robocopy nebo rsync. Nástroje hromadného kopírování zopakují operaci kopírování pro občasné chyby a zajištění vyšší odolnosti.
- Pokud sdílená složka přidružená k kontejneru úložiště Azure nahraje objekty blob, které neodpovídají typu objektů BLOB definovaných pro sdílenou složku v době vytváření, takové objekty BLOB se neaktualizují. Například na zařízení vytvoříte sdílenou složku objektů blob bloku. Přidružte sdílenou složku ke stávajícímu kontejneru cloudu, který má objekty blob stránky. Aktualizujte tuto sdílenou složku a Stáhněte soubory. Upravte některé obnovené soubory, které jsou už uložené jako objekty blob stránky v cloudu. Zobrazí se chyba nahrávání.
