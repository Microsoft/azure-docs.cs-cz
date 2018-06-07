---
title: Požadavky pro službu Azure Import/Export | Microsoft Docs
description: Pochopení softwarové a hardwarové požadavky pro službu Azure Import/Export.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/14/2018
ms.author: alkohli
ms.openlocfilehash: eb50846bd838597a6f1e9a0b6550595866e05edc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660745"
---
# <a name="azure-importexport-system-requirements"></a>Požadavky na systém Azure Import/Export

Tento článek popisuje požadavky na důležité pro vaši službu Azure Import/Export. Doporučujeme, abyste si prošli informace pečlivě než začnete používat službu Import/Export a pak zpátky na ni odkazuje podle potřeby během operace.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Příprava pevných disků pomocí nástroje WAImportExport následující **64bitová verze operačního systému, které podporují nástroj BitLocker Drive Encryption** jsou podporovány.


|Platforma |Verze |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>Účty úložiště podporované

Služba Azure Import/Export podporuje následující účty úložiště Azure.
- Classic
- Účty služby Blob Storage
- Účty úložiště v1 obecné účely. 

Každá úloha může použít k přenosu dat do nebo z jenom jeden účet úložiště. Jinými slovy úlohu jeden importu a exportu nelze rozmístěny napříč více účtů úložiště. Informace o vytvoření nového účtu úložiště najdete v tématu [postup vytvoření účtu úložiště](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Služba Azure Import Export nepodporuje účty úložiště kde [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md) funkce povolena. 

## <a name="supported-storage-types"></a>Typy podporované úložiště

Následující seznam typů úložiště je podporována službou Azure Import/Export.


|Úloha  |Úložiště  |Podporováno  |Nepodporuje se  |
|---------|---------|---------|---------|
|Import     |  Úložiště objektů Blob Azure. <br>Objekty BLOB bloku, objekty BLOB stránky podporovány. <br> Soubory Azure podporován.       |         |
|Export     |   Úložiště objektů Blob Azure. <br>Objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB podporována.       | Soubory Azure nejsou podporovány.        |


## <a name="supported-hardware"></a>Podporovaný hardware 

Pro službu Azure Import/Export potřebovat podporované disky a podporované konektory SATA zkopírovat data.

### <a name="supported-disks"></a>Podporované disky

Následující seznam disků je podporována pro použití se službou importu a exportu.


|Typ disku  |Velikost  |Podporováno |Nepodporuje se  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |         |         |
|HDD     |  2,5"<br>3,5"       |SATA II SATA III         |Externí pevný disk s integrovanou adaptéru USB <br> Disk v malá a velká písmena externí pevný disk         |


Může mít jeden importu a exportu úlohy:
- Maximálně 10 pevný disk nebo disky SSD.
- Směs HDD/SSD jakékoli velikosti.

Velký počet jednotek možné rozdělit do více úloh a neexistuje žádná omezení na počet úloh, které lze vytvořit. 

Pro import úlohy jsou zpracovávány pouze první datový svazek na disku. Datový svazek musí být formátován pomocí systému souborů NTFS.

### <a name="supported-external-usb-adaptors"></a>Podporované externí adaptéry USB

Tady je seznam externích adaptéry USB použít ke zkopírování dat do interní pevné disky. 
- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Orico 6628SUS3-C-černá (6628 řada)
- Thermaltake BlacX odkládacího horká SATA externí pevné jednotky ukotvení stanice (USB 2.0 & eSATA)


## <a name="next-steps"></a>Další postup

* [Nastavit nástroj WAImportExport](storage-import-export-tool-how-to.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Ukázka Azure Import, Export REST API](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

