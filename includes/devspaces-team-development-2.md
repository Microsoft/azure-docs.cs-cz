---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 749120446619bf682d02be0f9290a6d47540c16a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664369"
---
### <a name="run-the-service"></a>Spuštění služby

1. Pokud chcete spustit službu, stiskněte klávesu F5 (nebo v okně terminálu zadejte `azds up`). Služba se automaticky spustí v nově vybraný prostor _dev/scott_. 
1. Opětovným spuštěním `azds list-up` můžete ověřit, že je vaše služba spuštěná ve vlastním prostoru. Můžete si všimnout instance *mywebapi* je nyní spuštěna v _dev/scott_ místa (verze, která běží _dev_ je pořád spuštěný, ale není uvedená).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Spustit `azds list-uris`a Všimněte si, že přístupový bod adresy URL pro *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://dev.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
    ```

1. Místo "dev.s." jako předponu adresy URL nahraďte "scott.s." ve webovém prohlížeči. Všimněte si, že tato adresa URL aktualizována stále řeší. Je jedinečný pro tuto adresu URL _dev/scott_ místa. Speciální URL znamená, že požadavky odeslané na adresu"Scott" se pokusí první trasa ke službám v _dev/scott_ místa, ale pokud se nezdaří, bude přejdou na služby ve _dev_ místa.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje testování kódu ve sdíleném prostoru bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Otestovat novou verzi systému *mywebapi* s *webfrontend*, otevřete prohlížeč na adresu URL bodu veřejný přístup pro *webfrontend* a přejděte na stránku o. Měla by se zobrazit nová zpráva.

Nyní odeberte část „scott.s.“ adresy URL a aktualizujte stránku v prohlížeči. Měli byste vidět staré chování (s *mywebapi* verze, která běží _dev_).

Jakmile budete mít _dev_ místa, která vždy obsahuje nejnovější změny a za předpokladu, že vaše aplikace je navržená tak, výhod směrování na základě místa pro DevSpace jako je popsáno v této části kurzu, snad je snadné Podívejte se, jak vývoj prostory velmi užitečné při testování nových funkcí v rámci větší aplikace. Namísto nutnosti nasazení _všechny_ služby do privátního prostoru, můžete vytvořit privátní místa, která je odvozena z _dev_a "pouze nahoru" služby ve skutečnosti právě pracujete. Infrastruktura směrování Dev prostory se postaráme o všechno ostatní s využitím tolik služeb z vaší privátní místo jak to můžete znát při jako výchozí se použije zpět do nejnovější verze, která běží _dev_ místa. A stále lepší _více_ vývojáři můžou aktivně vyvíjet různé služby ve stejnou dobu do jejich vlastního prostoru pravidla bez narušení běžného mezi sebou.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Stanovení základní úrovně funkce s použitím vývoje prostorů na snadno testujte izolovaných změn v rámci větších aplikací mikroslužeb

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](../articles/dev-spaces/how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```