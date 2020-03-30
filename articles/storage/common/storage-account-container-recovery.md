---
title: Obnovení kontejneru účtu úložiště
description: Obnovení kontejneru účtu úložiště
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562271"
---
# <a name="storage-account-container-recovery"></a>Obnovení kontejneru účtu úložiště

Azure Storage poskytuje odolnost proti chybám dat prostřednictvím automatizovaných replik. To však nebrání kódu aplikace nebo uživatelům v poškození dat, ať už náhodně nebo úmyslně. Zachování věrnosti dat tváří v tvář chybě aplikace nebo uživatele vyžaduje pokročilejší techniky, jako je například kopírování dat do sekundárního úložiště pomocí protokolu auditu.

## <a name="checking-azure-storage-account-type"></a>Kontrola typu účtu úložiště Azure

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Vyhledejte svůj účet úložiště.

3. V části **Přehled** zkontrolujte **replikaci**.

   ![Image](media/storage-account-container-recovery/1.png)

4. Pokud je typ replikace **GRS/RA-GRS**, obnovení kontejneru účtu je možné bez záruky. Pro všechny ostatní typy replikace není možné.

5. Shromážděte následující informace a podejte žádost o podporu pomocí podpory společnosti Microsoft.

   * Název účtu úložiště:
   * Název kontejneru:
   * Čas smazání:

   Následující tabulka obsahuje přehled rozsahu obnovení kontejneru účtu úložiště v závislosti na strategii replikace.

   |Typ obsahu|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Kontejner úložiště|Ne|Ne|Ano|Ano| 

   * Můžeme se pokusit obnovit kontejner účtu úložiště, ale bez záruky. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Co nedělat, aby se zotavil, aby byl úspěšný

* Nevytvářejte kontejner znovu (se stejným názvem).  
* Pokud jste již znovu vytvořili kontejner, je třeba odstranit kontejner před podáním žádosti o podporu pro obnovení.

## <a name="steps-to-prevent-this-in-the-future"></a>Kroky k tomu, aby se tomu v budoucnu zabránilo

1. Chcete-li tomu v budoucnu zabránit, je nejdoporučovanější funkcí, kterou je třeba [použít, funkce Obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Doporučujeme také [funkci Snímek.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Další kroky

Zde jsou dva ukázkové kódy na funkci:

  * [Vytvoření a správa snímku objektu blob v rozhraní .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Použití snímků objektů blob s Prostředím PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

