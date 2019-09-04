---
title: Microsoft Azure Data Box Disk požadavky na systém | Microsoft Docs
description: Seznamte se s požadavky na software a sítě pro vaše Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f054d0665c49ee048451681a7cc34d2a648a6e9e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240227"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk požadavky na systém

Tento článek popisuje důležité systémové požadavky pro vaše Microsoft Azure Data Box Disk řešení a klienty, kteří se připojují k Data Box Disk. Před nasazením Data Box Disk doporučujeme pečlivě zkontrolovat informace a pak je v případě potřeby v průběhu nasazení a následné operace vrátit na ni.

Požadavky na systém zahrnují podporované platformy pro klienty připojující se k diskům, podporované účty úložiště a typy úložišť.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Kontrola požadovaných součástí

1. K objednání data box disk v tomto [kurzu musíte použít: Seřazení Azure Data Box Disk](data-box-disk-deploy-ordered.md). Dostali jste disky a jeden připojený kabel na disk.
2. Máte k dispozici klientský počítač, ze kterého můžete kopírovat data. Klientský počítač musí splňovat tyto požadavky:

    - Spusťte podporovaný operační systém.
    - Máte nainstalovaný další požadovaný software.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

Tady je seznam podporovaných operačních systémů pro odemknutí disku a operace kopírování dat prostřednictvím klientů připojených k Data Box Disk.

| **Operační systém** | **Testované verze** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bitů) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Další požadovaný software pro klienty Windows

Pro klienta systému Windows je třeba nainstalovat i následující.

| **Software**| **Verze** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Další požadovaný software pro klienty se systémem Linux

Pro klienty se systémem Linux nainstaluje sada nástrojů Data Box Disk následující požadovaný software:

- Weblock
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Podporované připojení

Klientský počítač obsahující data musí mít port USB 3,0 nebo novější. Disky se připojují k tomuto klientovi pomocí zadaného kabelu.

## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Tady je seznam podporovaných typů úložiště pro Data Box Disk.

| **Účet úložiště** | **Poznámky** |
| --- | --- |
| Classic | Standard |
| Obecné použití  |Standardní jsou podporovány verze V1 a v2. Podporovány jsou horké a studené úrovně. |
| Účet úložiště objektů BLOB | |

>[!NOTE]
> Účty Azure Data Lake Storage Gen 2 nejsou podporovány.


## <a name="supported-storage-types-for-upload"></a>Podporované typy úložišť pro nahrání

Tady je seznam typů úložišť podporovaných pro nahrání do Azure pomocí Data Box Disk.

| **Formát souboru** | **Poznámky** |
| --- | --- |
| Objekt blob bloku Azure | |
| Objekt blob stránky Azure  | |
| Soubory Azure  | |
| Spravované disky | |


## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

