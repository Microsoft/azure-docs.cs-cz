---
title: Obnovení kontejneru účtu úložiště
description: Obnovení kontejneru účtu úložiště
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693576"
---
# <a name="storage-account-container-recovery"></a>Obnovení kontejneru účtu úložiště

Azure Storage zajišťuje odolnost dat prostřednictvím automatizovaných replik. To však nebrání kódu aplikace nebo uživatelům v poškození dat, ať už omylem nebo škodlivě. Zachování věrnosti dat na tváři aplikace nebo chyby uživatele vyžaduje pokročilejší techniky, jako je například kopírování dat do sekundárního úložiště pomocí protokolu auditu.

## <a name="checking-azure-storage-account-type"></a>Kontrola typu účtu Azure Storage

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Vyhledejte svůj účet úložiště.

3. V části **Přehled** vyhledejte **replikaci**.

   ![Image](media/storage-account-container-recovery/1.png)

4. Pokud je typ replikace **GRS/RA-GRS**, je možné provést obnovení kontejneru účtů bez záruky. U všech ostatních typů replikace to není možné.

5. Shromážděte následující informace a Zažádejte si žádost o podporu s podpora Microsoftu.

   * Název účtu úložiště:
   * Název kontejneru:
   * Čas odstranění:

   V následující tabulce najdete přehled rozsahu obnovení kontejneru účtu úložiště v závislosti na strategii replikace.

   |Typ obsahu|LRS|ZRS|GRS|RA – GRS| 
   |---|---|---|---|---|
   |Kontejner úložiště|Ne|Ne|Ano|Ano| 

   * Můžeme se pokusit o obnovení kontejneru účtu úložiště, ale bez jakékoli záruky. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Věci, které není potřeba k úspěšnému obnovení

* Nevytvářejte prosím znovu kontejner (se stejným názvem).  
* Pokud jste kontejner již znovu vytvořili, je nutné jej odstranit před podáním žádosti o podporu pro obnovení.

## <a name="steps-to-prevent-this-in-the-future"></a>Postup, který zabrání v budoucnu

1. Aby k tomu nedocházelo v budoucnu, je doporučené použití funkce [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Doporučujeme také funkci [Snapshot](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) .
 
## <a name="next-steps"></a>Další kroky

Tady jsou dva ukázkové kódy pro funkci:

  * [Vytvoření a Správa snímku objektu BLOB v .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Použití snímků objektů BLOB v prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

