---
title: " Připojení vývojového počítače k AKS clusteru"
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Zjistěte, jak připojit vývojový počítač k AKS clusteru pomocí Azure Dev Spaces
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 15e46fba19d92e08a9c87a63f6f01ec6b0836910
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888913"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Připojení vývojového počítače ke clusteru AKS (Preview)

Azure Dev Spaces umožňuje spouštět a ladit kód s kontejnerem ve vývojovém počítači nebo bez něj, a to i nadále připojen ke clusteru Kubernetes se zbytkem vaší aplikace nebo služeb. Připojení vývojového počítače k vašemu clusteru vám pomůže rychle vyvíjet aplikace a provádět komplexní testování bez nutnosti vytvářet žádné Kubernetes nebo konfiguraci Docker nebo konfigurace. Můžete se taky připojit ke clusteru AKS, aniž by to ovlivnilo jiné úlohy nebo uživatele, kteří můžou používat stejný cluster.

Azure Dev Spaces přesměruje provoz mezi připojeným clusterem AKS a vývojovým počítačem. Toto přesměrování provozu umožňuje kódu na vašem vývojovém počítači a službách spuštěných v clusteru AKS komunikovat jako v případě, že jsou ve stejném clusteru AKS. Vzhledem k tomu, že váš kód běží na vašem vývojovém počítači, máte také flexibilitu v vývojářských nástrojích, které používáte ke spouštění a ladění kódu. Azure Dev Spaces taky poskytuje způsob, jak replikovat proměnné prostředí a připojené soubory, které jsou dostupné pro lusky ve vašem clusteru AKS ve vývojovém počítači.

V tomto průvodci se naučíte:

* Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
* Nasaďte rozsáhlou aplikaci s více mikroslužbami do vývojového prostoru.
* Pomocí Azure Dev Spaces můžete přesměrovat provoz mezi clusterem AKS a kódem běžícím na vašem vývojovém počítači.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Než začnete

Tato příručka používá [ukázkovou aplikaci pro sdílení kol Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení připojení vývojového počítače k AKS clusteru. Pokud chcete spustit ukázkovou aplikaci, postupujte podle pokynů v [souboru Readme ukázek aplikace Azure dev Spaces sdílení kolací](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) . Případně, pokud máte vlastní aplikaci v clusteru AKS, můžete postupovat podle následujících kroků a používat názvy vlastních služeb a lusků.

### <a name="limitations"></a>Omezení

* Protokol UDP není v tuto chvíli podporován.

### <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli]
* [Visual Studio Code][vs-code] s nainstalovaným rozšířením [Azure dev Spaces][azds-vs-code] a spuštěným v MacOS nebo Windows 10.
* [Ukázková aplikace Azure dev Spaces pro sdílení kol](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) nebo vlastní aplikace běžící v clusteru AKS.

## <a name="connect-your-development-machine"></a>Připojení vývojového počítače

Otevřete okno *dev-Spaces/Samples/BikeSharingApp/Bikes* in Visual Studio Code a pomocí rozšíření Azure dev Spaces Připojte svůj vývojový počítač k vašemu clusteru AKS.

Chcete-li použít rozšíření Azure Dev Spaces, otevřete paletu příkazů v Visual Studio Code kliknutím na tlačítko *Zobrazit* a *paleta příkazů*. Začněte psát `Azure Dev Spaces: Redirect` a klikněte buď na `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`nebo `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Příkazy](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Výběr možnosti přesměrování

Pokud `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`spustíte, budete požádáni o výběr existující služby Kubernetes:

![Zvolit službu](../media/how-to-connect/connect-choose-service.png)

Tato možnost přesměruje veškerý provoz v clusteru AKS pro tuto službu na verzi vaší aplikace spuštěné ve vývojovém počítači. Pokud má tato služba více prostředí spuštěných v clusteru AKS, veškerý provoz této služby je směrován pouze do vašeho vývojového počítače. Azure Dev Spaces také směruje veškerý odchozí provoz z aplikace zpátky do vašeho clusteru AKS.

Pokud spustíte `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, budete požádáni o výběr konkrétního pod:

![Zvolit pod](../media/how-to-connect/connect-choose-pod.png)

Tato možnost se připojí k určitému pod. Tato možnost je užitečná pro interakci s lusky, které neodesílají ani nepřijímaly provoz a replikují ukončené lusky. V případě, že v poli pod odesílá a přijímá přenos, tato možnost se chová podobným způsobem `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` a přesměruje veškerý provoz v clusteru AKS pro všechny lusky související se službou vybraného seznamu pod.

Pokud spustíte `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, nebudete vyzváni k výběru existujícího pod nebo služba. Tato možnost přesměruje veškerý odchozí provoz z aplikace spuštěné na vašem vývojovém počítači do clusteru AKS.

V tomto příkladu zvolte možnost `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` a vyberte službu *Bikes* .

### <a name="select-a-connection-mode"></a>Vyberte režim připojení.

Po výběru možnosti přesměrování se zobrazí výzva k výběru režimu připojení *nahradit* nebo *klonovat* .

![Nahrazení nebo klonování](../media/how-to-connect/connect-replace-clone.png)

Možnost *nahradit* nahrazuje aktuální položku pod nebo službu v clusteru AKS a přesměruje veškerý provoz této služby do vašeho vývojového počítače. Tato možnost může být narušena na jiné služby v clusteru AKS, které komunikují se službou, kterou přesměrováváte, nemusí fungovat, dokud nespustíte aplikaci na vývojovém počítači. Možnost *klonování* vám umožňuje zvolit existující podřízený prostor pro vývoj nebo vytvořit nový podřízený vývojový prostor pro přesměrování provozu pro uzel pod nebo službu do vývojového počítače. Tato možnost umožňuje pracovat v izolaci a Nerušit jiné služby, protože do vašeho vývojového počítače budou přesměrovány jenom přenosy do tohoto podřízeného vývojového prostoru. Možnost *klonování* vyžaduje, aby cluster AKS měl povolený Azure dev Spaces.

V tomto příkladu vyberte *nahradit*.

> [!NOTE]
> Pokud má již vaše stávající služba více kontejnerů, zobrazí se také výzva k výběru kontejneru aplikace.

### <a name="select-a-port-for-your-application"></a>Vyberte port pro vaši aplikaci.

Po výběru režimu připojení budete vyzváni k zadání portu TCP vaší místní aplikace. Pokud vaše aplikace otevře více portů, oddělte je čárkami, například *80, 81*. Pokud aplikace nepřijímá žádné síťové požadavky, zadejte *hodnotu 0*. V tomto příkladu zadejte *3000*.

![Připojit zvolit port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Potvrďte, že jste připojení.

Po výběru portu TCP vaší aplikace Azure Dev Spaces naváže připojení ke clusteru AKS. Azure Dev Spaces do clusteru AKS vloží agenta pro přesměrování provozu mezi clusterem AKS a vývojovým počítačem. Vytvoření tohoto připojení může trvat několik minut. Azure Dev Spaces také požádá o přístup správce, aby mohl upravovat soubor *hostitelů* ve vývojovém počítači.

> [!IMPORTANT]
> Jakmile Azure Dev Spaces naváže připojení ke clusteru AKS, nemusí ostatní služby v clusteru AKS fungovat správně, dokud službu nespustíte ve vývojovém počítači, pokud zvolíte možnost *nahradit* režim připojení. Místo toho můžete zvolit režim připojení *klonování* pro vytvoření podřízeného vývojového prostoru pro přesměrování a vyhnout se případnému přerušení v nadřazeném prostoru. Pokud má vaše služba závislost, která není ve vývojovém počítači k dispozici, může být nutné upravit svou aplikaci nebo zadat [Další konfiguraci](#additional-configuration) .

Po navázání připojení ke clusteru AKS otevře Azure Dev Spaces okno terminálu s názvem *AZDS Connect-Bikes* . Toto okno terminálu obsahuje všechny proměnné prostředí a položky DNS nakonfigurované z clusteru AKS. Jakýkoli kód, který spustíte v tomto okně terminálu nebo pomocí ladicího programu Visual Studio Code, je připojen ke clusteru AKS.

![Terminálovému](../media/how-to-connect/connect-terminal.png)

Kromě toho Azure Dev Spaces vytvoří okno s názvem *vývojářské prostory připojit* se ke všem jeho výstupům.

![Výstup](../media/how-to-connect/connect-output.png)

Azure Dev Spaces má také položku stavového řádku, která zobrazuje stav připojení.

![Status](../media/how-to-connect/connect-status.png)

Ověřte, že se ve stavovém řádku zobrazují *vývojové prostory: připojeno k vývojovým/bicyklům na místním portu 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Konfigurace aplikace na vývojovém počítači

Otevřete okno terminálu *AZDS Connect-Bikes* a spusťte `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```


Klikněte na *ladit* a pak *Otevřete konfigurace*. Pokud se zobrazí výzva k výběru prostředí, zvolte *Node. js*. Tím se vytvoří soubor `.vscode/launch.json`. Obsah tohoto souboru nahraďte následujícím:

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Otevřete soubor [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) a přidejte ladicí skript:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Spuštění aplikace na vývojovém počítači

Klikněte na ikonu *ladění* na levé straně a potom klikněte na tlačítko Start vedle tlačítka Spustit *přes npm* v horní části.

![Spustit přes NPM](../media/how-to-connect/launch-npm.png)

Vaše aplikace spustí a Azure Dev Spaces přesměruje provoz mezi vaším clusterem AKS a vývojovým počítačem. V *konzole ladění*se zobrazí zprávy podobné následujícímu:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Přejděte do služby *bikesharingweb* tak, že kliknete na stavový řádek Azure dev Spaces a zvolíte veřejnou adresu URL vaší aplikace. Veřejnou adresu URL můžete najít také z příkazu `azds list-uris`, který jste spustili dříve. Pokud ve vašem clusteru nepoužíváte Azure Dev Spaces, použijte IP adresu nebo adresu URL pro aplikaci pro obor názvů, který používáte. Ve výše uvedeném příkladu je veřejná adresa URL pro službu *bikesharingweb* `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí.

### <a name="set-a-break-point"></a>Nastavení bodu přerušení

Otevřete [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) a Kliknutím kamkoli na řádku 233 umístěte kurzor do umístění. Nastavte zarážku zavoláním *F9* nebo kliknutím na *ladění* a potom *přepínací zarážku*.

Přejděte do služby *bikesharingweb* otevřením veřejné adresy URL. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí. Všimněte si, že se obrázek pro kolo nenačte. Vraťte se na Visual Studio Code a sledujte řádek 233, který je zvýrazněný. Zarážka, kterou jste nastavili, pozastavila službu na řádku 233. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a ověřte, že se zobrazí zástupný obrázek pro kolo.

Odstraňte zarážku tak, že umístíte kurzor na řádek 233 v `server.js` a zapnete *F9*.

### <a name="update-your-application"></a>Aktualizace aplikace

Upravte `server.js` a odeberte řádky 232 a 233:

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

Uložte změny a klikněte na *ladit* a pak *znovu spusťte ladění*. Aktualizujte si prohlížeč a ověřte, že se už nezobrazuje zástupný obrázek pro kolo.

Klikněte na *ladit* a pak *Zastavit ladění* , aby se ladicí program zastavil. Kliknutím na stavový řádek Azure Dev Spaces se odpojíte od clusteru AKS.

## <a name="additional-configuration"></a>Další konfigurace

Azure Dev Spaces může zpracovávat směrování provozu a replikovat proměnné prostředí bez jakékoli další konfigurace. Potřebujete-li stáhnout všechny soubory, které jsou připojeny ke kontejneru v clusteru AKS, jako je například soubor ConfigMap, můžete vytvořit `azds-local.env` ke stažení těchto souborů do vývojového počítače.

Tady je příklad `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Další kroky

Naučte se, jak pomocí akcí Azure Dev Spaces a GitHubu testovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o získání dat do hlavní větve úložiště.

> [!div class="nextstepaction"]
> [Akce GitHubu & službě Azure Kubernetes][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download