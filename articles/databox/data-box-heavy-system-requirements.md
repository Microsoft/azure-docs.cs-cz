---
title: Microsoft Azure Data Box náročné systémové požadavky | Dokumentace Microsoftu
description: Další informace o softwaru a požadavky na síť pro váš Azure Data Box náročné
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839780"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box náročné požadavky na systém

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Azure Data Box náročné a pro připojení klientů k zařízení. Doporučujeme, abyste že si pečlivě přečetli informace předtím, než nasadíte vaše náročné pole dat a poté vraťte se k němu podle potřeby během nasazení a následná operace.

Systémové požadavky:

* **Softwarové požadavky pro připojování k velkým pole Data hostitele** -popisuje podporované platformy, prohlížeče pro místního webového uživatelského rozhraní, klienti SMB a veškeré další požadavky pro hostitele, které se můžete připojit k zařízení Data Box.
* **Požadavky na síť pro náročné pole Data** – poskytuje informace o síťové požadavky pro optimální fungování zařízení Data Box náročné.

## <a name="software-requirements"></a>Požadavky na software

Požadavky na software také informace o podporovaných operačních systémů, podporované prohlížeče pro místního webového uživatelského rozhraní a klienti SMB.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Podporované souborové systémy pro klienty Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Účty úložiště podporuje

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Typy podporovaných úložišť

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Požadavky na síť

Vaše datacentrum má přístup k vysokorychlostní síti. Pro nejrychlejší kopírování rychlosti můžete využít dvě připojení k 40 10GbE paralelně (jeden na uzel). Pokud nemáte k dispozici 40 10GbE, doporučujeme, že máte alespoň dva 10 GbE připojení (jeden do každého uzlu).

### <a name="port-requirements"></a>Požadavky na porty

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall pro povolení provozu SMB nebo NFS. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení Data Box náročné odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Další postup

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
