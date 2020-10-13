---
title: Microsoft Azure Data Box požadavky na systém | Microsoft Docs
description: Seznamte se s důležitými požadavky na systém pro vaše Azure Data Box a klienty, kteří se připojují k Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767873"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box požadavky na systém

Tento článek popisuje důležité systémové požadavky pro Microsoft Azure Data Box a pro klienty, kteří se připojují k Data Box. Doporučujeme pečlivě zkontrolovat informace před nasazením Data Box a pak podle potřeby v průběhu nasazení a provozu na něj odkazovat.

Požadavky na systém zahrnují:

* **Požadavky na software:** Pro hostitele, kteří se připojují k Data Box, popisuje podporované operační systémy, protokoly přenosů souborů, účty úložiště, typy úložiště a prohlížeče pro místní webové uživatelské rozhraní.
* **Požadavky na síť:** V Data Box popisuje síťové připojení a požadavky na porty pro optimální provoz Data Box.


## <a name="software-requirements"></a>Požadavky na software

Požadavky na software zahrnují podporované operační systémy, protokoly přenosů souborů, účty úložiště, typy úložiště a prohlížeče pro místní webové uživatelské rozhraní.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Podporované protokoly přenosů souborů pro klienty

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Připojení k Data Box sdíleným složkám není podporováno pro objednávky exportu prostřednictvím REST. 

### <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Požadavky na síť

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není k dispozici připojení s 10 GbEmi, je možné ke kopírování dat použít linku s 1 GbEí, ale budou ovlivněny i rychlosti kopírování.

### <a name="port-requirements"></a>Požadavky na porty

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby bylo možné provozovat přenosy SMB nebo NFS. V této tabulce se *v* (*příchozí*) odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní (* nebo *odchozí*) odkazuje na směr, ve kterém vaše zařízení data Box odesílá data externě, mimo nasazení: například odchozí na Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
