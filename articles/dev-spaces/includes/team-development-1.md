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
ms.openlocfilehash: 96a749c0cb59759e9294f52bd4f631d7fdc2275f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
Pokud jste jste spuštěna kódu vaší aplikace, jako kdyby pouze vývojář pracující na aplikaci. V této části se dozvíte, jak prostory Dev Azure zjednodušuje vývoj v týmu:
* Povolte tým práci ve stejném prostředí vývoj vývojářům.
* Podporuje každý vývojář iterace na kód v izolaci a bez obav z nejnovější jiné.
* Testování kódu klient server, před potvrzení kódu bez nutnosti vytvoření mocks nebo simulovat závislosti.

## <a name="challenges-with-developing-microservices"></a>Problémy s vývojem mikroslužeb
V tuto chvíli není velmi složité ukázkovou aplikaci. Ale v vývoj skutečných výzvy brzy vznikat, jak přidat další služby a zvětšování vývojový tým.

Obrázek sami práce na službě, která komunikuje s spoustě dalších služeb.

- Může stát vzhledem ke spuštění všechno místně pro vývoj. Vývojářském počítači nemusí mít dostatek prostředků ke spuštění celé aplikace. Nebo možná vaše aplikace obsahuje koncové body, které musí být veřejně přístupný (například aplikace reaguje na webhook, jehož z aplikace SaaS).

- Můžete zkusit spustit pouze služby, které závisí na, ale to znamená, že by potřebovat znát Úplné uzavření závislosti (například závislosti závislosti). Nebo, bude stačit není snadno zjistit, jak sestavit a spustit závislostmi, protože nebyla pracovat s nimi.
- Někteří vývojáři uchýlit k simulaci nebo mocking si řadu závislé služby. Tento přístup může pomoci někdy ale Správa těchto mocks může trvat brzy na svůj vlastní náročnost vývoje. Navíc tento přístup vede k prostředí dev vyhledávání příliš neliší do produkčního prostředí a jemně chyby můžete ovládat.
- Z toho vyplývá, že to libovolného typu klient server testování bude obtížné. Testování integrace může dojít pouze reálně po potvrzení, což znamená, že jste se zobrazit problémy, později v cyklu vývoje.

![](../media/common/microservices-challenges.png)


## <a name="work-in-a-shared-development-environment"></a>Fungovaly v prostředí s sdílené vývoj
Dev prostory Azure, můžete nastavit *sdílené* vývojového prostředí v Azure. Každý vývojář se můžou zaměřit na jejich součásti aplikace a můžete interaktivně vyvíjet *předem potvrdit kód* v prostředí, které již obsahuje všechny ostatní služby a cloudové prostředky, které jejich scénářů závisí na. Závislosti jsou vždy aktuální a vývojáři pracují způsobem, který odpovídá produkční.

## <a name="work-in-your-own-space"></a>Práce v vlastní místa
Když budete vyvíjet kódu služby a předtím, než jste připraveni zkontrolujte ho v kódu často nebudou ve funkčním stavu. Můžete se pořád interaktivně službu shaping, jeho otestováním a experimentování se řešení. Azure prostory Dev nabízí koncepci **místo**, který umožňuje pracovat v izolaci a bez obavy z nejnovější členové týmu.

> [!Note]
> Než budete pokračovat, zavřete všechna okna VS Code pro obě služby a potom spusťte `azds up -d` v každé z kořenové složky služby. (Jedná se o omezení verze Preview.)

Podívejme bližší pohled na aktuálně spuštěným služeb. Spustit `azds list` příkaz a zobrazí se výstup podobný následujícímu:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     https://webfrontend-contosodev.1234abcdef.westeurope.aksapp.io
```

Místo sloupci se zobrazuje, že obě služby jsou spuštěny v prostoru s názvem `default`. Každý, kdo otevře veřejnou adresu URL a přejde do webové aplikace se vyvolat cesty kódu, který jste napsali dříve používající prostřednictvím obě služby. Nyní předpokládejme, že chcete pokračovat ve vývoji `mywebapi`. Jak provádět změny kódu a otestovat je a není přerušení ostatní vývojáři, kteří používají vývojová prostředí? Kvůli tomu budete nastavit vlastní místa.

## <a name="create-a-space"></a>Vytvoření prostoru
Ke spuštění svou vlastní verzi `mywebapi` v prostoru jinak než `default`, můžete vytvořit vlastní místo pomocí následujícího příkazu:

``` 
azds space create --name scott
```

V příkladu nahoře tak, aby se osobní pro moje partnerské uzly, které je místo na práci v mé název pro nový prostor použitých, ale můžete ji volat nic jako a flexibilně o tom, co je, jako je 'sprint4' nebo "ukázku."

Spustit `azds space list` příkazu zobrazte seznam všechny mezery v prostředí vývojářů. Znak hvězdičky (*) se zobrazí vedle aktuálně vybraný prostor. Ve vašem případě byl místa s názvem 'scott' automaticky vybrán při byla vytvořena. Kdykoli se můžete vybrat jiné místo `azds space select` příkaz.
