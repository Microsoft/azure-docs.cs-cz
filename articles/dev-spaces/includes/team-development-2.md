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
ms.openlocfilehash: 78dca327a470394d19e6befc6578abf2d499850c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247581"
---
### <a name="run-the-service"></a>Spouštění služby

1. Stiskněte F5 (nebo typ `azds up` v okně Terminálové) ke spuštění služby. Služba se automaticky spustí v nově vybrané místo `scott`. 
1. Můžete potvrdit, že vaše služba běží ve vlastním prostoru spuštěním `azds space list` znovu. První, můžete si všimnout instance `mywebapi` je nyní spuštěna v `scott` místa (verze provozován `default` je stále spuštěná, ale není uveden). Za druhé, přístup bodu adresy URL pro `webfrontend` je s předponou text "scott.s.". Tato adresa URL je jedinečná `scott` místa. Označuje adresu URL speciální požadavky odeslané na adresu"scott" se pokusí první trasy pro služby `scott` místa, ale pokud to nepomůže, bude přejdou ke službám v `default` místa.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tato integrované funkce prostory Dev Azure umožňuje otestovat kód v sdílené místo bez nutnosti každý vývojář se znovu vytvořit úplné zásobníku služeb v jejich místo. Tato směrování vyžaduje kód aplikace a předat dál šíření hlavičky, jak je ukázáno v předchozím kroku tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Otestovat novou verzi systému `mywebapi` s `webfrontend`, otevřete prohlížeč na adresu URL bodu veřejný přístup `webfrontend` a přejděte na stránku o. Měli byste vidět novou zprávu zobrazí.

Nyní odeberte "scott.s." součástí adresy URL a aktualizujte stránku v prohlížeči. Měli byste vidět staré chování (s `mywebapi` verze spuštěné v `default`)
