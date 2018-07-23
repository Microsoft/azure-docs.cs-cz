---
title: Požadavky pro službu Azure Import/Export | Dokumentace Microsoftu
description: Vysvětlení softwarové a hardwarové požadavky pro službu Azure Import/Export.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: alkohli
ms.openlocfilehash: 68e31f6b88a772ad67e3c58e11925f46f1cc37e9
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188803"
---
# <a name="azure-importexport-system-requirements"></a>Požadavky na systém Azure Import/Export

Tento článek popisuje důležité požadavky pro vaši službu Azure Import/Export. Doporučujeme, abyste si informace o pečlivě před používat službu Import/Export a poté vraťte se k němu podle potřeby během operace.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Pro přípravu pevných disků pomocí nástroje WAImportExport následující **64bitová verze operačního systému, které podporují nástroj BitLocker Drive Encryption** jsou podporovány.


|Platforma |Verze |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Služba Import/Export Azure podporuje následující účty úložiště Azure.
- Classic
- Účty služby Blob Storage
- Obecné účty úložiště účely v1. 

Každá úloha slouží k přenosu dat do nebo z pouze jeden účet úložiště. Jinými slovy úlohu importu/exportu jedné nemůžou zahrnovat napříč několika účty úložiště. Informace o vytvoření nového účtu úložiště najdete v tématu [způsob vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Služba Azure Import Export nepodporuje účty úložiště ve kterém [koncové body služeb virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md) se povolila funkce. 

## <a name="supported-storage-types"></a>Typy podporovaných úložišť

Následující seznam typů úložiště podporuje služba Azure Import/Export.


|Úloha  |Úložiště  |Podporováno  |Nepodporuje se  |
|---------|---------|---------|---------|
|Import     |  Azure Blob storage. <br>Objekty BLOB bloku, objekty BLOB stránky, které jsou podporovány. <br> Služba soubory Azure nepodporuje.       |         |
|Export     |   Azure Blob storage. <br>Objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB, nepodporuje.       | Služba soubory Azure nejsou podporovány.        |


## <a name="supported-hardware"></a>Podporovaný hardware 

Pro službu Azure Import/Export potřebujete podporované disky kopírovat data.

### <a name="supported-disks"></a>Podporované disky

Následující seznam disků se podporuje pro použití se službou Import/Export.


|Typ disku  |Velikost  |Podporováno |Nepodporuje se  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |         |         |
|HDD     |  2,5"<br>3,5"       |II SATA, SATA III         |Externí pevný disk s integrovanou adaptéru USB <br> Disk v použití malých a velkých externí pevný disk         |


Úloha importu/exportu jedné může mít:
- Maximálně 10 pevný disk nebo disky SSD.
- Kombinace pevný disk nebo SSD libovolné velikosti.

Velký počet jednotek, lze distribuovat mezi několika úlohami a neexistuje žádné omezení počtu úloh, které je možné vytvořit. Pro úlohy importu se zpracuje pouze první datový svazek na disku. Objem dat musí být naformátovaná za použití systému souborů NTFS.

Když příprava pevných disků a kopírování dat pomocí nástroje WAImportExport, můžete použít externí adaptéry USB. Většina předem připravená USB 3.0 nebo novější adaptéry by měla fungovat. 


## <a name="next-steps"></a>Další postup

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázkový Import exportovat rozhraní REST API služby Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

