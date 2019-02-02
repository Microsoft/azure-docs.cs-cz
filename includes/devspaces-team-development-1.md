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
ms.openlocfilehash: 1f6e0a8fd2cc14877b98bc12b0d2c8632edbbbb9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664364"
---
# <a name="team-development-with-azure-dev-spaces"></a>Týmový vývoj se službou Azure Dev Spaces

V tomto kurzu se dozvíte, jak tým vývojářů může současně spolupracovat ve stejném clusteru Kubernetes pomocí prostorů vývoj.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji
Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Povolte tým vývojářů pro práci ve stejném prostředí, a to práce ve sdíleném vývojovém prostoru nebo v různých dev mezer podle potřeby.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není ukázková aplikace moc složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy. Může se stát, že spouštění všech služeb místně nebude během vývoje reálné.

* Svého vývojového počítače nemusí mít dostatek prostředků ke spuštění každé služby, které potřebujete najednou.
* Některé služby bude muset být veřejně dostupný. Služba možná muset mít koncový bod, který reaguje na webhooku.
* Pokud chcete spustit podmnožinu, budete muset znát hierarchii úplné závislost mezi všechny služby. Určení, že to může být obtížné, zejména jako váš počet služeb zvýšit.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomoct, ale Správa těchto mocks může brzy mít vliv na náklady na vývoj. Kromě toho tento postup vede do svého vývojového prostředí hledání velmi rozdíl oproti možnosti production, což umožňuje ovládat drobné chyby.
* Z toho vyplývá, že bude obtížné to libovolného typu testování integrace. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použít Dev prostory pro vývoj v týmu
Tyto nápadů pomocí konkrétní příklad si ukážeme náš *webfrontend* -> *mywebapi* ukázkovou aplikaci. Jsme budete Představte si situaci, kde pro vývojáře, Scott, musí provést změnu *mywebapi* služby, a *pouze* danou službu. *Webfrontend* není potřeba ji měnit jako součást Scottova aktualizace.

_Bez_ mezerami Dev, Scott má několik způsobů, jak vyvíjet a testovat jeho aktualizace, z nichž žádnou jsou ideální:
* VŠECHNY součásti můžete spusťte lokálně. To vyžaduje výkonnější vývojovém počítači nainstalovaný Docker, a potenciálně MiniKube.
* Spusťte všechny součásti v izolované oboru názvů v clusteru Kubernetes. Protože *webfrontend* se mění, jde o plýtvání prostředky clusteru.
* Spustit pouze *mywebapi*a provádět ruční volání REST pro testování. To není testování tohoto toku úplného začátku do konce.
* Přidejte kód zaměřený na vývoj pro *webfrontend* , který umožňuje vývojářům k odesílání požadavků na jinou instanci procesu *mywebapi*. To komplikuje *webfrontend* služby.

### <a name="set-up-your-baseline"></a>Nastavit standardní hodnoty
Nejdřív potřebujeme nasazení standardních hodnot z našich služeb. Toto nasazení bude představovat "poslední známé dobré", abyste mohli snadno porovnat chování místní kódu oproti verzi vrácené se změnami. Poté vytvoříme podřízené místo podle těchto standardních hodnot, takže můžeme otestovat změny v našich *mywebapi* v rámci větší aplikace.

1. Klonování [Dev prostory ukázkovou aplikaci](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Rezervujte vzdálenou větev *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Vyberte _dev_ místa: `azds space select --name dev`. Po zobrazení výzvy k výběru místa dev nadřazené vyberte  _\<žádný\>_.
1. Přejděte _mywebapi_ adresáře a spusťte: `azds up -d`
1. Přejděte _webfrontend_ adresáře a spusťte: `azds up -d`
1. Spustit `azds list-uris` zobrazíte veřejný koncový bod pro _webfrontend_

> [!TIP]
> Výše uvedené kroky ručně nastavit směrný plán, ale doporučujeme používat týmy CI/CD a automaticky udržovat aktuální potvrzené kódem směrný plán.
>
> Podívejte se na naše [příručka k nastavení CI/CD s Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) vytvořit pracovní postup podobně jako v následujícím diagramu.
>
> ![Příklad diagramu CI/CD](../articles/dev-spaces/media/common/ci-cd-complex.png)

V tomto okamžiku by měl být spuštěn směrný plán. Spusťte příkaz `azds list-up` a zobrazí se podobný výstup:

```
Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

Ve sloupci DevSpace zobrazí, že obě služby jsou spuštěny v prostoru s názvem _dev_. Každý, kdo je otevře veřejnou adresu URL a přejde do webové aplikace se vyvolá cestu vrácené se změnami kódu, na kterém běží prostřednictvím obě služby. Nyní předpokládejme, že chcete pokračovat ve vývoji _mywebapi_. Jak provedete změny kódu a otestujete je bez toho, abyste přerušili práci ostatních vývojářů, kteří používají stejné vývojové prostředí? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-dev-space"></a>Vytvoření vývojového prostoru
Spuštění vlastní verzi _mywebapi_ v prostoru než _dev_, vlastní místa můžete vytvořit pomocí následujícího příkazu:

```cmd
azds space select --name scott
```

Po zobrazení výzvy vyberte _dev_ jako **nadřazené místo dev**. To znamená, že náš nový prostor _dev/scott_, odvodí z prostoru _dev_. Za chvíli si ukážeme, jak nám to pomůže s testováním.

Udržení s naše úvodní hypotetické, jsme použili název _scott_ pro nový prostor partnerské uzly mohli identifikovat, kdo pracuje v ní. Ale může být volána nic a o tom, co znamená flexibilně, jako jsou _sprint4_ nebo _ukázka_. Bez ohledu případ, _dev_ slouží jako základ pro všechny vývojáře, kteří pracují na část této aplikace:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Spuštěním příkazu `azds space list` zobrazíte seznam všech prostorů ve vývojovém prostředí. _Vybrané_ sloupec uvádí, které místo jste vybrali (true/false). Ve vašem případě prostoru s názvem _dev/scott_ byl automaticky vybrán při vytvoření rovnou uložil. Kdykoli si můžete vybrat jiný prostor pomocí příkazu `azds space select`.

Podívejte se, jak to funguje: