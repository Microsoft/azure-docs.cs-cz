---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: f6245a97f5d94c90e022ac509b61da477f4d9494
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823828"
---
### <a name="run-the-service"></a>Spouštění služby

1. Stiskněte F5 (nebo typ `azds up` v okně Terminálové) ke spuštění služby. Služba se automaticky spustí v nově vybrané místo `scott`. 
1. Můžete potvrdit, že vaše služba běží ve vlastním prostoru spuštěním `azds resource list` znovu. První, můžete si všimnout instance `mywebapi` je nyní spuštěna v `scott` místa (verze provozován `default` místa je stále spuštěná, ale není uveden). Za druhé přístup bodu adresy URL pro `webfrontend` má nyní předponu `scott.s.`. Tato adresa URL je jedinečná `scott` místa a znamená, že požadavky odeslané na adresu"scott" se nejprve pokusí směrovat do služeb v `scott` místa, a pokud neexistuje žádná konkrétní `scott` instance pro službu, požadavky se vrátili ke službám v `default` místa.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tato integrované funkce prostory Dev Azure umožňuje testování kódu začátku do konce v prostoru sdílené dev bez nutnosti každý vývojář se znovu vytvořit úplné zásobníku služeb v jejich místo. Tato směrování vyžaduje kód aplikace a předat dál šíření hlavičky, jak je ukázáno v předchozím kroku tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Otestovat novou verzi systému `mywebapi` s `webfrontend`, otevřete prohlížeč na adresu URL bodu veřejný přístup pro webfrontend a přejít na stránku o. Měli byste vidět novou zprávu zobrazí.

Nyní, odeberte `scott.s.` součástí adresy URL a aktualizujte stránku v prohlížeči. Měli byste vidět staré chování (vykazují `mywebapi` verze spuštěné v `default`)