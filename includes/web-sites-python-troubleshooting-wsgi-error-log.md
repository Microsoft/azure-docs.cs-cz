---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175611"
---
Pokud Python při spouštění aplikace narazí na chybu, bude vrácena pouze jednoduchá chybová stránka (například "Stránku nelze zobrazit, protože došlo k chybě interního serveru).").

Zachycení chyb aplikace pythonu:

1. Na webu Azure Portal ve webové aplikaci vyberte **Nastavení**.
2. Na kartě **Nastavení** vyberte **Nastavení aplikace**.
3. V části **Nastavení aplikace**zadejte následující dvojici klíč/hodnota:
    * Klíč : WSGI_LOG
    * Hodnota : D:\home\site\wwwroot\logs.txt (zadejte svůj výběr názvu souboru)

Nyní byste měli vidět chyby v souboru logs.txt ve složce wwwroot.
