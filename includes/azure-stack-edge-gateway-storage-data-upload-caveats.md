---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466756"
---
Následující omezení se vztahují na data při přesunu do Azure.

- Doporučujeme, aby více než jedno zařízení nepsalo do stejného kontejneru.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB nebo soubor), který má stejný název jako objekt, který se právě kopíruje, zařízení přepíše soubor v cloudu.
- Prázdná hierarchie adresářů (bez souborů) vytvořená v části sdílet složky není nahrána do kontejnerů objektů BLOB.
- Data můžete kopírovat přetažením v Průzkumníku souborů nebo přes příkazový řádek. Pokud je agregovaná velikost kopírovaných souborů větší než 10 GB, doporučujeme použít program hromadného kopírování, jako je Robocopy nebo rsync. Nástroje hromadného kopírování v případě přechodných chyb opakují operaci kopírování a zajišťují vyšší odolnost. Pokud používáte úložiště objektů BLOB prostřednictvím REST, AzCopy nebo Průzkumník služby Azure Storage lze použít.
- Pokud sdílená složka přidružená k kontejneru úložiště Azure nahraje objekty blob, které neodpovídají typu objektů BLOB definovaných pro sdílenou složku v době vytváření, takové objekty BLOB se neaktualizují. Příklad: Vytvoříte na zařízení sdílenou složku objektů blob bloku. Přidružíte sdílenou složku k existujícímu cloudovému kontejneru, který obsahuje objekty blob stránky. Aktualizujete sdílenou složku, aby se stáhly soubory. Upravíte některé z aktualizovaných souborů, které jsou v cloudu již uložené jako objekty blob stránky. Při nahrávání dojde k selhání.
- Po vytvoření souboru ve sdílených složkách se přejmenování souboru nepodporuje.
- Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
- Pokud ke kopírování dat používáte rsync, pak není `rsync -a` možnost podporována.
