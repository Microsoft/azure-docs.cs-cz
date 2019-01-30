---
title: Požadavky na systém Microsoft Azure Data Box | Dokumentace Microsoftu
description: Seznamte se s požadavky vašeho Azure Data Boxu na software a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206743"
---
# <a name="azure-data-box-system-requirements"></a>Požadavky na systém Azure Data Box

Tento článek popisuje důležité systémové požadavky pro vaše zařízení Microsoft Azure Data Box a klienti připojení k zařízení Data Box. Doporučujeme, abyste že si pečlivě přečetli informace předtím, než nasadíte Data Box a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

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
| SMB |2.X a novější |
| Systém souborů NFS | Všechny verze až po a včetně 4.1|

### <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Tady je seznam účtů úložiště podporuje a typy úložiště pro zařízení Data Box. Úplný seznam všech různých typů účtů úložiště a jejich úplné možnosti najdete v tématu [typy účtů úložiště](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Účet úložiště / podporované typy úložiště** | **Objekt blob bloku** |**Objekt blob stránky*** |**Služba soubory Azure** |**Poznámky**|
| --- | --- | -- | -- | -- |
| Klasické Standard | Ano | Ano | Ano |
| Standard pro obecné účely v1  | Ano | Ano | Ano | Horká a studená jsou podporovány.|
| Premium pro obecné účely v1  |  | Ano| | |
| Standard pro obecné účely v2  | Ano | Ano | Ano | Horká a studená jsou podporovány.|
| Premium pro obecné účely v2  |  |Ano | | |
| Úložiště objektů BLOB Standard |Ano | | |Horká a studená jsou podporovány. |

\* *-Daty nahranými do objektů BLOB stránky musí být zarovnán jako je například virtuální pevné disky 512 bajtů.*

>[!NOTE]
> Účty Azure Data Lake Storage generace 2 nejsou podporovány.


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

Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud 10 GbE připojení není k dispozici, 1 GbE dat propojení lze použít ke zkopírování dat, ale kopie, které se to týká rychlosti.

## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)

