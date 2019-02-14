---
title: Skript Azure Powershellu ukázkový – výpočet fakturace celkové velikosti kontejneru objektů blob | Dokumentace Microsoftu
description: Vypočítejte celkovou velikost kontejneru v úložišti objektů Blob v Azure pro účely fakturace.
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
ms.openlocfilehash: f1e905b0b67048a10f6eb455d77275375a99dbd0
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245401"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Výpočet fakturace celkové velikosti kontejneru objektů blob

Tento skript vypočítá velikost kontejneru v úložišti objektů Blob v Azure pro účely odhad fakturačních nákladů. Skript sečte velikost objektů BLOB v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Tento skript Powershellu vypočítá velikost kontejneru pro účely fakturace. Pokud se výpočet velikosti kontejneru pro jiné účely, přečtěte si téma [vypočítat celkové velikosti úložiště kontejner objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) jednodušší skript, který poskytuje odhad.

## <a name="determine-the-size-of-the-blob-container"></a>Určení velikosti kontejneru objektů blob

Celková velikost kontejneru objektů blob zahrnuje velikost kontejner sám o sobě a velikost všech objektů BLOB v kontejneru.

V dalších částech se popisuje, jak se počítá kapacita úložiště pro kontejnery objektů blob a objekty BLOB. V následující části Len(X) znamená, že počet znaků v řetězci.

### <a name="blob-containers"></a>Kontejnery objektů BLOB

Následující výpočet popisuje, jak odhadnout objem úložiště za kontejner objektů blob:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Tady je rozpis:
* aspoň 48 bajtů režie pro každý kontejner obsahuje čas poslední změny, oprávnění, nastavení veřejné a některá metadata systému.

* Název kontejneru je uložena ve formátu Unicode, takže trvat počet znaků a zdvojnásobte.

* Pro každý blok metadat objektu blob kontejneru, který je uložený uložíme délka názvu (ASCII), a navíc délka řetězcové hodnoty.

* 512 bajtů na podepsané identifikátor obsahuje název identifikátoru podepsaný držitelem, čas zahájení, čas vypršení platnosti a oprávnění.

### <a name="blobs"></a>Objekty blob

Tyto výpočty ukazují, jak odhadnout objem úložiště spotřebované u jednotlivých objektů blob.

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

Tady je rozpis:

* 124 bajty režie pro objekt blob, který obsahuje:
    - Čas poslední úpravy
    - Velikost
    - Cache-Control
    - Typ obsahu
    - Jazyk obsahu
    - Kódování obsahu
    - Content-MD5
    - Oprávnění
    - Informace o snímku
    - Zapůjčení
    - Některá metadata systému

* Název objektu blob se ukládá jako kódování Unicode, takže trvat počet znaků a zdvojnásobte.

* Pro každý blok metadat, která je uložena přidáte délka názvu (uložené ve formátu ASCII), a navíc délka řetězcové hodnoty.

* Pro objekty BLOB bloku:
    * 8 bajtů pro do seznamu zakázaných položek.
    * Počet bloků časový limit velikosti bloku ID v bajtech.
    * Velikost dat ve všech bloků nepotvrzené a potvrzené.

    >[!NOTE]
    >Zadáním snímky tato velikost obsahuje pouze jedinečné data pro tento základní nebo snímek objektu blob. Pokud po týdnu nejsou použity nepotvrzené bloky, jsou uvolnění paměti. Po tomto není počet směrem k fakturaci.

* Pro objekty BLOB stránky:
    * Počet rozsahů nejdou stránky s daty vyprší 12 bajtů. Toto je počet jedinečných rozsahů se zobrazí při volání **GetPageRanges** rozhraní API.

    * Velikost dat v bajtech všech uložených stránek.

    >[!NOTE]
    >Zadáním snímky tato velikost obsahuje pouze jedinečné stránek pro základní objekt blob nebo pořízení snímku objektu blob, který se právě počítá.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Další postup

- Zobrazit [vypočítat celkové velikosti úložiště kontejner objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) jednoduchý skript, který poskytuje odhad velikosti kontejneru.

- Další informace o fakturaci Azure Storage najdete v tématu [Principy Windows Azure Storage fakturace](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Další informace o modulu Azure Powershellu najdete v tématu [dokumentaci k Azure Powershellu](https://docs.microsoft.com/powershell/azure/overview).

- Můžete najít další ukázkové skripty Powershellu pro úložiště v [ukázky Powershellu pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).
