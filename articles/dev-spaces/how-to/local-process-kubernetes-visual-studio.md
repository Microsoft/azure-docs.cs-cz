---
title: Použití místního procesu s Kubernetes s využitím sady Visual Studio (Preview)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Naučte se používat místní proces s Kubernetes a sadou Visual Studio k připojení vašeho vývojového počítače k clusteru Kubernetes s Azure Dev Spaces
keywords: Místní proces pomocí Kubernetes, Azure Dev Spaces, vývojových prostorů, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316800"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Použití místního procesu s Kubernetes s využitím sady Visual Studio (Preview)

Místní proces s Kubernetes umožňuje spouštět a ladit kód na vašem vývojovém počítači, ale stále je připojený ke svému clusteru Kubernetes se zbytkem vaší aplikace nebo služeb. Například pokud máte rozsáhlou architekturu mikroslužeb s mnoha vzájemně závislými službami a databázemi, může být obtížné replikovat tyto závislosti na vašem vývojovém počítači. Kromě toho může být při vytváření a nasazování kódu do clusteru Kubernetes pro každou změnu kódu během vývoje ve vnitřní smyčce pomalý, časově náročný a obtížný použití s ladicím programem.

Místní proces s Kubernetes zabraňuje sestavení a nasazení kódu do clusteru, a to tak, že vytvoří připojení přímo mezi vývojovým počítačem a vaším clusterem. Připojení vývojového počítače ke clusteru během ladění umožňuje rychlou otestování a vývoj vaší služby v kontextu plné aplikace bez vytvoření jakékoli konfigurace Docker nebo Kubernetes.

Místní proces s Kubernetes přesměruje provoz mezi připojeným clusterem Kubernetes a vaším vývojovým počítačem. Toto přesměrování provozu umožňuje kódu na vašem vývojovém počítači a službách spuštěných v clusteru Kubernetes komunikovat jako v případě, že jsou ve stejném clusteru Kubernetes. Místní proces s Kubernetes také poskytuje způsob, jak replikovat proměnné prostředí a připojené svazky k dispozici do lusků ve vašem clusteru Kubernetes ve vývojovém počítači. Poskytnutí přístupu k proměnným prostředí a připojeným svazkům ve vývojovém počítači vám umožní rychle pracovat na svém kódu, aniž byste tyto závislosti museli replikovat ručně.

V této příručce se dozvíte, jak pomocí místního procesu s Kubernetes přesměrovat provoz mezi clusterem Kubernetes a kódem běžícím na vašem vývojovém počítači. Tato příručka také poskytuje skript pro nasazení rozsáhlé ukázkové aplikace s více mikroslužbami v clusteru Kubernetes.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][preview-terms]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Než začnete

Tato příručka používá [ukázkovou aplikaci Azure dev Spaces pro sdílení kol][bike-sharing-github] k předvedení připojení vývojového počítače ke clusteru Kubernetes. Pokud už máte svoji vlastní aplikaci spuštěnou v clusteru Kubernetes, můžete postupovat podle následujících kroků a používat i názvy vlastních služeb.

### <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli]
* [Visual Studio 2019][visual-studio] verze 16,7 Preview 2 nebo vyšší, která běží ve Windows 10 s nainstalovanými úlohami *vývoje ASP.NET a web* a *vývoj pro Azure* .
* Je [nainstalovaný Azure dev Spaces CLI][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>Povolení místního procesu pomocí funkce Kubernetes ve verzi Preview v aplikaci Visual Studio

Pokud chcete povolit místní proces pomocí Kubernetes v aplikaci Visual Studio, klikněte na *nástroje*  >  *Možnosti*  >  *prostředí*  >  *verze Preview*. Vyberte *Povolit místní ladění pro služby Kubernetes Services*.

Také pro konzolové aplikace .NET nainstalujte balíček NuGet *Microsoft. VisualStudio. Azure. Kubernetes. Tools. targets* .

## <a name="create-a-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes

Vytvořte cluster AKS v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Instalace ukázkové aplikace

Nainstalujte do clusteru ukázkovou aplikaci pomocí poskytnutého skriptu. Tento skript můžete spustit na svém vývojovém počítači nebo pomocí [Azure Cloud Shell][azure-cloud-shell].

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Přejděte do ukázkové aplikace, ve které je spuštěný cluster, otevřením jeho veřejné adresy URL, která se zobrazí ve výstupu instalačního skriptu.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

Ve výše uvedeném příkladu je veřejná adresa URL `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Připojení ke clusteru a ladění služby

Ve vývojovém počítači stáhněte a nakonfigurujte rozhraní příkazového řádku Kubernetes pro připojení k vašemu clusteru Kubernetes pomocí [AZ AKS Get-Credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Otevřete *dev-Spaces/Samples/BikeSharingApp/ReservationEngine/App. csproj* z [ukázkové aplikace sdílení kolace Azure dev Spaces][bike-sharing-github] v aplikaci Visual Studio.

V projektu vyberte v rozevíracím seznamu nastavení spuštění možnost *místní proces pomocí Kubernetes* , jak je znázorněno níže.

![Výběr místního procesu pomocí Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Klikněte na tlačítko Start vedle *místní proces pomocí Kubernetes*. V dialogovém okně *místní proces s Kubernetes* :

* Vyberte své předplatné.
* Pro svůj cluster Vyberte *MyAKS* .
* Vyberte *vývoj* pro svůj obor názvů.
* Pro přesměrování služby vyberte *reservationengine* .
* Vyberte *aplikaci* pro spouštěcí profil.
* Vyberte `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` adresu URL pro spuštění prohlížeče.

![Výběr místního procesu pomocí clusteru Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Můžete přesměrovat pouze služby, které mají jeden pod.

Klikněte na *Uložit a spusťte ladění*.

Veškerý provoz v clusteru Kubernetes se přesměruje do služby *reservationengine* na verzi vaší aplikace spuštěné ve vývojovém počítači. Místní proces s Kubernetes také směruje veškerý odchozí provoz z aplikace zpátky do vašeho clusteru Kubernetes.

> [!NOTE]
> Zobrazí se výzva, abyste umožnili *KubernetesDNSManager* spouštění zvýšených oprávnění a změny souboru hostitelů.

Váš vývojový počítač se připojí, když se na stavovém řádku zobrazí, že jste připojeni ke službě *reservationengine* .

![Připojený vývojový počítač](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> V subesquent se nebudete vyzváni k *místnímu procesu pomocí* dialogového okna Kubernetes. Tato nastavení aktualizujete v podokně *ladění* ve vlastnostech projektu.

Po připojení k vývojovému počítači spustí provoz přesměrování na váš vývojový počítač pro službu, kterou nahrazujete.

## <a name="set-a-break-point"></a>Nastavení bodu přerušení

Otevřete [BikesHelper.cs][bikeshelper-cs-breakpoint] a Kliknutím kamkoli na řádku 26 umístěte kurzor do umístění. Nastavte zarážku zavoláním *F9* nebo kliknutím na *ladění* a potom *přepínací zarážku*.

Přejděte na ukázkovou aplikaci otevřením veřejné adresy URL. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí. Klikněte na *pronajímat kolo*. Vraťte se do sady Visual Studio a sledujte řádek 26, který je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 26. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a ověřte, že se na stránce zobrazuje, že jste toto kolo zapůjčujíi.

Odstraňte zarážku tak, že umístíte kurzor na řádek 26 v a zapnete `BikesHelper.cs` *F9*.

> [!NOTE]
> Ve výchozím nastavení zastavování úlohy ladění také odpojí váš vývojový počítač od clusteru Kubernetes. Toto chování můžete změnit změnou možnosti *Odpojit po ladění* na *false* v části *ladicí nástroje Kubernetes* v možnostech ladění. Po aktualizaci tohoto nastavení zůstane váš vývojový počítač po zastavení a spuštění ladění připojen. Pokud chcete odpojit svůj vývojový počítač od svého clusteru, klikněte na tlačítko *Odpojit* na panelu nástrojů.
>
> Pokud Visual Studio náhle ukončí připojení ke clusteru nebo skončí, služba, kterou přesměrováváte, nemusí být obnovená do původního stavu předtím, než se připojíte k místnímu procesu pomocí Kubernetes. Chcete-li tento problém vyřešit, přečtěte si [příručku Poradce při potížích][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Používání protokolování a diagnostiky

Diagnostické protokoly můžete najít v `Azure Dev Spaces` adresáři v [ *dočasném* adresáři vašeho vývojového počítače][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Odebrání ukázkové aplikace z clusteru

Pomocí poskytnutého skriptu odeberte ukázkovou aplikaci z clusteru.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Další kroky

Naučte se, jak pomocí akcí Azure Dev Spaces a GitHubu testovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o získání dat do hlavní větve úložiště.

> [!div class="nextstepaction"]
> [Akce GitHubu & službě Azure Kubernetes][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/