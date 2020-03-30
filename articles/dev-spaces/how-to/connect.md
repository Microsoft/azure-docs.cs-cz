---
title: Připojení vývojového počítače ke clusteru AKS (preview)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Zjistěte, jak připojit vývojový počítač ke clusteru AKS pomocí Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235006"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Připojení vývojového počítače ke clusteru AKS (preview)

Azure Dev Spaces umožňuje spouštět a ladit kód s kontejnerem nebo bez kontejneru ve vývojovém počítači, zatímco je stále připojený ke clusteru Kubernetes se zbytkem vaší aplikace nebo služeb. Připojení vývojového počítače ke clusteru vám pomůže rychle vyvinout aplikaci a provést komplexní testování bez nutnosti vytvářet konfiguraci Dockeru nebo Kubernetes. Můžete se také připojit ke clusteru AKS bez ovlivnění jiných úloh nebo uživatelů, kteří mohou používat stejný cluster.

Azure Dev Spaces přesměruje provoz mezi připojeným clusterem AKS a vývojovým počítačem. Toto přesměrování provozu umožňuje kódu vývojového počítače a služeb spuštěných v clusteru AKS komunikovat, jako by byly ve stejném clusteru AKS. Vzhledem k tomu, že váš kód je spuštěn ve vývojovém počítači, máte také flexibilitu ve vývojových nástrojích, které používáte ke spuštění a ladění tohoto kódu. Azure Dev Spaces také poskytuje způsob, jak replikovat proměnné prostředí a připojené soubory, které jsou k dispozici pro pody v clusteru AKS ve vývojovém počítači.

V tomto průvodci se naučíte:

* Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
* Nasazení velké aplikace s více mikroslužeb do prostoru pro vývoj.
* Azure Dev Spaces slouží k přesměrování provozu mezi clusterem AKS a kódem spuštěným ve vývojovém počítači.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Než začnete

Tato příručka používá [ukázkovou aplikaci Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) k předvedení připojení vývojového počítače ke clusteru AKS. Podle pokynů v [ukázkové aplikaci Azure Dev Spaces Bike Sharing spusťte](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) ukázkovou aplikaci. Případně pokud máte vlastní aplikaci v clusteru AKS, můžete stále postupovat podle následujících kroků a používat názvy vlastních služeb a podů.

### <a name="limitations"></a>Omezení

* UDP není v tuto chvíli podporována.

### <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli]
* [Visual Studio Code][vs-code] s nainstalované a spuštěné v MacOS nebo Windows 10 s nainstalovaným a spuštěným [rozšířením Azure Dev Spaces.][azds-vs-code]
* [Ukázková aplikace Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) nebo vaše vlastní aplikace spuštěná v clusteru AKS.

## <a name="connect-your-development-computer"></a>Připojení vývojového počítače

Otevřete *dev-spaces/samples/BikeSharingApp/Bikes* v kódu Visual Studia a pomocí rozšíření Azure Dev Spaces připojte vývojový počítač k clusteru AKS.

Chcete-li použít rozšíření Azure Dev Spaces, otevřete paletu příkazů v kódu sady Visual Studio klepnutím na *tlačítko Zobrazit* a potom *paletu příkazů*. Začněte `Azure Dev Spaces: Redirect` psát a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`klikněte na položku , `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`nebo `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Příkazy](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Výběr možnosti přesměrování

Pokud spustíte `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, budete vyzváni k výběru stávající služby Kubernetes:

![Zvolte službu](../media/how-to-connect/connect-choose-service.png)

Tato možnost přesměruje veškerý provoz v clusteru AKS pro tuto službu na verzi aplikace spuštěné ve vývojovém počítači. Pokud má tato služba v clusteru AKS spuštěno více podů, veškerý provoz pro tuto službu je směrován pouze do vývojového počítače. Azure Dev Spaces také směruje všechny odchozí provozy z aplikace zpět do clusteru AKS.

Pokud spustíte `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, budete vyzváni k výběru konkrétního podu:

![Zvolte Pod](../media/how-to-connect/connect-choose-pod.png)

Tato možnost se připojí ke konkrétnímu podu. Tato možnost je užitečná pro interakci s pody, které neodesílají ani nepřijímají přenosy a replikují ukončené pody. Pokud pod neodesílá a přijímá přenosy, tato možnost `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` se chová podobným způsobem a přesměruje veškerý provoz v clusteru AKS pro všechny pody související se službou vybraného podu.

Pokud spustíte `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, nebudete vyzváni k výběru existujícípod nebo služby. Tato možnost přesměruje veškerý odchozí provoz z aplikace spuštěné ve vývojovém počítači do clusteru AKS.

V tomto příkladu vyberte `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` a vyberte službu *jízdních kol.*

### <a name="select-a-connection-mode"></a>Výběr režimu připojení

Po výběru možnosti přesměrování budete vyzváni k výběru režimu *připojení Nahradit* nebo *Klonovat.*

![Nahradit nebo klonovat](../media/how-to-connect/connect-replace-clone.png)

Možnost *Nahradit* nahradí aktuální pod nebo službu v clusteru AKS a přesměruje veškerý provoz pro tuto službu do vývojového počítače. Tato možnost může rušivě rušivě pro jiné služby v clusteru AKS, které interagují se službou, kterou přesměrováváte, nemusí fungovat, dokud nespustíte aplikaci ve vývojovém počítači. Možnost *Klonování* umožňuje zvolit existující podřízený dev prostor nebo vytvořit nový podřízený dev prostor pro přesměrování provozu pro pod nebo službu do vývojového počítače. Tato možnost umožňuje pracovat izolovaně a nenarušovat jiné služby, protože pouze přenos do tohoto podřízeného vývojového prostoru bude přesměrován do vývojového počítače. Možnost *Klonování* vyžaduje, aby váš cluster AKS měl povolenou funkci Azure Dev Spaces.

V tomto příkladu zvolte *Nahradit*.

> [!NOTE]
> Pokud pod vaší existující služby má více kontejnerů, budete také vyzváni k výběru kontejneru aplikace.

### <a name="select-a-port-for-your-application"></a>Výběr portu pro vaši aplikaci

Po výběru režimu připojení budete vyzváni k zadání portu TCP místní aplikace. Pokud aplikace otevře více portů, oddělte je čárkou například *80,81*. Pokud aplikace nepřijímá žádné síťové požadavky, zadejte *hodnotu 0*. V tomto příkladu zadejte *3000*.

![Připojit vybrat port](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Potvrďte, že jste připojeni

Po výběru portu TCP aplikace Azure Dev Spaces naváže připojení ke clusteru AKS. Azure Dev Spaces vloží agenta do clusteru AKS k přesměrování provozu mezi clusterem AKS a vývojovým počítačem. Navázání tohoto spojení může trvat několik minut. Azure Dev Spaces bude také požadovat přístup správce za účelem *úpravy* souboru hosts ve vývojovém počítači.

> [!IMPORTANT]
> Jakmile Azure Dev Spaces naváže připojení k clusteru AKS, ostatní služby v clusteru AKS nemusí fungovat správně, dokud nespustíte službu ve vývojovém počítači, pokud zvolíte režim *nahrazení* připojení. Místo toho můžete zvolit režim připojení *Klonování,* chcete-li vytvořit podřízený dev prostor pro přesměrování a vyhnout se narušení nadřazeného prostoru. Pokud má vaše služba závislost, která není k dispozici ve vývojovém počítači, bude pravděpodobně nutné upravit aplikaci nebo poskytnout [další konfiguraci.](#additional-configuration)

Azure Dev Spaces otevře okno terminálu s názvem *AZDS Connect – kola* po navázání připojení k clusteru AKS. Toto okno terminálu má všechny proměnné prostředí a položky DNS nakonfigurované z clusteru AKS. Veškerý kód, který spustíte v tomto terminálovém okně nebo pomocí ladicího programu kódu sady Visual Studio, je připojen ke clusteru AKS.

![Terminálové](../media/how-to-connect/connect-terminal.png)

Azure Dev Spaces navíc vytvoří okno s názvem *Dev Spaces Connect* se všemi jeho výstupem.

![Výstup](../media/how-to-connect/connect-output.png)

Azure Dev Spaces má také položku stavového řádku zobrazující stav připojení.

![Status](../media/how-to-connect/connect-status.png)

Ověřte, že na stavovém řádku se zobrazuje *dev Spaces: Connected to dev/bikes na místním portu 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Konfigurace aplikace ve vývojovém počítači

Otevřete okno terminálu *AZDS* `npm install`Connect - Bikes a spusťte :

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Klepněte na tlačítko *Ladění* a *potom na položku Otevřít konfiguraci*. Pokud se zobrazí výzva k výběru prostředí, zvolte *Node.js*. Tím se `.vscode/launch.json` vytvoří soubor. Nahraďte obsah tohoto souboru následujícím:

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

Otevřete [soubor package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) a přidejte ladicí skript:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Spuštění aplikace ve vývojovém počítači

Klikněte na ikonu *Ladění* vlevo a klikněte na tlačítko Start vedle *spustit přes NPM* v horní části.

![Spuštění přes NPM](../media/how-to-connect/launch-npm.png)

Spustí se vaše aplikace a Azure Dev Spaces přesměruje provoz mezi clusterem AKS a vývojovým počítačem. Zobrazí se zprávy podobné níže v *Ladicí konzole*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Přejděte na webovou službu *bikesharing* web kliknutím na stavový řádek Azure Dev Spaces a výběrem veřejné adresy URL vaší aplikace. Veřejnou adresu URL můžete `azds list-uris` také najít z příkazu, který jste spustili dříve. Pokud v clusteru nepoužíváte Azure Dev Spaces, použijte IP adresu nebo adresu URL aplikace pro obor názvů, který používáte. Ve výše uvedeném příkladu je `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`veřejná adresa URL služby *bikesharingweb* . Vyberte *Aurelia Briggs (zákazník)* jako uživatel, pak vyberte kolo k pronájmu.

### <a name="set-a-break-point"></a>Nastavení bodu přerušení

Otevřete [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) a klikněte někde na řádku 233, aby váš kurzor tam. Nastavte zarážku tak, že kliknete na *F9* nebo klepnete na *tlačítko Ladění* a potom *přepnete zarážku*.

Přejděte na službu *bikesharingweb* otevřením veřejné adresy URL. Vyberte *Aurelia Briggs (zákazník)* jako uživatel, pak vyberte kolo k pronájmu. Všimněte si, že obraz kola se nenačte. Návrat do visual studio kód a sledovat řádek 233 je zvýrazněna. Nastavená zarážka pozastavila službu na řádku 233. Chcete-li službu obnovit, stiskněte klávesu *F5* nebo klepněte na tlačítko *Ladění* *a potom pokračujte*. Vraťte se do prohlížeče a ověřte, zda se zobrazí zástupný obrázek kola.

Odstraňte zarážku tím, že kurzor na řádku 233 a `server.js` bít *F9*.

### <a name="update-your-application"></a>Aktualizace aplikace

Chcete-li `server.js` odebrat řádky 232 a 233, upravte:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekce by nyní měla vypadat takto:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Uložte změny a klepněte na tlačítko *Ladění* *a potom restartujte ladění*. Aktualizujte prohlížeč a ověřte, zda již nevidíte zástupný obrázek kola.

*Chcete-li* ladicí program zastavit, klepněte na tlačítko Ladění a potom *zastavte ladění.* Kliknutím na stavový řádek Azure Dev Spaces se odpojíte od clusteru AKS.

## <a name="additional-configuration"></a>Další konfigurace

Azure Dev Spaces zvládne směrování a replikuje proměnné prostředí bez jakékoli další konfigurace. Pokud potřebujete stáhnout všechny soubory, které jsou připojeny ke kontejneru v clusteru AKS, `azds-local.env` jako je například soubor ConfigMap, můžete vytvořit a stáhnout tyto soubory do vývojového počítače.

Zde je `azds-local.env`příklad :

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Použití protokolování a diagnostiky

Protokolování výstupu se zapíše do okna *Dev Spaces Connect* po připojení vývojového počítače ke clusteru AKS.

![Výstup](../media/how-to-connect/connect-output.png)

Klikněte na stavový řádek Azure Dev Spaces a zvolte *Zobrazit diagnostické informace*. Tento příkaz vytiskne aktuální proměnné prostředí a dns celé ve výstupu protokolování.

![Výstup s diagnostikou](../media/how-to-connect/connect-output-diagnostics.png)

Diagnostické protokoly můžete navíc najít `Azure Dev Spaces` v adresáři v [adresáři *TEMP* vývojového počítače][azds-tmp-dir].

## <a name="next-steps"></a>Další kroky

Zjistěte, jak pomocí Azure Dev Spaces a Akce GitHubu otestovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o přijetí změn do hlavní větve vašeho úložiště.

> [!div class="nextstepaction"]
> [Akce GitHubu & službu Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download