---
title: Vývoj aplikace v Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak používat Azure Dev Spaces a příkazový řádek pro vývoj aplikace ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2d3a498d72264d3084e45202b7daa99806d45ce3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602608"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Rychlý Start: vývoj aplikace na Kubernetes-Azure Dev Spaces
V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Vývoj a spouštění kódu v kontejnerech pomocí příkazového řádku.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí příkazu `use-dev-spaces` povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří *výchozí* místo pro vývoj.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje Azure Dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

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

Naklonujte aplikaci z GitHubu a přejděte do adresáře *dev-Spaces/Samples/NodeJS/Začínáme/webfront-Endu* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava aplikace

Aby bylo možné spustit aplikaci na Azure Dev Spaces, potřebujete graf souboru Dockerfile a Helm. Pro některé jazyky, jako jsou [Java][java-quickstart], [.NET Core][netcore-quickstart]a [Node. js][nodejs-quickstart], můžou Azure dev Spaces nástroje klienta vygenerovat všechny potřebné prostředky. Pro mnoho dalších jazyků, jako je například jít, PHP nebo Python, mohou nástroje klienta generovat graf Helm, pokud můžete zadat platné souboru Dockerfile.

Vygenerujte prostředky Docker a Helm Chart pro spuštění aplikace v Kubernetes pomocí příkazu `azds prep`:

```cmd
azds prep --enable-ingress
```

Chcete-li správně vygenerovat prostředky Docker a Helm Chart, je nutné spustit příkaz `prep` z adresáře *dev-Spaces/Samples/NodeJS/Začínáme/webfront-Endu* .

> [!TIP]
> Příkaz `prep` se pokusí vygenerovat [graf souboru Dockerfile a Helm](how-dev-spaces-works.md#prepare-your-code) pro váš projekt. Azure Dev Spaces používá tyto soubory k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Sestavte a spusťte kód v AKS pomocí příkazu `azds up`:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která se zobrazí ve výstupu příkazu `azds up`. V tomto příkladu je veřejná adresa URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

> [!NOTE]
> Když při spuštění `azds up`přejdete ke službě, ve výstupu příkazu `azds up` se zobrazí také trasování požadavků HTTP. Tyto trasování vám můžou pomoct při řešení potíží a ladění vaší služby. Tato trasování můžete zakázat pomocí `--disable-http-traces` při spuštění `azds up`.

Pokud příkaz `azds up` zastavíte pomocí *kombinace kláves Ctrl + c*, služba bude nadále běžet v AKS a veřejná adresa URL zůstane dostupná.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit příkaz `azds up`. Příklad:

1. Pokud je stále spuštěna `azds up`, stiskněte klávesy *Ctrl + c*.
1. Aktualizujte [řádek 13 v `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) na:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Spusťte příkaz `azds up` znovu:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Přejděte do spuštěné služby a sledujte své změny.
1. Stisknutím *kombinace kláves Ctrl + c* zastavte příkaz `azds up`.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service