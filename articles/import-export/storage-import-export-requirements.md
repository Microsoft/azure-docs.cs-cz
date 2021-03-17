---
title: Požadavky na službu Azure import/export | Microsoft Docs
description: Seznamte se s požadavky na software a hardware pro službu Azure import/export.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712602"
---
# <a name="azure-importexport-system-requirements"></a>Požadavky na systém Azure Import/Export

Tento článek popisuje důležité požadavky na službu Azure import/export. Doporučujeme pečlivě zkontrolovat informace, než použijete službu import/export, a pak se na ni v případě potřeby v průběhu této operace znovu odkazuje.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Pro přípravu pevných disků pomocí nástroje WAImportExport jsou podporovány následující **64 operační systémy, které podporují nástroj BitLocker Drive Encryption** .


|Platforma |Verze |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 pro, Windows 8 Enterprise, Windows 8.1 pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Další požadovaný software pro klienta Windows

|Platforma |Verze |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Podporované účty úložiště

Služba import/export Azure podporuje následující typy účtů úložiště:

- Účty úložiště Standard Pro obecné účely v2 (doporučeno pro většinu scénářů)
- Účty služby Blob Storage
- Účty úložiště Pro obecné účely V1 (nasazení Classic nebo Azure Resource Manager)

> [!IMPORTANT]
> Podpora protokolů systému souborů NFS (Network File System) 3,0 ve službě Azure Blob Storage není podporovaná pro Azure import/export.

Další informace o účtech úložiště najdete v tématu [Přehled účtů Azure Storage](../storage/common/storage-account-overview.md).

Každou úlohu můžete použít k přenosu dat do nebo z jednoho účtu úložiště. Jinými slovy, jedna úloha importu/exportu nemůže být rozložená mezi několik účtů úložiště. Informace o vytvoření nového účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

> [!IMPORTANT]
> U účtů úložiště, kde je povolená funkce [koncových bodů služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) , použijte nastavení **Povolit důvěryhodné služby společnosti Microsoft...** , aby služba [Import/export](../storage/common/storage-network-security.md) mohla provádět import/export dat do a z Azure.

## <a name="supported-storage-types"></a>Podporované typy úložiště

Služba Azure import/export podporuje následující seznam typů úložišť.


|Úloha  |Služba úložiště |Podporováno  |Nepodporováno  |
|---------|---------|---------|---------|
|Import     |  Azure Blob Storage <br><br> Azure File Storage       | Podporované objekty blob bloku a objekty blob stránky <br><br> Podporované soubory          |
|Export     |   Azure Blob Storage       | Podporované objekty blob bloku, objekty blob stránky a doplňovací objekty blob         | Soubory Azure nejsou podporované


## <a name="supported-hardware"></a>Podporovaný hardware

Pro službu Azure import/export potřebujete Podporované disky pro kopírování dat.

### <a name="supported-disks"></a>Podporované disky

Následující seznam disků je podporován pro použití se službou Import/export.


|Typ disku  |Velikost  |Podporováno |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          |
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

Následující typy disků nejsou podporovány:

- USBs.
- Externí HDD s integrovaným adaptérem USB
- Disky, které se nacházejí v malých a velkých jednotkách externího pevného disku.

Jedna úloha importu/exportu může mít tyto:

- Maximálně 10 HDD/SSD.
- Kombinace HDD/SSD libovolné velikosti.

Velký počet jednotek může být rozložen mezi více úloh a neexistuje žádné omezení počtu úloh, které lze vytvořit. Pro úlohy importu se zpracovává jenom první datový svazek na disku. Datový svazek musí být naformátován systémem souborů NTFS.

Když připravujete pevné disky a kopírujete data pomocí nástroje WAImportExport, můžete použít externí adaptéry USB. Většina adaptérů USB 3,0 nebo vyšší musí být funkční.

## <a name="next-steps"></a>Další kroky

* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../storage/common/storage-use-azcopy-v10.md)