---
title: Azure skript prostředí PowerShell ukázkový – vypočítat celkové velikosti fakturace kontejner objektů blob | Microsoft Docs
description: Vypočítejte celková velikost kontejner v úložišti objektů Blob v Azure pro účely fakturace.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26368540"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Vypočítat celkové velikosti fakturace kontejner objektů blob

Tento skript vypočítá velikost kontejner v úložišti objektů Blob v Azure pro účely odhadování fakturace náklady. Skript celkový počet zpracovaných položek velikost objektů BLOB v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Tento skript prostředí PowerShell vypočítá velikost kontejner pro účely fakturace. Pokud jsou výpočet velikosti kontejneru pro jiné účely, přečtěte si téma [vypočítat celkové velikosti kontejner úložiště objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) jednodušší skript, který poskytuje odhad.

## <a name="determine-the-size-of-the-blob-container"></a>Určit velikost kontejneru objektů blob

Celková velikost kontejneru objektů blob obsahuje velikost kontejneru sám a velikost všech objektů BLOB v kontejneru.

Následující části popisuje, jak je vypočtena úložnou kapacitu pro kontejnery objektů blob a objekty BLOB. V následující části Len(X) znamená počet znaků v řetězci.

### <a name="blob-containers"></a>Kontejnery objektů BLOB

Následující výpočet popisuje, jak odhadnout velikost úložiště, který je na kontejner objektů blob:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Toto je rozdělení:
* 48 bajtů režie pro každý kontejner obsahuje čas poslední změny, oprávnění, nastavení veřejných a některá metadata systému.

* Název kontejneru je uložena ve formátu Unicode, takže provést počet znaků a zdvojnásobte.

* Pro každý blok metadata kontejneru objektu blob, který je uložený uložíme délka názvu (ASCII), plus délka hodnotu řetězce.

* 512 bajtů na podepsané identifikátor zahrnuje název podepsaný identifikátoru, čas zahájení, čas vypršení platnosti a oprávnění.

### <a name="blobs"></a>Objekty blob

Tyto výpočty ukazují, jak odhadnout velikost úložiště na objekt blob.

* Objekt blob bloku (základní objekt blob nebo snímek):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Objekt blob stránky (základní objekt blob nebo snímek):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Toto je rozdělení:

* 124 bajtů režie pro objekt blob, která zahrnuje:
    - Čas poslední změny
    - Velikost
    - Cache-Control
    - Content-Type
    - Obsah – jazyk
    - Kódování obsahu
    - Obsah MD5
    - Oprávnění
    - Informace o snímku
    - Zapůjčení
    - Některá metadata systému

* Název objektu blob je uložena ve formátu Unicode, takže provést počet znaků a zdvojnásobte.

* Pro každý blok metadat, která je uložená přidejte délka názvu (uložené ve formátu ASCII), plus délka hodnotu řetězce.

* Pro objekty BLOB bloku:
    * 8 bajtů pro do seznamu zakázaných položek.
    * Počet bloků časy velikost bloku ID v bajtech.
    * Velikost dat ve všech potvrdit a nepotvrzené bloky. 
    
    >[!NOTE]
    >Pokud použijete snímky, tato velikost obsahuje pouze jedinečné data pro tento objekt blob základní nebo snímek. Pokud se po týdnu se nepoužívají nepotvrzené bloky, jsou uvolňování paměti. Potom nemáte započítává k fakturaci.

* Pro objekty BLOB stránky:
    * Počet rozsahů nejdou stránek s daty případech 12 bajtů. Toto je počet rozsahů jedinečný stránek se zobrazí při volání metody **GetPageRanges** rozhraní API.

    * Velikost dat v bajtech všechny uložené stránky. 
    
    >[!NOTE]
    >Pokud použijete snímky, tato velikost obsahuje pouze jedinečné stránek pro základní objekt blob nebo objekt blob snímek, který se počítá.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Další kroky

- V tématu [vypočítat celkové velikosti kontejner úložiště objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) jednoduchý skript, který poskytuje odhad velikosti kontejneru.

- Další informace o fakturaci Azure Storage najdete v tématu [Principy Windows Azure Storage fakturace](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- Můžete najít další ukázky skriptu PowerShell úložiště v [ukázky prostředí PowerShell pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).
