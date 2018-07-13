---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765534"
---
| Název | Adresa URL komerční | Adresa URL pro státní správu | Popis |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Používá pro přístup k řízení a správě identit pomocí AAD |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Používá pro koordinaci a přenosu dat replikace. |
| Replikace | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Používá pro koordinaci a operace správy replikací. |
| Úložiště | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data |
| Telemetrie (volitelné) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Použít pro telemetrii |

``time.nist.gov`` a ``time.windows.com`` slouží ke kontrole časové synchronizace mezi systémovým a globálním časem ve všech nasazeních.


