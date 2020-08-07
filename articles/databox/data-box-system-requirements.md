---
title: Microsoft Azure Data Box požadavky na systém | Microsoft Docs
description: Seznamte se s důležitými požadavky na systém pro vaše Azure Data Box a klienty, kteří se připojují k Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 71f499e1cdd7ccf22d90b21ce04299798978a828
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926599"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box požadavky na systém

Tento článek popisuje důležité systémové požadavky pro Microsoft Azure Data Box a pro klienty, kteří se připojují k Data Box. Před nasazením Data Box doporučujeme pečlivě zkontrolovat informace a pak je v případě potřeby v průběhu nasazení a následné operace vrátit na ni.

Požadavky na systém zahrnují:

* **Požadavky na software pro hostitele, kteří se připojují k data box** – popisuje podporované platformy, prohlížeče pro místní webové uživatelské rozhraní, klienty SMB a jakékoli další požadavky pro hostitele, kteří se mohou připojit k data box.
* **Požadavky na síť pro data box** – poskytuje informace o požadavcích sítě pro optimální fungování data box.


## <a name="software-requirements"></a>Požadavky na software

Požadavky na software zahrnují informace o podporovaných operačních systémech, podporovaných prohlížečích místních webových uživatelských rozhraní a klientech SMB.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-filesystems-for-linux-clients"></a>Podporované systémy souborů pro klienty se systémem Linux

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

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není k dispozici připojení s 10 GbEmi, je možné ke kopírování dat použít datové propojení s 1 GbEí, ale budou to mít vliv na rychlost kopírování.

### <a name="port-requirements"></a>Požadavky na porty

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby bylo možné provozovat přenosy SMB nebo NFS. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení data Box odesílá data externě, mimo nasazení: například odchozí na Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
