---
title: Vývoj týmu pomocí kódu .NET Core a Visual Studio
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: Tento kurz ukazuje, jak používat Azure Dev Spaces a Visual Studio Code k vývoji týmu v aplikaci .NET Core ve službě Azure Kubernetes Service
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: 69434c6168bfadbf3291c6efe85fb2f1934c8d11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251971"
---
# <a name="team-development-using-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Vývoj týmu pomocí kódu .NET Core a Visual Studio code s Azure Dev Spaces

V tomto kurzu se dozvíte, jak tým vývojářů může současně spolupracovat ve stejném clusteru Kubernetes pomocí Dev Spaces.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji
Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožněte týmu vývojářů pracovat ve stejném prostředí, a to tak, že podle potřeby pracuje ve sdíleném vývojářém prostoru nebo v odlišných prostorách pro vývojáře.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není ukázková aplikace moc složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy. Může se stát, že spouštění všech služeb místně nebude během vývoje reálné.

* Vývojový počítač nemusí mít dostatek prostředků pro spuštění všech služeb, které potřebujete najednou.
* Některé služby mohou být nutné veřejně dostupné. Služba může například potřebovat koncový bod, který reaguje na webhooku.
* Pokud chcete spustit podmnožinu služeb, musíte znát úplnou hierarchii závislostí mezi všemi službami. Určení tohoto může být obtížné, zejména proto, že se zvyšuje počet služeb.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomoci, ale správa těchto mocks může brzy ovlivnit náklady na vývoj. Navíc tento přístup vede k tomu, že vaše vývojové prostředí vypadá velmi odlišně od výroby, což umožňuje jemné chyby, které se vplíží dovnitř.
* Z toho vyplývá, že dělat jakýkoli typ testování integrace se stává obtížné. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použití dev spaces pro vývoj týmu
Pojďme předvést tyto myšlenky s konkrétním příkladem pomocí našeho *webfrontend* -> *mywebapi* ukázkové aplikace. Budeme si představit scénář, kdy vývojář, Scott, potřebuje provést změnu služby *mywebapi* a *pouze* tuto službu. *Webfrontend* nebude muset měnit jako součást aktualizace Scott.

_Bez_ použití Dev Spaces, Scott by měl několik způsobů, jak vyvinout a otestovat jeho aktualizaci, z nichž žádný je ideální:
* Spusťte všechny součásti místně. To vyžaduje výkonnější vývojový počítač s nainstalovaným Dockerem a potenciálně MiniKube.
* Spusťte všechny součásti v izolovaném oboru názvů v clusteru Kubernetes. Vzhledem k tomu, *že webfrontend* se nemění, je to plýtvání prostředky clusteru.
* Spusťte pouze *mywebapi*, a ruční REST volání k testování. To netestuje celý tok od konce do konce.
* Přidejte kód zaměřený na vývoj do *webfrontendu,* který umožňuje vývojáři odesílat požadavky na jinou instanci *mywebapi*. To komplikuje *webfrontend* služby.

### <a name="set-up-your-baseline"></a>Nastavení směrného plánu
Nejprve budeme muset nasadit základní linii našich služeb. Toto nasazení bude představovat "poslední známé zboží", takže můžete snadno porovnat chování místního kódu vs. verze se změnami. Potom vytvoříme podřízený prostor založený na tomto směrnému plánu, abychom mohli otestovat naše změny *mywebapi* v kontextu větší aplikace.

1. Klonujte [ukázkovou aplikaci Dev Spaces](https://github.com/Azure/dev-spaces):`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Pokladna vzdálené větve *azds_updates*:`git checkout -b azds_updates origin/azds_updates`
1. Vyberte _dev_ `azds space select --name dev`prostor: . Po zobrazení výzvy k výběru _ \<\>_ nadřazené dev mezery vyberte možnost Žádný .
1. Přejděte do adresáře _mywebapi_ a spusťte:`azds up -d`
1. Přejděte do adresáře _webfrontendu_ a spusťte:`azds up -d`
1. Spustit `azds list-uris` zobrazení veřejného koncového bodu pro _webfrontend_

> [!TIP]
> Výše uvedené kroky ručně nastavit směrný plán, ale doporučujeme týmy používají CI/CD automaticky udržovat váš směrný plán aktuální s potvrzeným kódem.
>
> Podívejte se na náš [průvodce nastavením CI/CD s Azure DevOps](how-to/setup-cicd.md) a vytvořte pracovní postup podobný následujícímu diagramu.
>
> ![Příklad diagramu CI/CD](media/common/ci-cd-complex.png)

V tomto okamžiku by měl být spuštěn směrný plán. Spusťte příkaz `azds list-up --all` a zobrazí se podobný výstup:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace sloupec ukazuje, že obě služby jsou spuštěny v prostoru s názvem _dev_. Každý, kdo otevře veřejnou adresu URL a přejde do webové aplikace, vyvolá cestu kódu se změnami, která prochází oběma službami. Nyní předpokládejme, že chcete pokračovat ve vývoji _mywebapi_. Jak provedete změny kódu a otestujete je bez toho, abyste přerušili práci ostatních vývojářů, kteří používají stejné vývojové prostředí? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-dev-space"></a>Vytvoření vývojového prostoru
Chcete-li spustit vlastní verzi _rozhraní mywebapi_ v jiném než _dev_, můžete vytvořit vlastní prostor pomocí následujícího příkazu:

```cmd
azds space select --name scott
```

Po zobrazení výzvy vyberte _jako_ **nadřazenou dev mezeru**. To znamená, že náš nový prostor, _dev / scott_, bude odvozen od prostoru _dev_. Za chvíli si ukážeme, jak nám to pomůže s testováním.

V souladu s naší úvodní hypotetickou, jsme použili jméno _Scott_ pro nový prostor, takže vrstevníci mohou identifikovat, kdo pracuje v něm. Ale to může být nazýván o všem, co se vám líbí, a být flexibilní o tom, co to znamená, jako _sprint4_ nebo _demo_. Ať tak či onak, _dev_ slouží jako základ pro všechny vývojáře pracující na část této aplikace:

![](media/common/ci-cd-space-setup.png)

Spuštěním příkazu `azds space list` zobrazíte seznam všech prostorů ve vývojovém prostředí. Vybraný _Selected_ sloupec označuje, které místo jste aktuálně vybrali (pravda/nepravda). Ve vašem případě byla při vytvoření automaticky vybrána mezera s názvem _dev/scott._ Kdykoli si můžete vybrat jiný prostor pomocí příkazu `azds space select`.

Podívejte se, jak to funguje:

### <a name="make-a-code-change"></a>Změna kódu
Přejděte do okna VS Kód pro `mywebapi` a `string Get(int id)` proveďte úpravu kódu metody v `Controllers/ValuesController.cs`oblasti , například:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```

### <a name="run-the-service"></a>Spuštění služby

Chcete-li službu spustit, spusťte službu tak, že stisknete položku F5 (nebo zadejte `azds up` okno terminálu). Služba se automaticky spustí v nově vybraném prostoru _dev/scott_. Spuštěním `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Všimněte si, že instance *mywebapi* je nyní spuštěna v prostoru _dev/scott._ Verze spuštěná v _dev_ je stále spuštěna, ale není uvedena.

Seznam adres URL pro aktuální prostor `azds list-uris`spuštěním .

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Všimněte si, že adresa URL veřejného přístupového bodu pro *webfrontend* je předpona u *scott.s*. Tato adresa URL je jedinečná pro prostor _pro vývoj a vývoj._ Tato předpona adresy URL informuje řadič příchozího přenosu dat směrovat požadavky na verzi _služby pro vývoj a vývoj._ Pokud je požadavek s touto adresou URL zpracován dev spaces, řadič příchozího přenosu dat se nejprve pokusí směrovat požadavek do služby *webfrontend* u prostoru _pro vývoj a vývoj._ Pokud se to nezdaří, požadavek bude směrován do služby *webfrontend* v _prostoru pro zpracování_ jako záložní. Všimněte si také, že je localhost URL pro přístup ke službě přes localhost pomocí kubernetes *port-forward* funkce. Další informace o adresách URL a směrování v Azure Dev Spaces najdete v tématu [Jak Azure Dev Spaces funguje a je nakonfigurovaný](how-dev-spaces-works.md).

![Směrování prostoru](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje testování kódu ve sdíleném prostoru bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Chcete-li otestovat novou verzi *mywebapi* s *webfrontendem*, otevřete prohlížeč na adresu URL veřejného přístupového bodu pro *webfrontend* a přejděte na stránku Informace. Měla by se zobrazit nová zpráva.

Nyní odeberte část „scott.s.“ adresy URL a aktualizujte stránku v prohlížeči. Měli byste vidět staré chování (s *mywebapi* verze spuštěna v _dev_).

Jakmile budete mít _dev_ prostor, který vždy obsahuje nejnovější změny, a za předpokladu, že vaše aplikace je navržen tak, aby využít devSpace prostor ové směrování, jak je popsáno v této části kurzu, doufejme, že se stane snadné vidět, jak Dev Spaces může výrazně pomoci při testování nových funkcí v kontextu větší aplikace. Místo nutnosti nasazovat _všechny_ služby do soukromého prostoru můžete vytvořit soukromý prostor, který je odvozen od _aplikace dev_a pouze "nahoru" služby, na kterých skutečně pracujete. Infrastruktura směrování Dev Spaces zvládne zbytek využitím tolika služeb z vašeho soukromého prostoru, kolik dokáže najít, a zároveň se vrátí k nejnovější verzi spuštěné v prostoru _pro spuštění._ A ještě lépe, _více_ vývojářů může aktivně rozvíjet různé služby současně ve svém vlastním prostoru, aniž by se navzájem narušovali.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Vytvoření základního plánu funkcí pomocí funkce Dev Spaces pro snadné testování izolovaných změn v kontextu větší aplikace mikroslužeb

Teď, když jste prozkoumali Azure Dev Spaces, [sdílejte svůj dev prostor s členem týmu](how-to/share-dev-spaces.md) a začněte spolupracovat.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
