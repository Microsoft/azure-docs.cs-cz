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
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394022"
---
Pokud Python dojde k chybě při spouštění vaší aplikace, (např.) bude vrácen pouze jednoduché chybovou stránku "Na stránce nelze zobrazit, protože došlo k vnitřní chybě serveru.").

K zachycení chyb aplikace Python:

1. Na portálu Azure ve vaší webové aplikaci, vyberte **nastavení**.
2. Na **nastavení** kartu, vyberte možnost **nastavení aplikace**.
3. V části **nastavení aplikace**, zadejte následující dvojici klíč/hodnota:
    * Klíč: WSGI_LOG
    * Hodnota: D:\home\site\wwwroot\logs.txt (zadejte svůj název souboru)

Teď byste měli vidět chyby v souboru logs.txt ve složce wwwroot.
