---
title: Vývoj pro týmovou práci pomocí Node. js a Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: V tomto kurzu se dozvíte, jak používat Azure Dev Spaces a Visual Studio Code k vývoji týmu v aplikaci Node. js ve službě Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: e2124d6c3f903f6a9faa6c41a2015b7198faf42d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438122"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Vývoj pro týmovou práci pomocí Node. js a Visual Studio Code s Azure Dev Spaces

V tomto kurzu se dozvíte, jak může tým vývojářů současně spolupracovat ve stejném clusteru Kubernetes pomocí vývojových prostorů.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji
Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožněte týmu vývojářů pracovat ve stejném prostředí, při práci ve sdíleném prostoru pro vývoj nebo v různých prostorech pro vývoj podle potřeby.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není ukázková aplikace moc složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy. Může se stát, že spouštění všech služeb místně nebude během vývoje reálné.

* Váš vývojový počítač nemusí mít dostatek prostředků ke spuštění každé služby, kterou potřebujete.
* Některé služby možná budou muset být veřejně dosažitelné. Služba může například potřebovat koncový bod, který reaguje na Webhook.
* Pokud chcete spustit podmnožinu služeb, musíte znát celou hierarchii závislostí mezi všemi vašimi službami. Určení toho může být obtížné, zejména při zvýšení počtu služeb.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomáhat, ale Správa těchto napodobenin může brzy ovlivnit náklady na vývoj. Kromě toho tento přístup vede k vašemu vývojovému prostředí, které se velmi liší od výroby, což umožňuje jemné chyby v nárůstu.
* Postup provádění jakéhokoli typu integračního testování se bude obtížně provádět. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použití vývojových prostorů pro vývoj v týmu
Pojďme předvést tyto nápady pomocí konkrétního příkladu s naší ukázkovou aplikací *webendu* -> *mywebapi* . Představme si situaci, kdy vývojář, Scott, potřebuje provést změnu služby *mywebapi* a *jenom* tuto službu. Webfront- *Endu* se nebude muset změnit jako součást Scott Update.

_Bez_ použití vývojových prostorů by Scott měl několik způsobů vývoje a testování jeho aktualizace, přičemž žádná z nich není ideální:
* VŠECHNY součásti spouštějte místně. To vyžaduje výkonnější vývojový počítač s nainstalovaným Docker a potenciálně MiniKube.
* Spustí všechny komponenty v izolovaném oboru názvů v clusteru Kubernetes. Vzhledem k tomu, že se *webendu* nemění, je to odpad z prostředků clusteru.
* Spouštějte jenom *mywebapi*a proveďte manuální volání REST. Tím se netestuje celý tok na konci.
* Přidejte do *webendu* kód zaměřený na vývoj, který vývojářům umožňuje odesílat požadavky na jinou instanci *mywebapi*. Tím se ztěžuje služba *webendu* .

### <a name="set-up-your-baseline"></a>Nastavení standardních hodnot
Nejprve bude nutné nasadit směrný plán našich služeb. Toto nasazení bude představovat "poslední známé funkční", takže můžete snadno porovnat chování místního kódu a verze vráceného se změnami. Pak vytvoříme podřízený prostor na základě tohoto směrného plánu, abychom mohli otestovat naše změny v *mywebapi* v kontextu větší aplikace.

1. Naklonujte [ukázkovou aplikaci pro vývojové prostory](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Rezervovat *azds_updates*vzdálené větve: `git checkout -b azds_updates origin/azds_updates`
1. Vyberte prostor pro _vývoj_ : `azds space select --name dev`. Po zobrazení výzvy k výběru nadřazeného prostoru pro vývoj vyberte _\<žádná\>_ .
1. Přejděte do adresáře _mywebapi_ a spusťte příkaz: `azds up -d`
1. Přejděte do adresáře _webendu_ a spusťte: `azds up -d`
1. Spusťte `azds list-uris` pro zobrazení veřejného koncového bodu pro _webendu_

> [!TIP]
> Výše uvedené kroky ručně nastaví směrný plán, ale doporučujeme, aby týmy používaly CI/CD k automatickému udržování směrného plánu v aktuálním stavu pomocí potvrzeného kódu.
>
> Pokud chcete vytvořit pracovní postup podobný následujícímu diagramu, přečtěte si náš průvodce vytvořením [CI/CD pomocí Azure DevOps](how-to/setup-cicd.md) .
>
> ![Příklad diagramu CI/CD](media/common/ci-cd-complex.png)

V tomto okamžiku by měl být spuštěný základní plán. Spusťte příkaz `azds list-up --all` a zobrazí se podobný výstup:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

Sloupec DevSpace ukazuje, že obě služby jsou spuštěny v prostoru s názvem _vývoj_. Kdokoli, kdo otevře veřejnou adresu URL a přejde k webové aplikaci, vyvolá cestu k vrácenému kódu, která se spouští prostřednictvím obou služeb. Nyní předpokládejme, že chcete pokračovat ve vývoji _mywebapi_. Jak provedete změny kódu a otestujete je bez toho, abyste přerušili práci ostatních vývojářů, kteří používají stejné vývojové prostředí? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-dev-space"></a>Vytvoření vývojového prostoru
Chcete-li spustit vlastní verzi _mywebapi_ v _jiném než vývoji_, můžete vytvořit vlastní místo pomocí následujícího příkazu:

```cmd
azds space select --name scott
```

Po zobrazení výzvy vyberte možnost _dev (vývoj_ ) **nadřazeného vývojového prostoru**. To znamená, že náš nový prostor, _dev/Scott_, bude odvozen od _vývoje_místa. Za chvíli si ukážeme, jak nám to pomůže s testováním.

V naší úvodní hypotetickě jsme použili název _Scott_ pro nové místo, takže partneři budou moci zjistit, kdo v něm pracuje. Ale může být volána cokoli, co potřebujete, a být flexibilní o tom, co to znamená, jako je _sprint4_ nebo _demo_. Bez ohledu na to, jaký případ nabízí, může _vývoj_ sloužit jako základ pro všechny vývojáře, kteří pracují na části této aplikace:

![](media/common/ci-cd-space-setup.png)

Spuštěním příkazu `azds space list` zobrazíte seznam všech prostorů ve vývojovém prostředí. _Vybraný_ sloupec indikuje, který prostor máte aktuálně vybraný (true/false). V takovém případě bylo při vytvoření automaticky vybráno místo s názvem _dev/Scott_ . Kdykoli si můžete vybrat jiný prostor pomocí příkazu `azds space select`.

Podívejte se, jak to funguje:

### <a name="make-a-code-change"></a>Provedení změny kódu
Pro `mywebapi` použijte VS Code okno a v `server.js`proveďte úpravy kódu pro výchozí obslužnou rutinu GET `/`. Příklad:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>Spuštění služby

Pokud chcete službu spustit, stiskněte klávesu F5 (nebo zadejte `azds up` do okna terminálu) a službu spusťte. Služba se automaticky spustí s nově vybraným místem pro _vývoj/Scott_. Ověřte, že je služba spuštěná na svém vlastním prostoru spuštěním `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Všimněte si, že instance *mywebapi* je teď spuštěná v prostoru pro _vývoj/Scott_ . Verze spuštěná v _dev_ je pořád spuštěná, ale není uvedená.

Vypíše adresy URL pro aktuální prostor spuštěním `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Všimněte si, že adresa URL bodu veřejného přístupu pro *webendu* je s předponou *Scott. s*. Tato adresa URL je jedinečná pro _vývojové a scottský_ prostor. Tato předpona adresy URL oznamuje řadiči příchozího přenosu žádostí, aby směroval požadavky na verzi služby _dev/Scott_ . Pokud je žádost s touto adresou URL zpracována pomocí vývojových prostorů, kontroler příchozího přenosu dat se nejprve pokusí směrovat požadavek do služby *webendu* ve _vývojovém nebo scottovém_ prostoru. Pokud se to nezdaří, bude požadavek směrován do služby *webendu* v prostoru pro _vývoj_ jako záložní. Všimněte si také, že adresa URL místního hostitele pro přístup ke službě přes localhost pomocí funkce *předávaného portu* Kubernetes. Další informace o adresách URL a směrování v Azure Dev Spaces najdete v tématu [Jak fungují Azure dev Spaces a jsou nakonfigurované](how-dev-spaces-works.md).

![Směrování místa](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje testování kódu ve sdíleném prostoru bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-in-a-space"></a>Testování kódu v prostoru
Pokud chcete otestovat novou verzi *mywebapi* pomocí *webendu*, otevřete prohlížeč na adrese URL veřejného přístupového bodu pro *webendu* a přejděte na stránku o produktu. Měla by se zobrazit nová zpráva.

Nyní odeberte část „scott.s.“ adresy URL a aktualizujte stránku v prohlížeči. Mělo by se zobrazit staré chování (s verzí *mywebapi* spuštěnou v _dev_).

Jakmile budete mít místo pro _vývoj_ , které vždycky obsahuje vaše nejnovější změny, a za předpokladu, že vaše aplikace je navržená tak, aby využila výhod směrování založeného na DevSpace, jak je popsané v tomto kurzu, snad se snadno podívat, jak si můžou vývojové prostory významně pomoct při testování nových funkcí v kontextu větší aplikace. Místo toho, abyste museli nasazovat _všechny_ služby do privátního prostoru, můžete vytvořit soukromý prostor, který je odvozený od _vývoje_, a jenom služby, na kterých skutečně pracujete. Infrastruktura směrování pro vývoj pro vývojová prostředí zpracuje zbytek tím, že se z vašeho privátního prostoru dokončí tolik služeb, jak může najít, zatímco se ve výchozím nastavení vrátí na nejnovější verzi, která běží ve _vývojovém_ prostoru. A stále ještě _víc_ vývojářům může aktivně vyvíjet různé služby ve stejnou dobu, aniž by navzájem narušily vzájemné přerušení.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Vytvořte základní hodnoty funkcí pomocí vývojových prostorů, abyste mohli snadno testovat izolované změny v kontextu větší aplikace mikroslužeb.

Teď, když jste prozkoumali Azure Dev Spaces, [sdílejte místo pro vývoj s členem týmu](how-to/share-dev-spaces.md) a začněte spolupracovat.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
