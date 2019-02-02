---
title: Týmový vývoj prostory vývoj Azure pomocí .NET Core a Visual Studio | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 7a77b8a1a2205465956d8c30a3fee6aec5e8428b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663787"
---
# <a name="team-development-with-azure-dev-spaces"></a>Týmový vývoj se službou Azure Dev Spaces

V tomto kurzu se dozvíte, jak tým vývojářů může současně spolupracovat ve stejném clusteru Kubernetes pomocí prostorů vývoj.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Povolte tým vývojářů pro práci ve stejném prostředí, a to práce ve sdíleném vývojovém prostoru nebo v různých dev mezer podle potřeby.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není komplexní ukázkovou aplikaci. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

* Svého vývojového počítače nemusí mít dostatek prostředků ke spuštění každé služby, které potřebujete najednou.
* Některé služby bude muset být veřejně dostupný. Služba možná muset mít koncový bod, který reaguje na webhooku.
* Pokud chcete spustit podmnožinu, budete muset znát hierarchii úplné závislost mezi všechny služby. Určení Tato hierarchie může být obtížné, zejména když zvýšit počet služeb.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomoct, ale Správa těchto mocks může brzy mít vliv na náklady na vývoj. Kromě toho tento postup vede do svého vývojového prostředí hledání rozdíl oproti možnosti production, což může vést k drobným chybám, ke kterým dochází.
* Z toho vyplývá, že bude obtížné to libovolného typu testování integrace. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použít Dev prostory pro vývoj v týmu
Tyto nápadů pomocí konkrétní příklad si ukážeme náš *webfrontend* -> *mywebapi* ukázkovou aplikaci. Jsme budete Představte si situaci, kde pro vývojáře, Scott, musí provést změnu *mywebapi* služby, a *pouze* danou službu. *Webfrontend* není potřeba ji měnit jako součást Scottova aktualizace.

_Bez_ mezerami Dev, Scott má několik způsobů, jak vyvíjet a testovat jeho aktualizace, z nichž žádnou jsou ideální:
* VŠECHNY součásti spustit místně, což vyžaduje výkonnější vývojovém počítači nainstalovaný Docker, a potenciálně MiniKube.
* Spusťte všechny součásti v izolované oboru názvů v clusteru Kubernetes. Protože *webfrontend* není změněn izolované pomocí oboru názvů by o plýtvání prostředky clusteru.
* Spustit pouze *mywebapi*a provádět ruční volání REST pro testování. Tento typ testování není testování tohoto toku úplného začátku do konce.
* Přidejte kód zaměřený na vývoj pro *webfrontend* , který umožňuje vývojářům k odesílání požadavků na jinou instanci procesu *mywebapi*. Přidání tohoto kódu komplikuje *webfrontend* služby.

### <a name="set-up-your-baseline"></a>Nastavit standardní hodnoty
Nejdřív potřebujeme nasazení standardních hodnot z našich služeb. Toto nasazení bude představovat "poslední známé dobré", abyste mohli snadno porovnat chování místní kódu oproti verzi vrácené se změnami. Poté vytvoříme podřízené místo podle těchto standardních hodnot, takže můžeme otestovat změny v našich *mywebapi* v rámci větší aplikace.

1. Klonování [Dev prostory ukázkovou aplikaci](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Podívejte se na vzdálené větve *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Zavřete všechny ladicí relace (otevřené pomocí F5) pro obě služby, ale nechte projekty v oknech sady Visual Studio otevřené.
1. Přepněte do okna sady Visual Studio s _mywebapi_ projektu.
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
1. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
1. Vyberte **změnu** vytvořit prostor, který bude nepoužívá, pokud jste F5 nebo Ctrl + F5 služby.
1. V rozevírací nabídce místa, vyberte  **\<vytvořte nový prostor... \>**.
1. Ujistěte se, že místo nadřazené nastavená na  **\<žádný\>** a zadejte název prostoru **dev**. Klikněte na tlačítko OK.
1. Stisknutím kláves Ctrl + F5 ke spuštění _mywebapi_ bez ladicí program připojený.
1. Přepněte do okna sady Visual Studio s _webfrontend_ projektu a stiskněte klávesu Ctrl + F5 ke spuštění i.

> [!Note]
> Někdy je při stisknutí Ctrl+F5 nutné po prvním zobrazení webové stránky v prohlížeči danou stránku aktualizovat.

> [!TIP]
> Výše uvedené kroky ručně nastavit směrný plán, ale doporučujeme používat týmy CI/CD a automaticky udržovat aktuální potvrzené kódem směrný plán.
>
> Podívejte se na naše [příručka k nastavení CI/CD s Azure DevOps](how-to/setup-cicd.md) vytvořit pracovní postup podobně jako v následujícím diagramu.
>
> ![Příklad diagramu CI/CD](media/common/ci-cd-complex.png)

Každý, kdo je otevře veřejnou adresu URL a přejde do webové aplikace se vyvolá do cesty kódu, který jste napsali který projde obou služeb pomocí výchozího _dev_ místa. Nyní předpokládejme, že chcete pokračovat ve vývoji *mywebapi* – jak to provést a přerušit jinými vývojáři, kteří používají místo vývoj? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-new-dev-space"></a>Vytvoření nového vývojového prostoru
V sadě Visual Studio můžete vytvářet další prostory, které se použijí, když pro danou službu stisknete F5 nebo Ctrl+F5. Prostor můžete pojmenovat libovolně (např. _sprint4_ nebo _ukázka_).

K vytvoření nového prostoru použijte následující postup:
1. Přepněte do okna sady Visual Studio s *mywebapi* projektu.
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
3. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
4. Odtud můžete změnit nebo vytvořit cluster nebo prostor, který se použije po stisknutí F5 nebo Ctrl+F5. *Zkontrolujte, že je vybraný vývojový prostor Azure, který jste vytvořili dříve*.
5. V rozevírací nabídce místa, vyberte  **\<vytvořte nový prostor... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. V **přidejte místo** dialogové okno, nastavte nadřazené místo na **dev**a zadejte název nové místo. Můžete pro nový prostor použít svoje jméno (například „scott“), aby bylo vašim kolegům jasné, že se jedná o prostor, ve kterém pracujete vy. Klikněte na **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teď by se měl zobrazit váš cluster AKS a na stránce vlastností projektu by měl být vybraný nový prostor.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualizace kódu pro *mywebapi*

1. V *mywebapi* projektu provést kód, přejděte `string Get(int id)` metody v souboru `Controllers/ValuesController.cs` následujícím způsobem:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Nastavte zarážku v tomto aktualizovaném bloku kódu (jednu už můžete mít nastavenou z dřívějška).
3. Stisknutím klávesy F5 spusťte _mywebapi_ službu, která se spustí službu ve vašem clusteru pomocí vybraný prostor. V tomto případě je vybraný prostor _scott_.

Následující diagram vám pomůže porozumět tomu, jak různé prostory fungují. Fialový cesta ukazuje žádost přes _dev_ místa, což je výchozí cesta použít, pokud žádný prostor je přidáno jako předpona adresy URL. Cesta růžový ukazuje požadavek prostřednictvím _dev/scott_ místa.

![](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje komplexní testování kódu ve sdíleném prostředí bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-running-in-the-devscott-space"></a>Testovací kód spuštěný v _dev/scott_ místa
Otestovat novou verzi systému *mywebapi* ve spojení s *webfrontend*, otevřete prohlížeč na adresu URL bodu veřejný přístup pro *webfrontend* (například http://dev.webfrontend.123456abcdef.eastus.aksapp.io)a přejděte na stránku o. Měla by se zobrazit původní zpráva: „Hello from webfrontend and Hello from mywebapi.“

Teď k adrese URL přidejte „scott.s.“. Adresa URL tedy bude vypadat nějak podobně jako http://scott.s.dev.webfrontend.123456abcdef.eastus.aksapp.io. Aktualizujte stránku v prohlížeči. Se zarážka nastavila ve vaší *mywebapi* projektu by měl získat přístupů. Pokračujte kliknutím na F5. V prohlížeči by se teď měla zobrazit nová zpráva „Hello from webfrontend and mywebapi now says something new.“ Důvodem je, že cesta k aktualizovaný kód v *mywebapi* běží v _dev/scott_ místa.

Jakmile budete mít _dev_ místa, které vždy obsahuje nejnovější změny a za předpokladu, že vaše aplikace je navržené tak, aby výhod DevSpace od místa založené na směrování, jak je popsáno v této části kurzu, snad budete moct snadno najdete v článku jak prostory vývoje velmi užitečné při testování nových funkcí v rámci větší aplikace. Namísto nutnosti nasazení _všechny_ služby do privátního prostoru, můžete vytvořit privátní místa, která je odvozena z _dev_a "pouze nahoru" služby ve skutečnosti právě pracujete. Infrastruktura směrování Dev prostory se postaráme o všechno ostatní s využitím tolik služeb z vaší privátní místo jak to můžete znát při jako výchozí se použije zpět do nejnovější verze, která běží _dev_ místa. A stále lepší _více_ vývojáři můžou aktivně vyvíjet různé služby ve stejnou dobu do jejich vlastního prostoru pravidla bez narušení běžného mezi sebou.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Stanovení základní úrovně funkce s použitím vývoje prostorů na snadno testujte izolovaných změn v rámci větších aplikací mikroslužeb

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```