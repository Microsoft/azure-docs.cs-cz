---
title: Požadavky na systém Microsoft Azure Data Box | Dokumentace Microsoftu
description: Seznamte se s požadavky vašeho Azure Data Boxu na software a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839747"
---
# <a name="azure-data-box-system-requirements"></a>Požadavky na systém Azure Data Box

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Microsoft Azure Data Box a klienti připojení k zařízení Data Box. Doporučujeme, abyste že si pečlivě přečetli informace předtím, než nasadíte Data Box a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Systémové požadavky:

* **Požadavky na software pro připojení k zařízení Data Box hostitele** -popisuje podporované platformy, prohlížeče pro místního webového uživatelského rozhraní, klienti SMB a veškeré další požadavky pro hostitele, které se můžete připojit k zařízení Data Box.
* **Požadavky na síť pro zařízení Data Box** – poskytuje informace o síťové požadavky pro optimální fungování zařízení Data Box.


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

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, 1 GbE dat propojení lze použít ke zkopírování dat, ale kopie, které se to týká rychlosti.

### <a name="port-requirements"></a>Požadavky na porty

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall pro povolení provozu SMB nebo NFS. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém vaše zařízení Data Box odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Další postup

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
