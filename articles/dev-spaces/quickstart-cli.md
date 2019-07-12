---
title: Nasazení aplikace v Kubernetes pomocí Azure Dev mezery
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Nasazení mikroslužeb v Azure Dev prostory AKS
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706271"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Rychlý start: Vývoj aplikace v Kubernetes pomocí Azure Dev mezery
V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Vývoj a spouštění kódu v kontejnerech pomocí příkazového řádku.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
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

Klonování aplikace z Githubu a přejděte do *dev prostorů/ukázky/nodejs/získávání spustit/webfrontend* adresáře:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava aplikace

Chcete-li spustit aplikaci na Azure Dev mezery, je nutné soubor Dockerfile a Helm grafu. Pro některé jazyky jako například [Java][java-quickstart], [.NET core][netcore-quickstart], a [Node.js][nodejs-quickstart], klienta Azure Dev prostory nástrojů můžete vygenerovat všechny prostředky, které potřebujete. Pro mnoho dalších jazyků, jako jsou Go, PHP a Python můžete klienta nástroje generování grafu helmu tak dlouho, dokud je zadat platný soubor Docker.

Generovat graf prostředků Dockeru a Helm pro spuštění aplikace v Kubernetes pomocí `azds prep` příkaz:

```cmd
azds prep --public
```

Je třeba spustit `prep` příkaz *dev prostorů/ukázky/nodejs/získávání spustit/webfrontend* adresáře se správně Generovat graf prostředků Dockeru a Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Sestavte a spusťte váš kód v AKS pomocí `azds up` příkaz:

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

Zobrazí se tato služba spuštěna po otevření veřejnou adresu URL, které se zobrazí ve výstupu `azds up` příkazu. V tomto příkladu je veřejnou adresu URL *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Chcete-li zrušit `azds up` příkazu *Ctrl + c*, služba bude pokračovat ve službě AKS a veřejná adresa URL bude stále dostupný.

## <a name="update-code"></a>Aktualizace kódu

Pokud chcete nasadit aktualizovanou verzi vaší služby, můžete aktualizovat všechny soubory ve vašem projektu a znovu spustit `azds up` příkazu. Příklad:

1. Pokud `azds up` je pořád spuštěný, stiskněte klávesu *Ctrl + c*.
1. Aktualizace [řádek 13 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) na:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spustit `azds up` příkaz:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Přejděte na spuštěnou službu a sledujte změny.
1. Stisknutím klávesy *Ctrl + c* Zastavit `azds up` příkazu.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak prostory vývoj Azure vám pomůže vytvořit složitější aplikace napříč více kontejnery, a jak můžete zjednodušit spolupráce na vývoji práce s různými verzemi nebo větve kódu v různých oborech.

> [!div class="nextstepaction"]
> [Vývoj v týmu v Azure Dev mezery][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations