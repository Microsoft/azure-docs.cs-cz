---
title: Vývoj aplikace s existujícím Helm grafem v Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: V tomto rychlém startu se dozvíte, jak používat Azure Dev Spaces a příkazový řádek pro vývoj aplikace s existujícím grafem Helm ve službě Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7d1d782203401607d3909d79d1f0f3a499712c32
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414305"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Rychlý Start: vývoj aplikace pomocí existujícího Helm grafu na Kubernetes-Azure Dev Spaces
V tomto průvodci se naučíte:

- Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
- Spusťte aplikaci s existujícím Helm grafem v AKS pomocí Azure Dev Spaces v příkazovém řádku.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes Service

Cluster AKS je potřeba vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí `use-dev-spaces` příkazu povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří vývojové místo s názvem *dev*.

> [!NOTE]
> `use-dev-spaces`Příkaz nainstaluje taky Azure dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku pomocí [ukázkové aplikace Azure dev Spaces](https://github.com/Azure/dev-spaces) předvádíte použití Azure dev Spaces.

Naklonujte aplikaci z GitHubu a přejděte do adresáře *dev-Spaces/Samples/Python/Začínáme/webfront-Endu* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Příprava aplikace

Aby bylo možné spustit aplikaci na Azure Dev Spaces, potřebujete graf souboru Dockerfile a Helm. V některých jazycích, jako jsou [Java][java-quickstart], [.net Core][netcore-quickstart]a [Node.js][nodejs-quickstart], mohou nástroje pro Azure dev Spaces klienta generovat všechny potřebné prostředky. Pro mnoho dalších jazyků, jako je například jít, PHP nebo Python, mohou nástroje klienta generovat graf Helm, pokud můžete zadat platné souboru Dockerfile. V takovém případě má ukázková aplikace existující graf souboru Dockerfile a Helm.

Vygenerujte konfiguraci pro spuštění aplikace pomocí Azure Dev Spaces s existujícím grafem Helm a souboru Dockerfile pomocí `azds prep` příkazu:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Musíte spustit `prep` příkaz z adresáře *dev-Spaces/Samples/Python/Začínáme/webfront-Endu* a zadat umístění grafu Helm pomocí `--chart` .

> [!NOTE]
> Může se zobrazit upozornění: *Upozornění: souboru Dockerfile nebylo možné vygenerovat z důvodu nepodporovaného jazyka.* Při spuštění `azds prep` . `azds prep`Příkaz se pokusí vygenerovat [graf souboru Dockerfile a Helm](how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt, ale nepřepíše žádné existující grafy fázemi nebo Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes

Sestavte a spusťte kód v AKS pomocí `azds up` příkazu:

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

Službu spuštěnou můžete zobrazit otevřením veřejné adresy URL, která se zobrazí ve výstupu `azds up` příkazu. V tomto příkladu je veřejná adresa URL `http://dev.service.1234567890abcdef1234.eus.azds.io/` .

> [!NOTE]
> Když při spuštění přejdete ke službě `azds up` , ve výstupu příkazu se zobrazí také trasování požadavků HTTP `azds up` . Tyto trasování vám můžou pomoct při řešení potíží a ladění vaší služby. Tato trasování můžete zakázat pomocí aplikace `--disable-http-traces` při spuštění `azds up` .

Pokud příkaz zastavíte stisknutím `azds up` *kombinace kláves Ctrl + c*, služba bude nadále běžet v AKS a veřejná adresa URL zůstane dostupná.

## <a name="update-code"></a>Aktualizace kódu

Chcete-li nasadit aktualizovanou verzi služby, můžete aktualizovat libovolný soubor v projektu a znovu spustit `azds up` příkaz. Příklad:

1. Pokud `azds up` je pořád spuštěný, stiskněte klávesy *Ctrl + c*.
1. Aktualizujte [řádek 13 `webfrontend.py` v](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) :
    
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

1. Přejděte do spuštěné služby a sledujte své změny.
1. Stisknutím *kombinace kláves Ctrl + c* zastavte `azds up` příkaz.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
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