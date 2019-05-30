---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244589"
---
- Nemusíte kopírovat soubory přímo do všech vytvořených sdílené složky. Budete muset vytvořit složku ve sdílené složce a zkopírujte soubory do této složky.
- Složku ve složce *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *StorageAccount_BlockBlob/kontejneru* a *StorageAccount_PageBlob/kontejneru*.
- Všechny složky, vytvořené přímo v rámci *StorageAccount_AzureFiles* přeloženy do sdílené složky Azure.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení Data Box přepsat soubor v cloudu.
- Každý soubor zapsán do *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* sdílené složky se nahraje jako objekt blob bloku a objektů blob stránky.
- Úložiště objektů blob v Azure nepodporuje adresáře. Pokud vytváříte složku ve složce *StorageAccount_BlockBlob* složku a pak virtuální složky se vytvoří v názvu objektu blob. Pro soubory Azure je udržován skutečné adresářovou strukturu.
- Všechny prázdné hierarchii adresářů (bez jakékoli soubory) vytvořené v rámci *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* složky nebylo odesláno.
- Pokud nejsou žádné chyby při odesílání dat do Azure, vytvoří se protokol chyb v účtu cílového úložiště. Cesta k této chybě protokolu je k dispozici, když se nahrávání dokončí, a vy můžete zkontrolovat protokol k provedení nápravné akce. Neodstraňovat data ze zdroje bez ověření odesílaná data.
