---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72168370"
---
>[!NOTE]
>Při práci s výchozími zásadami může Azure během nastavení tunelu IPsec fungovat jako iniciátor i respondér. Azure není podporován pouze jako respondér.
>

### <a name="initiator"></a>Initiator

V následujících oddílech jsou uvedeny podporované kombinace zásad, pokud je Azure iniciátorem pro tunel.

**Fáze-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256 DH_GROUP_2

**Fáze 2**

* GCM_AES_256, GCM_AES_256 PFS_NONE
* AES_256, SHA_1 PFS_NONE
* AES_256, SHA_256 PFS_NONE
* AES_128, SHA_1 PFS_NONE

### <a name="responder"></a>Protějšk

V následujících částech jsou uvedeny podporované kombinace zásad, pokud je Azure respondér pro tunelové propojení.

**Fáze-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256 DH_GROUP_2

**Fáze 2**

* GCM_AES_256, GCM_AES_256 PFS_NONE
* AES_256, SHA_1 PFS_NONE
* AES_256, SHA_256 PFS_NONE
* AES_128, SHA_1 PFS_NONE
* AES_256, SHA_1 PFS_1
* AES_256, SHA_1 PFS_2
* AES_256, SHA_1 PFS_14
* AES_128, SHA_1 PFS_1
* AES_128, SHA_1 PFS_2
* AES_128, SHA_1 PFS_14
* AES_256, SHA_256 PFS_1
* AES_256, SHA_256 PFS_2
* AES_256, SHA_256 PFS_14
* AES_256, SHA_1 PFS_24
* AES_256, SHA_256 PFS_24
* AES_128, SHA_256 PFS_NONE
* AES_128, SHA_256 PFS_1
* AES_128, SHA_256 PFS_2
* AES_128, SHA_256 PFS_14