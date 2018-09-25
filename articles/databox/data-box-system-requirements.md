---
title: Požadavky na systém Microsoft Azure Data Box | Dokumentace Microsoftu
description: Další informace o softwaru a síťové požadavky pro vaše zařízení Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f97c6174adf454a031e94942843075c457236575
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982968"
---
# <a name="azure-data-box-system-requirements"></a>Požadavky na systém Azure Data Box

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Microsoft Azure Data Box a klienti připojení k zařízení Data Box. Doporučujeme, abyste si informace o pečlivě před nasazení vašeho zařízení Data Box a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Systémové požadavky:

* **Požadavky na software pro připojení k zařízení Data Box hostitele** -popisuje podporované platformy, prohlížeče pro místního webového uživatelského rozhraní, klienti SMB a veškeré další požadavky pro hostitele, které se můžete připojit k zařízení Data Box.
* **Požadavky na síť pro zařízení Data Box** – poskytuje informace o síťové požadavky pro optimální fungování zařízení Data Box.


## <a name="software-requirements"></a>Požadavky na software

Požadavky na software také informace o podporovaných operačních systémů, podporované prohlížeče pro místního webového uživatelského rozhraní a klienti SMB.

### <a name="supported-operating-systems-for-clients"></a>Podporované operační systémy pro klienty

Tady je seznam podporovaných operačních systémech pro kopírování dat prostřednictvím klienty připojené k zařízení Data Box.

| **Operační systém** | **Verze** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Podporované souborové systémy pro klienty Linux

| **Protokoly** | **Verze** | 
| --- | --- | 
| Protokol SMB |2.X a novější |
| Systém souborů NFS | Všechny verze až po a včetně 4.1|

### <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Tady je seznam typů úložiště podporuje pro zařízení Data Box.

| **Účet úložiště** | **Poznámky** |
| --- | --- |
| Classic | Standard |
| Obecné účely  |Standardní; verze V1 i V2 podporují. |
| Objekt blob |Horká a studená jsou podporovány. |


### <a name="supported-storage-types"></a>Typy podporovaných úložišť

Tady je seznam typů úložiště podporuje pro zařízení Data Box.

| **Formát souboru** | **Poznámky** |
| --- | --- |
| Objekt blob bloku Azure | |
| Objekt blob stránky Azure  | Data by měla být 512 bajtů zarovnána.|
| Soubory Azure | |


### <a name="supported-web-browsers"></a>Podporované webové prohlížeče

Tady je seznam webových prohlížečů podporovaných pro místního webového uživatelského rozhraní.

| **Prohlížeč** | **Verze** | **Další požadavky a poznámky** |
| --- | --- | --- |
| Google Chrome |Nejnovější verze |Testování s prohlížečem Chrome|
| Microsoft Edge |Nejnovější verze | |
| FireFox | Nejnovější verze | Testování s prohlížečem FireFox|
| Internet Explorer |Nejnovější verze |Pokud se nemůžete přihlásit, zkontrolujte, zda jsou povoleny soubory cookie a Javascript. Pokud chcete povolit přístup k uživatelskému rozhraní, přidat IP adresu zařízení k **akce ochrany osobních údajů** tak, aby zařízení může získat přístup k soubory cookie. |


## <a name="networking-requirements"></a>Požadavky na síť

Vaše datové centrum musí mít vysokorychlostní sítě. Důrazně doporučujeme, abyste měli aspoň jeden 10 GbE připojení. Pokud 10 GbE připojení není k dispozici, 1 GbE datové propojení lze použít ke zkopírování dat, ale rychlost kopírování se to týká.

## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)

