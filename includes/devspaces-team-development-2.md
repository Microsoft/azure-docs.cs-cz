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
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157884"
---
### <a name="run-the-service"></a>Spuštění služby

Pokud chcete spustit službu, stiskněte F5 (nebo typ `azds up` v okně terminálu) ke spuštění služby. Služba se automaticky spustí v nově vybraný prostor _dev/scott_. Potvrďte, že vaše služba je spuštěná ve vlastním prostoru spuštěním `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Všimněte si, že instance *mywebapi* je nyní spuštěna v _dev/scott_ místa. Verze, která běží _dev_ je pořád spuštěný, ale není uvedený.

Seznam adres URL pro aktuální místo spuštěním `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Všimněte si, že adresa URL bodu veřejný přístup pro *webfrontend* předchází *scott.s*. Je jedinečný pro tuto adresu URL _dev/scott_ místa. Tuto předponu adresy URL říká kontroler příchozího přenosu dat ke směrování požadavků _dev/scott_ verzi služby. Při žádosti o tuto adresu URL zařizuje služba Dev mezery, kontroler příchozího přenosu dat se nejprve pokusí směrovat žádosti *webfrontend* služby v _dev/scott_ místa. Pokud selže, žádost přesměruje na *webfrontend* službu _dev_ místo jako záložní. Všimněte si také adresu URL místního hostitele pro přístup ke službě prostřednictvím místního hostitele pomocí rozhraní Kubernetes je *port vpřed* funkce. Další informace o směrování v Azure Dev mezery a adresy URL, najdete v části [jak prostory vývoj Azure funguje a je nakonfigurován](../articles/dev-spaces/how-dev-spaces-works.md).



![Místo směrování](../articles/dev-spaces/media/common/Space-Routing.png)

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

Teď, když zkoumáte prostory Azure Dev, [sdílet místa na vývoj se členem týmu](../articles/dev-spaces/how-to/share-dev-spaces.md) a zahájit spolupráci.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```