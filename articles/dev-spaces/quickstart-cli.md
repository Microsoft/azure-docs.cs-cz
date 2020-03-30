---
title: Vývoj aplikace na Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Tento rychlý start ukazuje, jak používat Azure Dev Spaces a příkazový řádek k vývoji aplikace ve službě Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8ee5cba06d9a526640d9057ee88a681d46392f4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239698"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Úvodní příručka: Vývoj aplikace na Kubernetes – Azure Dev Spaces
V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Vyvíjejte a spouštějte kód v kontejnerech pomocí příkazového řádku.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je třeba vytvořit cluster AKS v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků nazvanou *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů. Níže uvedený příkaz povolí dev spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří *výchozí* dev prostor.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces, pokud ještě není nainstalován. V prostředí Azure Cloud Shell nelze nainstalovat příkaz příkazpříkaz ový příkaz Azure Dev Spaces.

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

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces](https://github.com/Azure/dev-spaces) k předvedení pomocí Azure Dev Spaces.

Klonujte aplikaci z GitHubu a přejděte do *adresáře dev-spaces/samples/nodejs/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava žádosti

Chcete-li spustit aplikaci v Azure Dev Spaces, potřebujete dockerfile a helm graf. Pro některé jazyky, jako je [například Java][java-quickstart], [.NET core][netcore-quickstart]a [Node.js][nodejs-quickstart], můžete nástroje klienta Azure Dev Spaces generovat všechny prostředky, které potřebujete. Pro mnoho dalších jazyků, jako je Například Go, PHP a Python, může nástroj klienta generovat graf Helm tak dlouho, dokud můžete poskytnout platný Dockerfile.

Vygenerujte prostředky grafu Dockeru a Helma pro `azds prep` spuštění aplikace v Kubernetes pomocí příkazu:

```cmd
azds prep --enable-ingress
```

Chcete-li `prep` správně generovat datové zdroje grafu Docker a Helm, je nutné spustit příkaz z *adresáře dev-spaces/samples/nodejs/getting-started/webfrontend.*

> [!TIP]
> Příkaz `prep` se pokusí vygenerovat [Dockerfile a Helm graf](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt. Azure Dev Spaces používá tyto soubory k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Vytvořte a spusťte kód `azds up` v AKS pomocí příkazu:

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

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která `azds up` je zobrazena ve výstupu z příkazu. V tomto příkladu je *http://webfrontend.1234567890abcdef1234.eus.azds.io/* veřejná adresa URL .

> [!NOTE]
> Při přechodu na službu při spuštění `azds up`jsou ve výstupu `azds up` příkazu zobrazeny také trasování požadavků HTTP. Tyto trasování vám může pomoci při řešení potíží a ladění služby. Tyto trasování můžete `--disable-http-traces` zakázat `azds up`při spuštění .

Pokud `azds up` příkaz zastavíte pomocí *kombinace kláves Ctrl+c*, bude služba nadále spuštěna v AKS a veřejná adresa URL zůstane dostupná.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat `azds up` libovolný soubor v projektu a znovu spustit příkaz. Například:

1. Pokud `azds up` je stále spuštěn, stiskněte *kombinaci kláves Ctrl+c*.
1. Aktualizovat [řádek 13 `server.js` na:](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spusťte `azds up` příkaz:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Přejděte na spuštěnou službu a sledujte změny.
1. Stisknutím *kláves Ctrl+c* příkaz zastavte. `azds up`

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service