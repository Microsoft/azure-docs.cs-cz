---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "73799739"
---
- Nekopírujte soubory přímo do žádné z předvytvořených sdílených složek. Je nutné vytvořit složku ve sdílené složce a následně zkopírovat soubory do této složky.
- Složka pod *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *StorageAccount_BlockBlob/Container* a *StorageAccount_PageBlob/Container*.
- Každá složka vytvořená přímo v rámci *StorageAccount_AzureFiles* je přeložena do sdílené složky Azure.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB nebo soubor), který má stejný název jako objekt, který se právě kopíruje, Data Box přepíše soubor v cloudu.
- Každý soubor zapsaný do *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* sdílené složky se nahrají jako objekt blob bloku a objekt blob stránky.
- Úložiště objektů BLOB v Azure nepodporuje adresáře. Pokud vytvoříte složku ve složce *StorageAccount_BlockBlob* , vytvoří se v názvu objektu BLOB virtuální složky. Pro soubory Azure se zachová skutečná adresářová struktura.
- Žádná prázdná hierarchie adresářů (bez jakýchkoli souborů) vytvořená v části *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* složky se neodešlou.
- Pokud při nahrávání dat do Azure dojde k nějakým chybám, v cílovém účtu úložiště se vytvoří protokol chyb. Cesta k tomuto protokolu chyb je k dispozici po dokončení nahrávání a můžete si prohlédnout protokol a provést nápravné akce. Neodstraňujte data ze zdroje bez ověřování nahraných dat.
- Metadata souboru a oprávnění systému souborů NTFS se nezachovají při nahrávání dat do souborů Azure. Například *atribut naposledy upravený* soubory se nebude uchovávat při kopírování dat.
