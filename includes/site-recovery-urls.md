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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116031"
---
Název | Adresa URL komerční | Adresa URL pro státní správu | Popis
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Používat pro přístup k řízení a správě identit pomocí Azure Active Directory. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Používá se ke koordinaci a přenosu dat replikace.
Replikace | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Používá se pro koordinaci a operace správy replikací.
Úložiště | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
Telemetrie (volitelné) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Používá se pro telemetrii.
Čas synchronizace | ``time.windows.com`` | ``time.nist.gov`` | Používá se ke kontrole časové synchronizace mezi systémovým a globálním časem ve všech nasazeních.


