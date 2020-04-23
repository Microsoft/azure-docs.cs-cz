---
title: Vývoj aplikace s existujícím Graf helmu na Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Tento rychlý start ukazuje, jak pomocí Azure Dev Spaces a příkazového řádku vyvíjet aplikaci s existujícím grafem Helm ve službě Azure Kubernetes Service.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033561"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Úvodní příručka: Vývoj aplikace s existujícím grafem Helm na Kubernetes – Azure Dev Spaces
V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Spusťte aplikaci s existujícím grafem Helm v AKS pomocí Azure Dev Spaces na příkazovém řádku.

## <a name="prerequisites"></a>Požadované součásti

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Je třeba vytvořit cluster AKS v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků nazvanou *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte funkci Dev Spaces v clusteru AKS a postupujte podle pokynů. Příkaz níže povolí funkce Dev Spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří dev prostor nazvaný *dev*.

> [!NOTE]
> Příkaz `use-dev-spaces` také nainstaluje příkaz CLI Azure Dev Spaces, pokud ještě není nainstalován. V prostředí Azure Cloud Shell nelze nainstalovat příkaz příkazpříkaz ový příkaz Azure Dev Spaces.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku použijete [ukázkovou aplikaci Azure Dev Spaces](https://github.com/Azure/dev-spaces) k předvedení pomocí Azure Dev Spaces.

Klonujte aplikaci z GitHubu a přejděte do *adresáře dev-spaces/samples/python/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava žádosti

Chcete-li spustit aplikaci v Azure Dev Spaces, potřebujete dockerfile a helm graf. Pro některé jazyky, jako je [například Java][java-quickstart], [.NET core][netcore-quickstart]a [Node.js][nodejs-quickstart], můžete nástroje klienta Azure Dev Spaces generovat všechny prostředky, které potřebujete. Pro mnoho dalších jazyků, jako je Například Go, PHP a Python, může nástroj klienta generovat graf Helm tak dlouho, dokud můžete poskytnout platný Dockerfile. V tomto případě ukázková aplikace má existující Dockerfile a Helm graf

Vygenerujte konfiguraci pro spuštění aplikace pomocí Azure Dev `azds prep` Spaces s existujícím grafem Helm a dockerfile pomocí příkazu:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Příkaz je `prep` nutné spustit z *adresáře dev-spaces/samples/python/getting-started/webfrontend* a zadat `--chart`umístění grafu Helm pomocí .

> [!NOTE]
> Upozornění: *UPOZORNĚNÍ: Dockerfile nelze vygenerovat z důvodu nepodporovaného jazyka.* při `azds prep`spuštění . Příkaz `azds prep` se pokusí generovat [Dockerfile a Helm graf](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt, ale nebude přepsat žádné existující Dockerfiles nebo Helm grafy.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Vytvořte a spusťte kód `azds up` v AKS pomocí příkazu:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která `azds up` je zobrazena ve výstupu z příkazu. V tomto příkladu je *http://dev.service.1234567890abcdef1234.eus.azds.io/* veřejná adresa URL .

> [!NOTE]
> Při přechodu na službu při spuštění `azds up`jsou ve výstupu `azds up` příkazu zobrazeny také trasování požadavků HTTP. Tyto trasování vám může pomoci při řešení potíží a ladění služby. Tyto trasování můžete `--disable-http-traces` zakázat `azds up`při spuštění .

Pokud `azds up` příkaz zastavíte pomocí *kombinace kláves Ctrl+c*, bude služba nadále spuštěna v AKS a veřejná adresa URL zůstane dostupná.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat `azds up` libovolný soubor v projektu a znovu spustit příkaz. Příklad:

1. Pokud `azds up` je stále spuštěn, stiskněte *kombinaci kláves Ctrl+c*.
1. Aktualizovat [řádek 13 `webfrontend.py` na:](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Uložte provedené změny.
1. Znovu spusťte `azds up` příkaz:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
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