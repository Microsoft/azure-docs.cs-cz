---
title: Požadavky na systém datové schránky Microsoft Azure| Dokumenty společnosti Microsoft
description: Seznamte se s požadavky vašeho Azure Data Boxu na software a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259952"
---
# <a name="azure-data-box-system-requirements"></a>Požadavky na systém Azure Data Box

Tento článek popisuje důležité systémové požadavky pro datové schránky Microsoft Azure a pro klienty, kteří se připojují k datové schránce. Doporučujeme, abyste si tyto informace před nasazením datové schránky pečlivě prostudovali a pak se k ní vrátili podle potřeby během nasazení a následné operace.

Systémové požadavky zahrnují:

* **Požadavky na software pro hostitele, kteří se připojují k datové schránce** – popisuje podporované platformy, prohlížeče pro místní webové uživatelské prostředí, klienty SMB a všechny další požadavky pro hostitele, kteří se mohou připojit k datové schránce.
* **Síťové požadavky pro Datovou schránku** - poskytuje informace o síťových požadavcích pro optimální provoz datové schránky.


## <a name="software-requirements"></a>Požadavky na software

Požadavky na software zahrnují informace o podporovaných operačních systémech, podporovaných prohlížečích pro místní webové uživatelské prostředí a klientech SMB.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Podporované souborové systémy pro klienty Linuxu

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Požadavky na vytváření sítí

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud připojení 10 GbE není k dispozici, lze ke kopírování dat použít datové spojení 1 GbE, ale rychlost kopírování je ovlivněna.

### <a name="port-requirements"></a>Požadavky na port

V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy se systémem SMB nebo NFS. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k vašemu zařízení. *Odchozí* nebo *odchozí* označuje směr, ve kterém vaše zařízení Data Box odesílá data externě, mimo nasazení: například odchozí do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Další kroky

* [Nasazení datové schránky Azure](data-box-deploy-ordered.md)
