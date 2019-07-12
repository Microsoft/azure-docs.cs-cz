---
title: Vývoj s využitím Node.js v Kubernetes pomocí Azure Dev mezery
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Rychlý vývoj Kubernetes s kontejnery, mikroslužby a Node.js v Azure
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 3da6c015d46d2c83dd74c625e1e8eeaee81da2ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707125"
---
# <a name="quickstart-develop-with-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Rychlý start: Vývoj s využitím Node.js v Kubernetes pomocí Azure Dev mezery

V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Iterativní vývoj kódu v kontejnerech pomocí nástroje Visual Studio Code.
- Ladění kódu v prostoru dev z Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalovat Visual Studio Code](https://code.visualstudio.com/download).
- [Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) rozšíření pro Visual Studio Code nainstalované.
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru Azure Kubernetes Service

Je potřeba vytvořit v clusteru AKS [podporované oblasti][supported-regions]. Následujících příkazů vytvořte skupinu prostředků s názvem *MyResourceGroup* a cluster AKS volá *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolit Azure Dev mezery ve vašem clusteru AKS

Použití `use-dev-spaces` příkazu povolit prostory vývoj ve vašem clusteru AKS a postupujte podle zobrazených výzev. Následující příkaz povolí vývoj mezery na *MyAKS* cluster v *MyResourceGroup* seskupovat a vytvoří *výchozí* dev místa.

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

## <a name="get-sample-application-code"></a>Získat kód ukázkové aplikace

V tomto článku budete používat [Azure Dev prostory ukázkovou aplikaci](https://github.com/Azure/dev-spaces) demonstruje použití Azure Dev mezery.

Klonování aplikace z Githubu.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Příprava ukázkové aplikace ve Visual Studio Code

Otevřít Visual Studio Code, klikněte na tlačítko *souboru* pak *otevřít...* , přejděte *dev prostorů/ukázky/nodejs/získávání spustit/webfrontend* adresář a klikněte na tlačítko *otevřít*.

Teď máte *webfrontend* projekt otevřít ve Visual Studio Code. Ke spuštění aplikace v prostoru dev, Generovat graf prostředky Dockeru a Helm, pomocí rozšíření Azure Dev mezery v Pallette příkazu.

Ve Visual Studio Code otevřete paletu příkazů, klikněte na tlačítko *zobrazení* pak *paletu příkazů*. Začněte psát `Azure Dev Spaces` a klikněte na `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Příprava Azure Dev prostory konfiguračních souborů](./media/common/command-palette.png)

Když Visual Studio Code také vyžaduje, je možné nakonfigurovat veřejný koncový bod, vyberte `Yes` umožňující veřejný koncový bod.

![Vyberte veřejný koncový bod](media/common/select-public-endpoint.png)

Tento příkaz připraví projektu pro spuštění v Azure Dev prostory generováním grafu soubor Dockerfile a Helm. Zároveň se vygeneruje *.vscode* adresáře s laděním konfigurace v kořenovém adresáři vašeho projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Sestavení a spuštění kódu z Visual Studio Code v Kubernetes

Klikněte na *ladění* na levé straně a klikněte na ikonu *spuštění serveru (AZDS)* v horní části.

![Spustit Server](media/get-started-node/debug-configuration-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev mezery. *Terminálu* v dolní části okna se zobrazí výstup sestavení a adresy URL pro vaši službu s Azure Dev prostory. *Ladění konzoly* zobrazuje výstup protokolu.

> [!Note]
> Pokud nevidíte žádné příkazy Azure Dev mezery v *paletu příkazů*, ujistěte se, že jste nainstalovali [rozšíření Visual Studio Code pro Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ověřte také, můžete otevřít *dev prostorů/ukázky/nodejs/získávání spustit/webfrontend* ve Visual Studio Code.

Zobrazí se tato služba spuštěna po otevření veřejnou adresu URL.

Klikněte na tlačítko *ladění* pak *Zastavit ladění* zastavit ladicí program.

## <a name="update-code"></a>Aktualizace kódu

Pokud chcete nasadit aktualizovanou verzi vaší služby, můžete aktualizovat všechny soubory ve vašem projektu a znovu spustit *spusťte Server*. Příklad:

1. Pokud vaše aplikace je stále spuštěna, klikněte na tlačítko *ladění* pak *Zastavit ladění* zastavte ji.
1. Aktualizace [řádek 13 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) na:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spusťte *spustit Server*.
1. Přejděte na spuštěnou službu a sledujte změny.
1. Klikněte na tlačítko *ladění* pak *Zastavit ladění* ukončíte aplikaci.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Nastavení a použití zarážky pro ladění

Spusťte službu pomocí *spuštění serveru (AZDS)* .

Přejděte zpět na *Explorer* zobrazení kliknutím *zobrazení* pak *Explorer*. Otevřít `server.js` a klikněte na tlačítko někde na řádku 13 umístěte kurzor existuje. Chcete-li nastavit zarážku přístupů *F9* nebo klikněte na tlačítko *ladění* pak *Přepnout zarážku*.

Otevřete svou službu v prohlížeči a Všimněte si, že se nezobrazí žádná zpráva. Vraťte se do Visual Studio Code a podívejte se, že se zvýrazní řádek 13. Nastavit zarážku bylo pozastaveno služby na řádku 13. A to obnovit ji, stiskněte *F5* nebo klikněte na tlačítko *ladění* pak *pokračovat*. Vraťte se do prohlížeče a Všimněte si, že se teď zobrazí zpráva.

Při spuštění služby v Kubernetes s připojen jiný ladicí program, máte plný přístup k ladění informace, jako je zásobník volání, místní proměnné a informace o výjimce.

Odeberte zarážku vložením kurzor na řádku 13 v `server.js` a stisknout *F9*.

Klikněte na tlačítko *ladění* pak *Zastavit ladění* zastavit ladicí program.

## <a name="update-code-from-visual-studio-code"></a>Aktualizace kódu ze sady Visual Studio Code

Změnit režim ladění tak *připojit k serveru (AZDS)* a spustit službu:

![](media/get-started-node/attach-nodejs.png)

Tento příkaz vytvoří a spustí vaši službu v Azure Dev mezery. Také začne [nodemon](https://nodemon.io) zpracovat v kontejneru a bude k obrazci VS Code ho vaši službu. *Nodemon* procesu umožňuje automatické restartování, pokud jsou provedeny změny zdrojového kódu, umožní vývoj rychleji vnitřní smyčky, podobně jako vývoj v místním počítači.

Po spuštění služby, přejděte do něj pomocí prohlížeče a pracovat s ním.

Zatímco je služba spuštěna, vraťte se do VS Code a aktualizaci řádku 13 v `server.js`. Příklad:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Uložte soubor a vraťte se do služby v prohlížeči. Interakce se službou a Všimněte si, že se zobrazí aktualizovaná zpráva.

Při spouštění *nodemon*, proces Node se automaticky restartuje, jakmile jsou zjištěny žádné změny kódu. Tento proces automatické restartování je obdobou úpravy a restartování služby na místním počítači, poskytuje prostředí vývoje vnitřní smyčky.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak prostory vývoj Azure vám pomůže vytvořit složitější aplikace napříč více kontejnery, a jak můžete zjednodušit spolupráce na vývoji práce s různými verzemi nebo větve kódu v různých oborech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations