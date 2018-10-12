---
title: Požadavky na systém Microsoft Azure Data Box | Dokumentace Microsoftu
description: Seznamte se s požadavky vašeho Azure Data Boxu na software a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 957100e042da1d41fb7c8fa27a5663cf99a41c20
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093931"
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

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE. Pokud 10 GbE připojení není k dispozici, 1 GbE datové propojení lze použít ke zkopírování dat, ale rychlost kopírování se to týká.

## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)

