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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175611"
---
Pokud Python při spuštění aplikace zaznamená chybu, bude vrácena pouze jednoduchá chybová stránka (například "stránku nelze zobrazit, protože došlo k vnitřní chybě serveru.").

Zachycení chyb aplikace Python:

1. V Azure Portal ve webové aplikaci vyberte **Nastavení**.
2. Na kartě **Nastavení** vyberte **nastavení aplikace**.
3. V části **nastavení aplikace** zadejte následující dvojici klíč/hodnota:
    * Klíč: WSGI_LOG
    * Hodnota: D:\home\site\wwwroot\logs.txt (zadejte název souboru, který si vyberete)

V souboru logs.txt by se teď měly zobrazit chyby ve složce Wwwroot.
