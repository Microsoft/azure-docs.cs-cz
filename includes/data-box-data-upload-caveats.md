---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91641111"
---
- Nekopírujte soubory přímo do žádné z předvytvořených sdílených složek. Je nutné vytvořit složku ve sdílené složce a následně zkopírovat soubory do této složky.
- Složka pod *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *StorageAccount_BlockBlob/Container* a *StorageAccount_PageBlob/Container*.
- Každá složka vytvořená přímo v rámci *StorageAccount_AzureFiles* je přeložena do sdílené složky Azure.
- Pokud má kopírovaný objekt stejný název jako objekt Azure, jako je například objekt BLOB nebo soubor, který je již v cloudu, Data Box přepíše soubor v cloudu.
- Každý soubor zapsaný do *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* sdílené složky se nahrají jako objekt blob bloku a objekt blob stránky.
- Úložiště objektů BLOB v Azure nepodporuje adresáře. Pokud vytvoříte složku ve složce *StorageAccount_BlockBlob* , vytvoří se v názvu objektu BLOB virtuální složky. Pro soubory Azure se zachová skutečná adresářová struktura.
- Žádná prázdná hierarchie adresářů (bez jakýchkoli souborů) vytvořená v části *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* složky se neodešlou.
- Pokud při nahrávání dat do Azure dojde k nějakým chybám, v cílovém účtu úložiště se vytvoří protokol chyb. Cesta k tomuto protokolu chyb je k dispozici po dokončení nahrávání a můžete si prohlédnout protokol a provést nápravné akce. Neodstraňujte data ze zdroje bez ověřování nahraných dat.
- Metadata souboru a oprávnění NTFS se můžou zachovat, když se data nahrají do souborů Azure, a to pomocí pokynů v části [zachování seznamů ACL souborů, atributů a časových razítek pomocí Azure Data box](../articles/databox/data-box-file-acls-preservation.md).