---
title: Požadavky pro službu Azure Import/Export | Dokumentace Microsoftu
description: Vysvětlení softwarové a hardwarové požadavky pro službu Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360929"
---
# <a name="azure-importexport-system-requirements"></a>Požadavky na systém Azure Import/Export

Tento článek popisuje důležité požadavky pro vaši službu Azure Import/Export. Doporučujeme, abyste si informace o pečlivě před používat službu Import/Export a poté vraťte se k němu podle potřeby během operace.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Pro přípravu pevných disků pomocí nástroje WAImportExport jsou podporovány následující **64 operační systémy, které podporují nástroj BitLocker Drive Encryption** .


|Platforma |Verze |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Jiného požadovaného softwaru pro klienta Windows

|Platforma |Verze |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  \_          |


## <a name="supported-storage-accounts"></a>Podporované účty úložiště

Služba Import/Export Azure podporuje následující typy účtů úložiště:

- Účty úložiště Standard Pro obecné účely v2 (doporučeno pro většinu scénářů)
- Účty služby Blob Storage
- Účty úložiště Pro obecné účely V1 (nasazení Classic nebo Azure Resource Manager),

Další informace o účtech úložiště najdete v tématu [Přehled účtů Azure Storage](storage-account-overview.md).

Každá úloha slouží k přenosu dat do nebo z pouze jeden účet úložiště. Jinými slovy úlohu importu/exportu jedné nemůžou zahrnovat napříč několika účty úložiště. Informace o vytvoření nového účtu úložiště najdete v tématu [Vytvoření účtu úložiště](storage-account-create.md).

> [!IMPORTANT]
> Služba Azure import export nepodporuje účty úložiště, ve kterých je povolená funkce [koncové body služby Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md) . 

## <a name="supported-storage-types"></a>Typy podporovaných úložišť

Následující seznam typů úložiště podporuje služba Azure Import/Export.


|Úloha  |Služba Storage |Podporuje se  |Nepodporuje se  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Azure File storage       | Objekty BLOB bloku a stránku objekty BLOB podporována <br><br> Podporované soubory          |
|Export     |   Azure Blob Storage       | Objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB, nepodporuje         | Nepodporuje soubory Azure


## <a name="supported-hardware"></a>Podporovaný hardware

Pro službu Azure Import/Export potřebujete podporované disky kopírovat data.

### <a name="supported-disks"></a>Podporované disky

Následující seznam disků se podporuje pro použití se službou Import/Export.


|Typ disku  |Velikost  |Podporuje se |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3,5"       |II SATA, SATA III         |

Následující typy disků nejsou podporovány:
- USBs.
- Externí HDD s integrovaným adaptérem USB
- Disky, které se nacházejí v malých a velkých jednotkách externího pevného disku.

Úloha importu/exportu jedné může mít:
- Maximálně 10 pevný disk nebo disky SSD.
- Kombinace pevný disk nebo SSD libovolné velikosti.

Velký počet jednotek, lze distribuovat mezi několika úlohami a neexistuje žádné omezení počtu úloh, které je možné vytvořit. Pro úlohy importu se zpracuje pouze první datový svazek na disku. Objem dat musí být naformátovaná za použití systému souborů NTFS.

Když příprava pevných disků a kopírování dat pomocí nástroje WAImportExport, můžete použít externí adaptéry USB. Většina předem připravená USB 3.0 nebo novější adaptéry by měla fungovat.


## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka REST API exportu pro import do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
