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
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
### <a name="run-the-service"></a>Spouštění služby

1. Stiskněte F5 (nebo typ `azds up` v okně Terminálové) ke spuštění služby. Služba se automaticky spustí v nově vybrané místo `scott`. 
1. Můžete potvrdit, že vaše služba běží ve vlastním prostoru spuštěním `azds resource list` znovu. První, můžete si všimnout instance `mywebapi` je nyní spuštěna v `scott` místa (verze provozován `default` je stále spuštěná, ale není uveden). Za druhé, přístup bodu adresy URL pro `webfrontend` je s předponou text *scott.s.*. Tato adresa URL je jedinečná `scott` místo a označuje, že požadavky odeslané na adresu"scott" se pokusí o první trasy pro služby `scott` místo a vrátí ke službám v `default` místa.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tato integrované funkce prostory Dev Azure umožňuje testování kódu začátku do konce ve sdíleném prostředí bez nutnosti každý vývojář se znovu vytvořit úplné zásobníku služeb v jejich místo. Tato směrování vyžaduje kód aplikace a předat dál šíření hlavičky, jak je ukázáno v předchozím kroku tohoto průvodce.

## <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Otestovat novou verzi systému `mywebapi` ve spojení s `webfrontend`, otevřete prohlížeč na adresu URL bodu veřejný přístup pro webfrontend a přejít na stránku o. Měli byste vidět novou zprávu zobrazí.

Nyní odeberte "scott.s." součástí adresy URL a aktualizujte stránku v prohlížeči. Měli byste vidět staré chování (vykazují `mywebapi` verze spuštěné v `default`)