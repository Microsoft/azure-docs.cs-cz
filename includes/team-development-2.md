---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e3762744d2d1f021531694b2d9a3f75ca1c3ed49
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291108"
---
### <a name="run-the-service"></a>Spuštění služby

1. Pokud chcete spustit službu, stiskněte klávesu F5 (nebo v okně terminálu zadejte `azds up`). Služba se automaticky spustí v nově vybraném prostoru `default/scott`. 
1. Opětovným spuštěním `azds list-up` můžete ověřit, že je vaše služba spuštěná ve vlastním prostoru. Nejprve si můžete všimnout, že instance `mywebapi` je nyní spuštěná v prostoru `default/scott` (verze spuštěná v `default` je stále spuštěná, ale není uvedená). Pokud spustíte příkaz `azds list-uris`, můžete si všimnout, že adresa URL přístupového bodu bude mít pro `webfrontend` předponu s textem „scott.s.“. Tato adresa URL je jedinečná pro prostor `default/scott`. Tato speciální adresa URL značí, že požadavky odeslané na adresu URL scott vyzkouší nejprve trasu ke službám v prostoru `default/scott`, ale pokud se to nezdaří, přejdou zpět ke službám v prostoru `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje testování kódu ve sdíleném prostoru bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Pokud chcete otestovat novou verzi `mywebapi` s projektem `webfrontend`, otevřete v prohlížeči adresu URL veřejného přístupového bodu pro `webfrontend` a přejděte na stránku O aplikaci. Měla by se zobrazit nová zpráva.

Nyní odeberte část „scott.s.“ adresy URL a aktualizujte stránku v prohlížeči. Mělo by se zobrazit staré chování (s verzí `mywebapi` spuštěnou v `default`).
