---
title: Vývoj týmu pomocí rozhraní .NET Core a Visual Studia
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Tento kurz ukazuje, jak používat Azure Dev Spaces a Visual Studio k vývoji týmu v aplikaci .NET Core ve službě Azure Kubernetes Service
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s '
ms.openlocfilehash: b4520ce35807fb022fa39ae9b00347a27e192380
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78245053"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Vývoj týmu pomocí rozhraní .NET Core a Visual Studia s Azure Dev Spaces

V tomto kurzu se dozvíte, jak tým vývojářů může současně spolupracovat ve stejném clusteru Kubernetes pomocí Dev Spaces.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožněte týmu vývojářů pracovat ve stejném prostředí, a to tak, že podle potřeby pracuje ve sdíleném vývojářém prostoru nebo v odlišných prostorách pro vývojáře.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
Ukázková aplikace není v tuto chvíli složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

* Vývojový počítač nemusí mít dostatek prostředků pro spuštění všech služeb, které potřebujete najednou.
* Některé služby mohou být nutné veřejně dostupné. Služba může například potřebovat koncový bod, který reaguje na webhooku.
* Pokud chcete spustit podmnožinu služeb, musíte znát úplnou hierarchii závislostí mezi všemi službami. Určení této hierarchie může být obtížné, zejména s nárůstem počtu služeb.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomoci, ale správa těchto mocks může brzy ovlivnit náklady na vývoj. Navíc tento přístup vede k vývojovému prostředí, které vypadá jinak než produkční prostředí, což může vést k jemným chybám.
* Z toho vyplývá, že dělat jakýkoli typ testování integrace se stává obtížné. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použití dev spaces pro vývoj týmu
Pojďme předvést tyto myšlenky s konkrétním příkladem pomocí našeho *webfrontend* -> *mywebapi* ukázkové aplikace. Budeme si představit scénář, kdy vývojář, Scott, potřebuje provést změnu služby *mywebapi* a *pouze* tuto službu. *Webfrontend* nebude muset měnit jako součást aktualizace Scott.

_Bez_ použití Dev Spaces, Scott by měl několik způsobů, jak vyvinout a otestovat jeho aktualizaci, z nichž žádný je ideální:
* Spusťte všechny komponenty místně, což vyžaduje výkonnější vývojový počítač s nainstalovaným Dockerem a potenciálně MiniKube.
* Spusťte všechny součásti v izolovaném oboru názvů v clusteru Kubernetes. Vzhledem k tomu, *že webfrontend* se nemění, použití izolovaného oboru názvů je plýtvání prostředky clusteru.
* Spusťte pouze *mywebapi*, a ruční REST volání k testování. Tento typ testování netestuje úplný tok od konce na konec.
* Přidejte kód zaměřený na vývoj do *webfrontendu,* který umožňuje vývojáři odesílat požadavky na jinou instanci *mywebapi*. Přidání tohoto kódu komplikuje *webfrontend* služby.

### <a name="set-up-your-baseline"></a>Nastavení směrného plánu
Nejprve budeme muset nasadit základní linii našich služeb. Toto nasazení bude představovat "poslední známé zboží", takže můžete snadno porovnat chování místního kódu vs. verze se změnami. Potom vytvoříme podřízený prostor založený na tomto směrnému plánu, abychom mohli otestovat naše změny *mywebapi* v kontextu větší aplikace.

1. Klonujte [ukázkovou aplikaci Dev Spaces](https://github.com/Azure/dev-spaces):`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Podívejte se na vzdálenou větev *azds_updates*:`git checkout -b azds_updates origin/azds_updates`
1. Zavřete všechny ladicí relace (otevřené pomocí F5) pro obě služby, ale nechte projekty v oknech sady Visual Studio otevřené.
1. Přepněte do okna sady Visual Studio s projektem _mywebapi._
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
1. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
1. Vyberte **Změnit,** chcete-li vytvořit prostor, který bude použit při f5 nebo ctrl+ f5 služby.
1. V rozevíracím příkazu Prostor vyberte ** \<Vytvořit nový prostor... \>**.
1. Zkontrolujte, zda je nadřazená mezera nastavena na ** \<\>žádnou**, a zadejte název oboru **dev**. Klikněte na tlačítko OK.
1. Stisknutím kombinace kláves Ctrl+F5 _spusťte rozhraní mywebapi_ bez připojeného ladicího programu.
1. Přepněte do okna sady Visual Studio s projektem _webfrontendu_ a spusťte jej také stisknutím kombinace kláves Ctrl+F5.

> [!Note]
> Někdy je při stisknutí Ctrl+F5 nutné po prvním zobrazení webové stránky v prohlížeči danou stránku aktualizovat.

> [!TIP]
> Výše uvedené kroky ručně nastavit směrný plán, ale doporučujeme týmy používají CI/CD automaticky udržovat váš směrný plán aktuální s potvrzeným kódem.
>
> Podívejte se na náš [průvodce nastavením CI/CD s Azure DevOps](how-to/setup-cicd.md) a vytvořte pracovní postup podobný následujícímu diagramu.
>
> ![Příklad diagramu CI/CD](media/common/ci-cd-complex.png)

Každý, kdo otevře veřejnou adresu URL a přejde do webové aplikace, vyvolá cestu kódu, kterou jste napsali a která prochází oběma službami pomocí _výchozího místa pro spuštění._ Nyní předpokládejme, že chcete pokračovat ve vývoji *mywebapi* - jak to můžete udělat a nepřerušit ostatní vývojáře, kteří používají dev prostor? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-new-dev-space"></a>Vytvoření nového vývojového prostoru
V sadě Visual Studio můžete vytvářet další prostory, které se použijí, když pro danou službu stisknete F5 nebo Ctrl+F5. Prostor můžete pojmenovat libovolně (např. _sprint4_ nebo _demo_).

K vytvoření nového prostoru použijte následující postup:
1. Přepněte do okna sady Visual Studio s projektem *mywebapi.*
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
3. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
4. Odtud můžete změnit nebo vytvořit cluster nebo prostor, který se použije po stisknutí F5 nebo Ctrl+F5. *Zkontrolujte, že je vybraný vývojový prostor Azure, který jste vytvořili dříve*.
5. V rozevíracím příkazu Prostor vyberte ** \<Vytvořit nový prostor... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. V dialogovém okně **Přidat mezeru** nastavte nadřazený prostor na **dev**a zadejte název nového prostoru. Můžete pro nový prostor použít svoje jméno (například „scott“), aby bylo vašim kolegům jasné, že se jedná o prostor, ve kterém pracujete vy. Klikněte na tlačítko **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teď by se měl zobrazit váš cluster AKS a na stránce vlastností projektu by měl být vybraný nový prostor.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualizace kódu pro *mywebapi*

1. V projektu *mywebapi* proveďte změnu `string Get(int id)` kódu `Controllers/ValuesController.cs` metody v souboru následujícím způsobem:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Nastavte zarážku v tomto aktualizovaném bloku kódu (jednu už můžete mít nastavenou z dřívějška).
3. Chcete-li spustit službu _mywebapi,_ která spustí službu v clusteru pomocí vybraného prostoru, spusťte položku F5. Vybrané místo v tomto případě je _scott_.

Následující diagram vám pomůže porozumět tomu, jak různé prostory fungují. Fialová cesta zobrazuje požadavek prostřednictvím _dev_ prostoru, což je výchozí cesta použitá, pokud není před adresou URL předřazeno žádné místo. Růžová cesta zobrazuje požadavek prostřednictvím prostoru _dev/scott._

![](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje komplexní testování kódu ve sdíleném prostředí bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-running-in-the-_devscott_-space"></a>Testovací kód spuštěný v prostoru _pro vývoj a vývoj scott_
Chcete-li otestovat novou verzi *mywebapi* ve spojení s *webfrontendem*, otevřete prohlížeč http://dev.webfrontend.123456abcdef.eus.azds.io) na adresu URL veřejného přístupového bodu pro *webfrontend* (například přejděte na stránku Informace. Měla by se zobrazit původní zpráva: „Hello from webfrontend and Hello from mywebapi.“

Teď k adrese URL přidejte „scott.s.“. část URL, takže to zní\:něco jako http //scott.s.dev.webfrontend.123456abcdef.eus.azds.io a obnovit prohlížeč. Zarážka, kterou nastavíte v projektu *mywebapi,* by měla být přístupová. Pokračujte kliknutím na F5. V prohlížeči by se teď měla zobrazit nová zpráva „Hello from webfrontend and mywebapi now says something new.“ Důvodem je, že cesta k aktualizovanému kódu v *mywebapi* je spuštěna v prostoru _pro vývoj a vývoj._

Jakmile budete mít _dev_ prostor, který vždy obsahuje nejnovější změny a za předpokladu, že vaše aplikace je navržena tak, aby využila směrování na základě prostoru DevSpace, jak je popsáno v této části kurzu, doufejme, že se stane snadné vidět, jak Dev Spaces může výrazně pomoci při testování nových funkcí v kontextu větší aplikace. Místo nutnosti nasazovat _všechny_ služby do soukromého prostoru můžete vytvořit soukromý prostor, který je odvozen od _aplikace dev_a pouze "nahoru" služby, na kterých skutečně pracujete. Infrastruktura směrování Dev Spaces zvládne zbytek využitím tolika služeb z vašeho soukromého prostoru, kolik dokáže najít, a zároveň se vrátí k nejnovější verzi spuštěné v prostoru _pro spuštění._ A ještě lépe, _více_ vývojářů může aktivně rozvíjet různé služby současně ve svém vlastním prostoru, aniž by se navzájem narušovali.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Vytvoření základního plánu funkcí pomocí funkce Dev Spaces pro snadné testování izolovaných změn v kontextu větší aplikace mikroslužeb

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
