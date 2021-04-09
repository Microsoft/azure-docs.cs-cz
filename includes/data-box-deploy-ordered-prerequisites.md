---
title: Požadavky zahrnují soubor sdílený dvěma kartami ve stejném souboru | Microsoft Docs
description: Azure Data Box nasazovat seřazené požadavky
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85392470"
---
### <a name="for-service"></a>Služba

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:

* Máte k síti datacentra připojený hostitelský počítač. Data Box zkopíruje data k tohoto počítače. Na vašem hostitelském počítači musí být podporovaný operační systém, jak je popsáno v [systémových požadavcích na Azure Data Box](../articles/databox/data-box-system-requirements.md).
* Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. Pokud není k dispozici připojení s 10 GbEmi, je možné použít datové propojení s 1 GbEí, ale budou ovlivněny rychlosti kopírování.
