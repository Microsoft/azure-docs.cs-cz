---
title: Team development using .NET Core and Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325535"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Team development using .NET Core and Visual Studio with Azure Dev Spaces

In this tutorial, you'll learn how a team of developers can simultaneously collaborate in the same Kubernetes cluster using Dev Spaces.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Enable a team of developers to work in the same environment, by working in a shared dev space or in distinct dev spaces as needed.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
Your sample application isn't complex at the moment. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

* Your development machine may not have enough resources to run every service you need at once.
* Some services may need to be publicly reachable. For example, a service may need to have an endpoint that responds to a webhook.
* If you want to run a subset of services, you have to know the full dependency hierarchy between all your services. Determining this hierarchy can be difficult, especially as your number of services increase.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. This approach can help, but managing those mocks can soon impact development cost. Plus, this approach leads to your development environment looking different from production, which can lead to subtle bugs occurring.
* It follows that doing any type of integration testing becomes difficult. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Use Dev Spaces for team development
Let's demonstrate these ideas with a concrete example using our *webfrontend* -> *mywebapi* sample application. We'll imagine a scenario where a developer, Scott, needs to make a change to the *mywebapi* service, and *only* that service. The *webfrontend* won't need to change as part of Scott's update.

_Without_ using Dev Spaces, Scott would have a few ways to develop and test his update, none of which are ideal:
* Run ALL components locally, which requires a more powerful development machine with Docker installed, and potentially MiniKube.
* Run ALL components in an isolated namespace on the Kubernetes cluster. Since *webfrontend* isn't changing, using an isolated namespace is a waste of cluster resources.
* ONLY run *mywebapi*, and make manual REST calls to test. This type of testing doesn't test the full end-to-end flow.
* Add development-focused code to *webfrontend* that allows the developer to send requests to a different instance of *mywebapi*. Adding this code complicates the *webfrontend* service.

### <a name="set-up-your-baseline"></a>Set up your baseline
First we'll need to deploy a baseline of our services. This deployment will represent the "last known good" so you can easily compare the behavior of your local code vs. the checked-in version. We'll then create a child space based on this baseline so we can test our changes to *mywebapi* within the context of the larger application.

1. Clone the [Dev Spaces sample application](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Check out the remote branch *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Zavřete všechny ladicí relace (otevřené pomocí F5) pro obě služby, ale nechte projekty v oknech sady Visual Studio otevřené.
1. Switch to the Visual Studio window with the _mywebapi_ project.
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
1. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
1. Select **Change** to create the space that will be used when you F5 or Ctrl+F5 the service.
1. In the Space dropdown, select **\<Create New Space…\>** .
1. Make sure the parent space is set to **\<none\>** , and enter space name **dev**. Klikněte na tlačítko OK.
1. Press Ctrl+F5 to run _mywebapi_ without the debugger attached.
1. Switch to the Visual Studio window with the _webfrontend_ project and press Ctrl+F5 to run it as well.

> [!Note]
> Někdy je při stisknutí Ctrl+F5 nutné po prvním zobrazení webové stránky v prohlížeči danou stránku aktualizovat.

> [!TIP]
> The above steps manually set up a baseline, but we recommend teams use CI/CD to automatically keep your baseline up to date with committed code.
>
> Check out our [guide to setting up CI/CD with Azure DevOps](how-to/setup-cicd.md) to create a workflow similar to the following diagram.
>
> ![Example CI/CD diagram](media/common/ci-cd-complex.png)

Anyone who opens the public URL and navigates to the web app will invoke the code path you have written which runs through both services using the default _dev_ space. Now suppose you want to continue developing *mywebapi* - how can you do this and not interrupt other developers who are using the dev space? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-new-dev-space"></a>Vytvoření nového vývojového prostoru
V sadě Visual Studio můžete vytvářet další prostory, které se použijí, když pro danou službu stisknete F5 nebo Ctrl+F5. Prostor můžete pojmenovat libovolně (např. _sprint4_ or _demo_).

K vytvoření nového prostoru použijte následující postup:
1. Switch to the Visual Studio window with the *mywebapi* project.
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
3. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
4. Odtud můžete změnit nebo vytvořit cluster nebo prostor, který se použije po stisknutí F5 nebo Ctrl+F5. *Zkontrolujte, že je vybraný vývojový prostor Azure, který jste vytvořili dříve*.
5. In the Space dropdown, select **\<Create New Space…\>** .

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. In the **Add Space** dialog, set the parent space to **dev**, and enter a name for your new space. Můžete pro nový prostor použít svoje jméno (například „scott“), aby bylo vašim kolegům jasné, že se jedná o prostor, ve kterém pracujete vy. Klikněte na **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teď by se měl zobrazit váš cluster AKS a na stránce vlastností projektu by měl být vybraný nový prostor.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualizace kódu pro *mywebapi*

1. In the *mywebapi* project make a code change to the `string Get(int id)` method in file `Controllers/ValuesController.cs` as follows:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Nastavte zarážku v tomto aktualizovaném bloku kódu (jednu už můžete mít nastavenou z dřívějška).
3. Hit F5 to start the _mywebapi_ service, which will start the service in your cluster using the selected space. The selected space in this case is _scott_.

Následující diagram vám pomůže porozumět tomu, jak různé prostory fungují. The purple path shows a request via the _dev_ space, which is the default path used if no space is prepended to the URL. The pink path shows a request via the _dev/scott_ space.

![](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje komplexní testování kódu ve sdíleném prostředí bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-running-in-the-_devscott_-space"></a>Test code running in the _dev/scott_ space
To test your new version of *mywebapi* in conjunction with *webfrontend*, open your browser to the public access point URL for *webfrontend* (for example, http://dev.webfrontend.123456abcdef.eus.azds.io) and go to the About page. Měla by se zobrazit původní zpráva: „Hello from webfrontend and Hello from mywebapi.“

Teď k adrese URL přidejte „scott.s.“. part to the URL so it reads something like http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io and refresh the browser. The breakpoint you set in your *mywebapi* project should get hit. Pokračujte kliknutím na F5. V prohlížeči by se teď měla zobrazit nová zpráva „Hello from webfrontend and mywebapi now says something new.“ This is because the path to your updated code in *mywebapi* is running in the _dev/scott_ space.

Once you have a _dev_ space that always contains your latest changes, and assuming your application is designed to take advantage of DevSpace's space-based routing as described in this tutorial section, hopefully it becomes easy to see how Dev Spaces can greatly assist in testing new features within the context of the larger application. Rather than having to deploy _all_ services to your private space, you can create a private space that derives from _dev_, and only "up" the services you're actually working on. The Dev Spaces routing infrastructure will handle the rest by utilizing as many services out of your private space as it can find, while defaulting back to the latest version running in the _dev_ space. And better still, _multiple_ developers can actively develop different services at the same time in their own space without disrupting each other.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Establish a baseline of functionality using Dev Spaces to easily test isolated changes within the context of a larger microservice application

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
