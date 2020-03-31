---
title: Požadavky na službu Azure Import/Export | Dokumenty společnosti Microsoft
description: Seznamte se s požadavky na software a hardware pro službu Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255311"
---
# <a name="azure-importexport-system-requirements"></a>Požadavky na systém Azure Import/Export

Tento článek popisuje důležité požadavky pro službu Azure Import/Export. Doporučujeme, abyste si tyto informace před použitím služby Import/Export pečlivě prostudovali a poté se k ní vrátili podle potřeby během operace.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Při přípravě pevných disků pomocí nástroje WAImportExport je podporován následující **64bitový operační systém, který podporuje šifrování bitlockerů.**


|Platforma |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Další požadovaný software pro klienta systému Windows

|Platforma |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Podporované účty úložiště

Služba Azure Import/Export podporuje následující typy účtů úložiště:

- Standardní účty úložiště pro obecné účely v2 (doporučeno pro většinu scénářů)
- Účty služby Blob Storage
- Účty úložiště pro obecné účely v1 (klasická nasazení nebo nasazení Azure Resource Manageru),

Další informace o účtech úložiště najdete v tématu [Přehled účtů úložiště Azure](storage-account-overview.md).

Každá úloha může být použita k přenosu dat do nebo pouze z jednoho účtu úložiště. Jinými slovy, jedna úloha importu a exportu nemůže přesáhnout více účtů úložiště. Informace o vytvoření nového účtu úložiště najdete v [tématu Jak vytvořit účet úložiště](storage-account-create.md).

> [!IMPORTANT]
> Služba Export importu Azure nepodporuje účty úložiště, kde byla povolena funkce koncové  [body služby Virtuální síť.](../../virtual-network/virtual-network-service-endpoints-overview.md) 

## <a name="supported-storage-types"></a>Podporované typy úložiště

Následující seznam typů úložišť je podporován službou Azure Import/Export.


|Úloha  |Služba úložiště |Podporuje se  |Nepodporuje se  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Azure File Storage       | Podporované objekty BLOB bloku a objekty BLOB stránky <br><br> Podporované soubory          |
|Export     |   Azure Blob Storage       | Blokové objekty BLOB, objekty BLOB stránky a podporované objekty BLOB aplikace Append         | Soubory Azure nejsou podporované


## <a name="supported-hardware"></a>Podporovaný hardware

Pro službu Import a export Azure potřebujete ke kopírování dat podporované disky.

### <a name="supported-disks"></a>Podporované disky

Následující seznam disků je podporován pro použití se službou Import/Export.


|Typ disku  |Velikost  |Podporuje se |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

Následující typy disků nejsou podporovány:
- USBs.
- Externí pevný disk s vestavěným USB adaptérem.
- Disky, které jsou uvnitř krytu externího pevného disku.

Jedna úloha importu a exportu může mít:
- Maximálně 10 HDD /SSD.
- Kombinace HDD / SSD libovolné velikosti.

Velký počet jednotek lze rozložit na více úloh a neexistuje žádné omezení počtu úloh, které lze vytvořit. U úloh importu je zpracován pouze první datový svazek na jednotce. Datový svazek musí být formátován pomocí systému souborů NTFS.

Při přípravě pevných disků a kopírování dat pomocí nástroje WAImportExport můžete použít externí adaptéry USB. Většina běžně dostupných adaptérů USB 3.0 nebo novějších by měla fungovat.


## <a name="next-steps"></a>Další kroky

* [Nastavení nástroje WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka rozhraní REST exportu azure importu](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
