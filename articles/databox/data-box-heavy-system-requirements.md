---
title: Požadavky na systém Microsoft Azure Data Box Heavy| Dokumenty společnosti Microsoft
description: Informace o softwaru a síťových požadavcích pro azure data box heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260069"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Požadavky na systém Azure Data Box Heavy

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Azure Data Box Heavy a pro klienty, kteří se k němu připojují. Doporučujeme, abyste si tyto informace před nasazením datové schránky Heavy pečlivě prostudovali a pak se k ní vrátili podle potřeby během nasazení a následné operace.

Systémové požadavky zahrnují:

* **Požadavky na software pro hostitele, kteří se připojují k datové schránce Heavy** – popisují podporované platformy, prohlížeče pro místní webové uživatelské prostředí, klienty SMB a všechny další požadavky pro hostitele, kteří se mohou připojit k datové schránce.
* **Síťové požadavky pro datovou schránku Heavy** - poskytuje informace o síťových požadavcích pro optimální provoz zařízení Data Box Heavy.

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

Vaše datacentrum má přístup k vysokorychlostní síti. Pro nejrychlejší rychlostkopírování lze paralelně využít dvě připojení 40 GbE (jeden na uzel). Pokud nemáte k dispozici 40 GbE, doporučujeme mít alespoň dvě připojení 10 GbE (jeden na uzel).

### <a name="port-requirements"></a>Požadavky na port

V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy se systémem SMB nebo NFS. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k vašemu zařízení. *Odchozí* nebo *odchozí* označuje směr, ve kterém vaše zařízení Data Box Heavy odesílá data externě, mimo nasazení: například odchozí do Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Další kroky

* [Nasazení datové schránky Azure](data-box-deploy-ordered.md)
