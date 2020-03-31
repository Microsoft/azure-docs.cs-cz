---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73799739"
---
- Nekopírujte soubory přímo do žádné z předem vytvořených sdílených složek. Je třeba vytvořit složku pod sdílenou složkou a potom do ní zkopírovat soubory.
- Složka pod *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* je kontejner. Kontejnery jsou například vytvořeny jako *StorageAccount_BlockBlob/kontejner* a *StorageAccount_PageBlob/kontejner*.
- Každá složka vytvořená přímo pod *StorageAccount_AzureFiles* se přeloží do sdílené složky Azure.
- Pokud máte existující objekt Azure (například objekt blob nebo soubor) v cloudu se stejným názvem jako objekt, který se kopíruje, Data Box přepíše soubor v cloudu.
- Každý soubor napsaný do *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* sdílených složek se nahraje jako objekt blob bloku a objekt blob stránky.
- Azure blob storage nepodporuje adresáře. Pokud vytvoříte složku pod *StorageAccount_BlockBlob* složky, virtuální složky se vytvoří v názvu objektu blob. Pro soubory Azure se udržuje skutečná adresářová struktura.
- Žádná prázdná hierarchie adresářů (bez souborů) vytvořená pod *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* složky se nenahraje.
- Pokud se při nahrávání dat do Azure zobrazí nějaké chyby, vytvoří se v cílovém účtu úložiště protokol chyb. Cesta k tomuto protokolu chyb je k dispozici po dokončení nahrávání a můžete zkontrolovat protokol provést nápravná opatření. Neodstraňujte data ze zdroje bez ověření nahraných dat.
- Metadata souborů a oprávnění ntfs nejsou zachovány při odeslání dat do souborů Azure. Například *poslední změněný atribut* souborů nebude při kopírování dat zachován.
