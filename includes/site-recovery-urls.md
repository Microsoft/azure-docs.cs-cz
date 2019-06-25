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
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175532"
---
Název | Adresa URL komerční | Adresa URL pro státní správu | Popis
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Používat pro přístup k řízení a správě identit pomocí Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Používá se ke koordinaci a přenosu dat replikace.
Replikace | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Používá se pro koordinaci a operace správy replikací.
Úložiště | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
Telemetrie (volitelné) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Používá se pro telemetrii.
Čas synchronizace | ``time.windows.com`` | ``time.nist.gov`` | Používá se ke kontrole časové synchronizace mezi systémovým a globálním časem ve všech nasazeních.


