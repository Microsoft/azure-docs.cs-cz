---
title: 'Ladění a iterace na Kubernetes: Visual Studio Code & Node. js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak používat Azure Dev Spaces a Visual Studio Code k ladění a rychlému iteraci aplikace Node. js ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 74063e03e8298e388efd6888fc05bcbbc9aefa4b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245070"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Rychlý Start: ladění a iterace v Kubernetes pomocí Visual Studio Code a Node. js – Azure Dev Spaces

V tomto rychlém startu jste nastavili Azure Dev Spaces pomocí spravovaného clusteru Kubernetes a pomocí aplikace Node. js v Visual Studio Code mohli iterativní vývoj a ladění kódu v kontejnerech. Azure Dev Spaces umožňuje ladit a testovat všechny komponenty vaší aplikace ve službě Azure Kubernetes Service (AKS) s minimálním nastavením vývojového počítače. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Nejnovější verzi Node. js](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) rozšíření pro Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Následující příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí příkazu `use-dev-spaces` povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí vývojové prostory v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří *výchozí* prostor pro vývoj.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje Azure Dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nemůžete nainstalovat do Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
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

Otevřete Visual Studio Code, vyberte **soubor** , pak **otevřít**, přejděte do adresáře *dev-Spaces/Samples/NodeJS/Začínáme/webendu* a vyberte **otevřít**.

Nyní máte projekt *webendu* otevřený v Visual Studio Code. Chcete-li aplikaci spustit ve vývojovém prostoru, vygenerujte pomocí rozšíření Azure Dev Spaces v paletě příkazů prostředky Docker a Helm Chart.

Chcete-li otevřít paletu příkazů v Visual Studio Code, vyberte **Zobrazit** **paleta příkazů**. Začněte psát `Azure Dev Spaces` a vyberte **Azure dev Spaces: Příprava konfiguračních souborů pro Azure dev Spaces**.

![Příprava konfiguračních souborů pro Azure Dev Spaces](./media/common/command-palette.png)

Když Visual Studio Code taky vyzve ke konfiguraci veřejného koncového bodu, vyberte `Yes` a povolte veřejný koncový bod.

![Vybrat veřejný koncový bod](media/common/select-public-endpoint.png)

Tento příkaz připraví projekt tak, aby běžel v Azure Dev Spaces generováním grafu typu souboru Dockerfile a Helm. Také generuje adresář *. VSCode* s konfigurací ladění v kořenovém adresáři projektu.

> [!TIP]
> [Graf souboru Dockerfile a Helm](how-dev-spaces-works.md#prepare-your-code) pro váš projekt je používán Azure dev Spaces k sestavení a spuštění kódu, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavování a spouštění kódu v Kubernetes z Visual Studio Code

Na levé straně vyberte ikonu **ladění** a v horní části vyberte **spustit server (AZDS)** .

![Spustit server](media/get-started-node/debug-configuration-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. V dolní části okna **terminálu** se zobrazuje výstup sestavení a adresy URL pro vaši službu běžící Azure dev Spaces. **Konzola ladění** zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev Spaces v **paletě příkazů**, ujistěte se, že máte nainstalovanou [příponu Visual Studio Code pro Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, že jste v Visual Studio Code otevřeli adresář *dev-Spaces/Samples/NodeJS/Začínáme/webendu* .

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL.

Vyberte **ladit** a pak **Zastavit ladění** , aby se ladicí program zastavil.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit **spouštěcí Server**. Příklad:

1. Pokud je vaše aplikace pořád spuštěná, vyberte ladění **a zastavte** ji.
1. Aktualizujte [řádek 13 v `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) na:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spusťte **spouštěcí Server**.
1. Přejděte do spuštěné služby a sledujte své změny.
1. Vyberte **ladit** a pak **Zastavit ladění** a zastavte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážek pro ladění

Spusťte službu pomocí **spouštěcího serveru (AZDS)** .

Přejděte zpět do zobrazení Průzkumníka výběrem možnosti **Zobrazit** a **Průzkumník**. Otevřete *Server. js* a vložte kurzor do umístění 13 Kliknutím kamkoli na řádku 13. Pokud chcete nastavit zarážku, stiskněte **F9** nebo vyberte **ladit** a pak **přepínací zarážku**.

V prohlížeči otevřete službu a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se na Visual Studio Code a sledujte, že je zvýrazněna řádka 13. Zarážka, kterou jste nastavili, službu pozastavila na řádku 13. Pokud chcete službu obnovit, stiskněte klávesu **F5** nebo vyberte **ladit** a pak **pokračovat**. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spouštění služby v Kubernetes s připojeným ladicím programem máte úplný přístup k ladicím informacím, jako je zásobník volání, místní proměnné a informace o výjimkách.

Odstraňte zarážku tak, že umístíte kurzor na řádek 13 v *serveru. js* a stisknete klávesu **F9**.

Vyberte **ladit** a pak **Zastavit ladění** , aby se ladicí program zastavil.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu z Visual Studio Code

Změňte režim ladění tak, aby se **připojil k serveru (AZDS)** , a spusťte službu:

![](media/get-started-node/attach-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev Spaces. Také spustí proces [nodemon](https://nodemon.io) v kontejneru služby a připojí k němu vs Code. Proces *nodemon* umožňuje automatické restartování při změně zdrojového kódu a umožňuje rychlejší vývoj vnitřních smyček podobně jako vývoj na místním počítači.

Po spuštění služby přejděte k ní pomocí prohlížeče a s ním můžete pracovat.

Když je služba spuštěná, vraťte se do VS Code a aktualizujte řádek 13 v *serveru. js*. Příklad:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Uložte soubor a vraťte se k vaší službě v prohlížeči. V interakci se službou a Všimněte si, že se zobrazí aktualizovaná zpráva.

Při spuštění *nodemon*se proces uzlu automaticky restartuje hned po zjištění všech změn kódu. Tento proces automatického restartování je podobný možnosti úprav a restartování služby na místním počítači a nabízí tak prostředí pro vývoj vnitřních smyček.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-nodejs.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
