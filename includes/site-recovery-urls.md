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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175532"
---
Name (Název) | Adresa URL | Adresa URL pro státní správu | Popis
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Používá se pro řízení přístupu a správu identit pomocí služby Azure Active Directory. 
Zálohování | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Používá se ke koordinaci a přenosu dat replikace.
Replikace | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Používá se pro koordinaci a operace správy replikací.
Úložiště | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data.
Telemetrie (volitelně) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Používá se pro telemetrii.
Čas synchronizace | ``time.windows.com`` | ``time.nist.gov`` | Slouží ke kontrole synchronizace času mezi systémem a globálním časem ve všech nasazeních.


