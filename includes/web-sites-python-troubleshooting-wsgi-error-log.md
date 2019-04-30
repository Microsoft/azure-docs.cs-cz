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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270190"
---
Pokud Python dojde k chybě při spouštění vaší aplikace, (např.) bude vrácen pouze jednoduché chybovou stránku "Na stránce nelze zobrazit, protože došlo k vnitřní chybě serveru.").

K zachycení chyb aplikace Python:

1. Na portálu Azure ve vaší webové aplikaci, vyberte **nastavení**.
2. Na **nastavení** kartu, vyberte možnost **nastavení aplikace**.
3. V části **nastavení aplikace**, zadejte následující dvojici klíč/hodnota:
    * Klíč: WSGI_LOG
    * Hodnota: D:\home\site\wwwroot\logs.txt (zadejte svůj název souboru)

Teď byste měli vidět chyby v souboru logs.txt ve složce wwwroot.
