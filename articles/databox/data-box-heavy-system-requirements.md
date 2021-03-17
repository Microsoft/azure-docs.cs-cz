---
title: Microsoft Azure Data Box Heavy požadavky na systém | Microsoft Docs
description: Seznamte se s požadavky na software a sítě pro vaše Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707748"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy požadavky na systém

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Azure Data Box Heavy a klienty, kteří se k zařízení připojují. Před nasazením Data Box Heavy doporučujeme pečlivě zkontrolovat informace a pak je v případě potřeby v průběhu nasazení a následné operace vrátit na ni.

Požadavky na systém zahrnují:

* **Požadavky na software pro hostitele, kteří se připojují k data box Heavy** – popisuje podporované platformy, prohlížeče pro místní webové uživatelské rozhraní, klienty SMB a jakékoli další požadavky pro hostitele, kteří se mohou připojit k data box.
* **Požadavky na síť pro data box Heavy** – poskytuje informace o požadavcích sítě pro optimální provoz data box Heavy zařízení.

## <a name="software-requirements"></a>Požadavky na software

Požadavky na software zahrnují informace o podporovaných operačních systémech, podporovaných prohlížečích místních webových uživatelských rozhraní a klientech SMB.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Podporované systémy souborů pro klienty se systémem Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Požadavky na síť

Vaše datacentrum má přístup k vysokorychlostní síti. Pro nejrychlejší rychlost kopírování je možné používat paralelní připojení 2 40 (jeden pro každý uzel). Pokud nemáte k dispozici 40 až GbE, doporučujeme, abyste měli aspoň 2 10 připojení (jedna na uzel).

### <a name="port-requirements"></a>Požadavky na porty

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby bylo možné provozovat přenosy SMB nebo NFS. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení data box Heavy odesílá data externě, mimo nasazení: například odchozí na Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
