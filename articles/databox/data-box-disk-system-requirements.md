---
title: Systémové požadavky Microsoft Azure Data Box Disku | Microsoft Docs
description: Seznamte se s požadavky vašeho Azure Data Box Disku na software a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fb2fd89664517e44cf5128a5c82e583f03087061
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307683"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Systémové požadavky Azure Data Box Disku

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box Disk a pro klienty připojující se k Data Box Disku. Před nasazením Data Box Disku doporučujeme, abyste si tyto informace pečlivě prostudovali. Později se k nim můžete podle potřeby vrátit během nasazování a dalšího provozu.

Systémové požadavky zahrnují podporované platformy pro klienty připojující se k diskům, podporované účty úložiště a typy úložiště.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Kontrola požadavků

1. Musíte mít objednaný Data Box Disk podle pokynů v článku [Kurz: Objednání Azure Data Box Disku](data-box-disk-deploy-ordered.md). Obdrželi jste své disky a jeden propojovací kabel pro každý disk.
2. Máte k dispozici klientský počítač, ze kterého můžete kopírovat data. Klientský počítač musí splňovat tyto požadavky:

    - Musí na něm běžet podporovaný operační systém.
    - Musí na něm být nainstalovaný další požadovaný software.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

Tady je seznam podporovaných operačních systémů pro operaci odemknutí disku a kopírování dat prostřednictvím klientů připojených k Data Box Disku.

| **Operační systém** | **Testované verze** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64bitová verze) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Další požadovaný software pro klienty Windows

V případě klienta Windows je potřeba nainstalovat také následující software.

| **Software**| **Verze** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Další požadovaný software pro klienty Linuxu

V případě klienta Linuxu sada nástrojů Data Box Disk nainstaluje následující požadovaný software:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Podporované připojení

Klientský počítač, který obsahuje data, musí mít port USB 3.0 nebo novější. Disky se s tímto klientem propojí pomocí přiloženého kabelu.

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

Tady je seznam podporovaných typů úložiště pro Data Box Disk.

| **Účet úložiště** | **Poznámky** |
| --- | --- |
| Classic | Standard |
| Pro obecné účely  |Standard (podporují se verze V1 i V2). Podporují se horká i studená úroveň. |
| Účet úložiště objektů blob | |

>[!NOTE]
> Účty Azure Data Lake Storage Gen2 se nepodporují.


## <a name="supported-storage-types-for-upload"></a>Podporované typy úložiště pro nahrání

Tady je seznam podporovaných typů úložiště pro nahrání do Azure pomocí Data Box Disku.

| **Formát souboru** | **Poznámky** |
| --- | --- |
| Objekt blob bloku Azure | |
| Objekt blob stránky Azure  | |
| Soubory Azure  | |
| Spravované disky | |

::: zone target="docs"

## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box Disku](data-box-disk-deploy-ordered.md)

::: zone-end

