---
title: Ladění a iterace pomocí Visual Studio Code a Node. js v Kubernetes pomocí Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Rychlý vývoj Kubernetes pomocí kontejnerů, mikroslužeb a Node. js v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b53e18d4b759bc86dd0bc8f913e02b9523fb328b
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815815"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-code-and-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Rychlý Start: ladění a iterace pomocí Visual Studio Code a Node. js v Kubernetes pomocí Azure Dev Spaces

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativní vývoj kódu v kontejnerech pomocí Visual Studio Code.
- Ladit kód ve vývojovém prostoru z Visual Studio Code.

Azure Dev Spaces taky umožňuje ladění a iteraci pomocí:
- [Java a Visual Studio Code](quickstart-java.md)
- [.NET Core a Visual Studio Code](quickstart-netcore.md)
- [.NET Core a Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Visual Studio Code nainstalován](https://code.visualstudio.com/download).
- Rozšíření [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) pro Visual Studio Code nainstalovaná.
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí příkazu `use-dev-spaces` povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří *výchozí* místo pro vývoj.

> [!NOTE]
> Příkaz `use-dev-spaces` nainstaluje také Azure Dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku pomocí [ukázkové aplikace Azure dev Spaces](https://github.com/Azure/dev-spaces) předvádíte použití Azure dev Spaces.

Naklonujte aplikaci z GitHubu.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Příprava ukázkové aplikace v Visual Studio Code

Otevřete Visual Studio Code, klikněte na *soubor* a pak na otevřít *...* , přejděte do adresáře *dev-Spaces/Samples/NodeJS/Začínáme/webendu* a klikněte na *otevřít*.

Nyní máte projekt *webendu* otevřený v Visual Studio Code. Chcete-li aplikaci spustit ve vývojovém prostoru, vygenerujte pomocí rozšíření Azure Dev Spaces v paletě příkazů prostředky Docker a Helm Chart.

Chcete-li otevřít paletu příkazů v Visual Studio Code, klikněte na tlačítko *Zobrazit* *paletu příkazů*. Začněte psát `Azure Dev Spaces` a klikněte na `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když Visual Studio Code také vyzve ke konfiguraci veřejného koncového bodu, vyberte `Yes` pro povolení veřejného koncového bodu.

![Vybrat veřejný koncový bod](media/common/select-public-endpoint.png)

Tento příkaz připraví projekt tak, aby běžel v Azure Dev Spaces generováním grafu typu souboru Dockerfile a Helm. Také generuje adresář *. VSCode* s konfigurací ladění v kořenovém adresáři projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavování a spouštění kódu v Kubernetes z Visual Studio Code

Klikněte na ikonu *ladění* vlevo a v horní části klikněte na *spustit server (AZDS)* .

![Spustit server](media/get-started-node/debug-configuration-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. V dolní části okna *terminálu* se zobrazuje výstup sestavení a adresy URL pro vaši službu běžící Azure dev Spaces. *Konzola ladění* zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev Spaces v *paletě příkazů*, ujistěte se, že máte nainstalovanou [příponu Visual Studio Code pro Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, že jste v Visual Studio Code otevřeli adresář *dev-Spaces/Samples/NodeJS/Začínáme/webendu* .

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

Klikněte na *ladit* a pak *Zastavit ladění* , aby se ladicí program zastavil.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit *spouštěcí Server*. Například:

1. Pokud je vaše aplikace stále spuštěná, klikněte na tlačítko *ladění* a zastavte tak *ladění* .
1. Aktualizujte [řádek 13 v `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) na:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spusťte *spouštěcí Server*.
1. Přejděte do spuštěné služby a sledujte své změny.
1. Klikněte na *ladit* a pak *Zastavit ladění* a zastavte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu pomocí *spouštěcího serveru (AZDS)* .

Kliknutím na tlačítko *Zobrazit* a *Průzkumník*přejděte zpět do zobrazení *Průzkumníka* . Otevřete `server.js` a Kliknutím kamkoli na řádku 13 umístěte kurzor do umístění. Chcete-li nastavit zarážku *F9* nebo klikněte na položku *ladit* a pak *Přepnout zarážku*.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se na Visual Studio Code a sledujte, že je zvýrazněna řádka 13. Zarážka, kterou jste nastavili, službu pozastavila na řádku 13. Pokud chcete službu obnovit, stiskněte klávesu *F5* nebo klikněte na *ladit* a pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 13 v `server.js` a zapnete *F9*.

Klikněte na *ladit* a pak *Zastavit ladění* , aby se ladicí program zastavil.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z Visual Studio Code

Změňte režim ladění tak, aby se *připojil k serveru (AZDS)* , a spusťte službu:

![](media/get-started-node/attach-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. Také spustí proces [nodemon](https://nodemon.io) v kontejneru služby a připojí k němu vs Code. Proces *nodemon* umožňuje automatické restartování při změně zdrojového kódu a umožňuje rychlejší vývoj vnitřních smyček podobně jako vývoj na místním počítači.

Po spuštění služby přejděte k ní pomocí prohlížeče a s ním můžete pracovat.

Když je služba spuštěná, vraťte se na VS Code a aktualizujte řádek 13 v `server.js`. Například:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Uložte soubor a vraťte se k vaší službě v prohlížeči. V interakci se službou a Všimněte si, že se zobrazí aktualizovaná zpráva.

Při spuštění *nodemon*se proces uzlu automaticky restartuje hned po zjištění všech změn kódu. Tento proces automatického restartování je podobný možnosti úprav a restartování služby na místním počítači a nabízí tak prostředí pro vývoj vnitřních smyček.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations
