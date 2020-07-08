---
title: Použití místního procesu s Kubernetes s využitím editoru Visual Studio Code (Preview)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Naučte se používat místní proces s Kubernetes k připojení vašeho vývojového počítače k clusteru Kubernetes s Azure Dev Spaces
keywords: Místní proces pomocí Kubernetes, Azure Dev Spaces, vývojových prostorů, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316737"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Použití místního procesu s Kubernetes s využitím editoru Visual Studio Code (Preview)

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
* [Visual Studio Code][vs-code] běžet na MacOS nebo Windows 10.
* Verze rozšíření [Azure dev Spaces][azds-vs-code] 2.0.220200601 nebo novější je nainstalovaná v Visual Studio Code.
* Je [nainstalovaný Azure dev Spaces CLI][azds-cli].

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

> [!IMPORTANT]
> Aby bylo možné skript spustit, musíte mít ke svému clusteru přístup *vlastníka* nebo *přispěvatele* .

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

Z [ukázkové aplikace Azure dev Spaces pro sdílení kol][bike-sharing-github] v Visual Studio Code otevřete *dev-Spaces/Samples/BikeSharingApp/Bikes* . Otevřete rozšíření služby Azure Kubernetes a vyberte obor názvů pro *vývoj* v clusteru *MyAKS* .

![Vybrat obor názvů](../media/local-process-kubernetes-vs-code/select-namespace.png)

Pomocí `npm install` příkazu nainstalujte závislosti pro aplikaci.

```console
npm install
```

Vyberte ikonu *ladění* na levé straně a v horní části vyberte *místní proces s Kubernetes (Preview)* .

![Výběr místního procesu pomocí Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Klikněte na tlačítko Start vedle *místní proces s Kubernetes (Preview)*. Při prvním spuštění této konfigurace spuštění se zobrazí výzva ke konfiguraci služby, kterou chcete nahradit, portu pro přeposílání z vývojového počítače a spouštěcí úlohy, která se má použít.

Vyberte službu *Bikes* .

![Zvolit službu](../media/local-process-kubernetes-vs-code/choose-service.png)

Veškerý provoz v clusteru Kubernetes se přesměruje pro službu *Bikes* na verzi vaší aplikace spuštěné ve vývojovém počítači. Místní proces s Kubernetes také směruje veškerý odchozí provoz z aplikace zpátky do vašeho clusteru Kubernetes.

> [!IMPORTANT]
> Můžete přesměrovat pouze služby, které mají jeden pod.

Po výběru služby se zobrazí výzva k zadání portu TCP pro místní aplikaci. V tomto příkladu zadejte *3000*.

![Připojit zvolit port](../media/local-process-kubernetes-vs-code/choose-port.png)

Jako spouštěcí úlohu vyberte *Spustit přes npm* .

![Připojit zvolit Spustit úlohu](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Zobrazí se výzva, abyste umožnili *KubernetesDNSManager* spouštění zvýšených oprávnění a změny souboru hostitelů.

Váš vývojový počítač se připojí, když se změní stavový řádek na oranžová a rozšíření pro vývoj prostorů vám ukáže, že jste připojení.

![Připojený vývojový počítač](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> V subesquent se nebudete vyzváni k zadání názvu služby, portu nebo spouštěcí úlohy. Tyto hodnoty jsou uloženy v *. VSCode/tasks.jsna*.

Po připojení k vývojovému počítači spustí provoz přesměrování na váš vývojový počítač pro službu, kterou nahrazujete.

## <a name="set-a-break-point"></a>Nastavení bodu přerušení

Otevřete [server.js][server-js-breakpoint] a kliknutím někam do řádku 233 umístěte kurzor do umístění. Nastavte zarážku pomocí příkazu *F9* nebo kliknutím na tlačítko *Spustit* a *přepínací zarážku*.

Přejděte na ukázkovou aplikaci otevřením veřejné adresy URL. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí. Všimněte si, že se obrázek pro kolo nenačte. Vraťte se na Visual Studio Code a sledujte řádek 233, který je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 233. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *Spustit* a pak *pokračovat*. Vraťte se do prohlížeče a ověřte, že se zobrazí zástupný obrázek pro kolo.

Odstraňte zarážku tak, že umístíte kurzor na řádek 233 a zapnete ho `server.js` *F9*.

### <a name="update-your-application"></a>Aktualizace aplikace

Úpravou `server.js` odstraňte řádky 234 a 235:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Oddíl by teď měl vypadat takto:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Uložte změny a klikněte na tlačítko *Spustit* a *znovu spusťte ladění*. Po opětovném připojení aktualizujte prohlížeč a ověřte, že se vám pro kolo už nezobrazuje zástupný symbol.

Kliknutím na tlačítko *Spustit* *Zastavte ladění* a ukončete ladicí program.

> [!NOTE]
> Ve výchozím nastavení zastavování úlohy ladění také odpojí váš vývojový počítač od clusteru Kubernetes. Toto chování můžete změnit tak, že vyhledáte *místní proces pomocí Kubernetes: po ladění* v nastaveních Visual Studio Code se odpojíte a odeberete zaškrtnutí políčka *automaticky odpojit při ukončení ladění.* Po aktualizaci tohoto nastavení zůstane váš vývojový počítač po zastavení a spuštění ladění připojen. Pokud chcete počítač pro vývoj odpojit od clusteru, klikněte na rozšíření Azure Dev Spaces na stavovém řádku a zvolte *odpojit aktuální relaci*.
>
> Pokud Visual Studio Code náhlé ukončení připojení ke clusteru nebo ukončení, služba, kterou přesměrováváte, nemusí být obnovená do původního stavu předtím, než se připojíte k místnímu procesu pomocí Kubernetes. Chcete-li tento problém vyřešit, přečtěte si [příručku Poradce při potížích][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Používání protokolování a diagnostiky

Po připojení vašeho vývojového počítače k vašemu clusteru Kubernetes se výstup protokolování zapisuje do okna pro *vývojová místa* .

![Výstup](../media/local-process-kubernetes-vs-code/output.png)

Klikněte na stavový řádek Azure Dev Spaces a vyberte *Zobrazit informace o diagnostice připojení*. Tento příkaz vytiskne aktuální proměnné prostředí a celá okna DNS ve výstupu protokolování.

![Výstup s diagnostikou](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Kromě toho můžete najít diagnostické protokoly v `Azure Dev Spaces` adresáři v [ *dočasném* adresáři vašeho vývojového počítače][azds-tmp-dir].

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
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download