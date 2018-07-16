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
ms.openlocfilehash: 2f2177b289ba9d13ba635bddcd61748e1e8ab0d1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39063096"
---
### <a name="run-the-service"></a>Spuštění služby

1. Pokud chcete spustit službu, stiskněte klávesu F5 (nebo v okně terminálu zadejte `azds up`). Služba se automaticky spustí v nově vybraném prostoru `default/scott`. 
1. Opětovným spuštěním `azds list-up` můžete ověřit, že je vaše služba spuštěná ve vlastním prostoru. Nejprve si můžete všimnout, že instance `mywebapi` je nyní spuštěná v prostoru `default/scott` (verze spuštěná v `default` je stále spuštěná, ale není uvedená). Dále bude mít adresa URL přístupového bodu pro `webfrontend` předponu s textem „scott.s.“. Tato adresa URL je jedinečná pro prostor `default/scott`. Tato speciální adresa URL značí, že požadavky odeslané na adresu URL scott vyzkouší nejprve trasu ke službám v prostoru `default/scott`, ale pokud se to nezdaří, přejdou zpět ke službám v prostoru `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje testování kódu ve sdíleném prostoru bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Pokud chcete otestovat novou verzi `mywebapi` s projektem `webfrontend`, otevřete v prohlížeči adresu URL veřejného přístupového bodu pro `webfrontend` a přejděte na stránku O aplikaci. Měla by se zobrazit nová zpráva.

Nyní odeberte část „scott.s.“ adresy URL a aktualizujte stránku v prohlížeči. Mělo by se zobrazit staré chování (s verzí `mywebapi` spuštěnou v `default`)
