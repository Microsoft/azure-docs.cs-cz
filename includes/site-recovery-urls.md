---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060344"
---
**Název** | **Adresa URL komerční**  | **Adresa URL pro státní správu** | **Popis** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Používá pro přístup k řízení a správě identit pomocí AAD 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Používá pro koordinaci a přenosu dat replikace. 
Replikace | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Používá pro koordinaci a operace správy replikací. 
Úložiště | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data 
Telemetrie (volitelné) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Použít pro telemetrii 
Čas synchronizace | ``time.windows.com`` | ``time.nist.gov`` | Ssed ke kontrole časové synchronizace mezi systémovým a globálním časem ve všech nasazeních.


