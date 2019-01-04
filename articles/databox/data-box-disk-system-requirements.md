---
title: Požadavky na systém Microsoft Azure Data Box Disk | Dokumentace Microsoftu
description: Další informace o softwaru a požadavky na síť pro váš Disk Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: 5debc14a6a20c42b62b9a7b2c524e36e94302221
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792862"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Azure Data Box Disk požadavky na systém (Preview)

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box Disk a pro připojení klientů k disku Data Box. Doporučujeme, abyste si informace o pečlivě před nasadit váš Disk Data Box a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

> [!IMPORTANT]
> Data Box Disk je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Požadavky na systém zahrnují podporovaných platforem pro připojení klientů k disky úložiště podporuje účty a typy úložišť.


## <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

Tady je seznam podporovaných operačních systémech pro disk odemknutí a operace kopírování dat prostřednictvím klienty připojené k disku Data Box.

| **Operační systém** | **Otestované verze** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Jiného požadovaného softwaru pro klienty Windows

Pro klienta Windows musí také nainstalovaná následující.

| **Software**| **Verze** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Jiného požadovaného softwaru pro klienty Linux

Sada nástrojů disku Data Box pro klienta pro Linux, nainstaluje následující požadovaný software:

- dislocker
- OpenSSL

## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Tady je seznam typů podporovaných úložiště pro Data Box Disk.

| **Účet úložiště** | **Poznámky** |
| --- | --- |
| Classic | Standard |
| Obecné použití  |Standardní; verze V1 i V2 podporují. Vrstvy hot a cool nejsou podporovány. |

>[!NOTE]
> Účty Azure Data Lake Storage generace 2 nejsou podporovány.


## <a name="supported-storage-types"></a>Typy podporovaných úložišť

Tady je seznam typů podporovaných úložiště pro Data Box Disk.

| **Formát souboru** | **Poznámky** |
| --- | --- |
| Objekt blob bloku Azure | |
| Objekt blob stránky Azure  | |


## <a name="next-step"></a>Další krok

* [Nasadit váš Disk Azure Data Box](data-box-disk-deploy-ordered.md)

