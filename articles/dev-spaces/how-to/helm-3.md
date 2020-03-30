---
title: Konfigurace clusteru Azure Dev Spaces tak, aby používal Helm 3 (preview)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Přečtěte si, jak nakonfigurovat cluster Dev Spaces tak, aby používal Helm 3
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454291"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurace clusteru Azure Dev Spaces tak, aby používal Helm 3 (preview)

Azure Dev Spaces používá Helm 2 ve výchozím nastavení k instalaci uživatelských služeb v dev prostorech v clusteru AKS. Azure Dev Spaces můžete povolit použití Helm 3 namísto Helm 2 instalace uživatelských služeb v dev prostorech. Bez ohledu na verzi Helm Azure Dev Spaces používá k instalaci uživatelských služeb, můžete pokračovat v použití Helm 2 nebo 3 klienta ke správě vlastní verze ve stejném clusteru.

Když povolíte Helm 3, Azure Dev Spaces se při instalaci uživatelských služeb v prostorách pro aplikace Dev chová jinak následujícími způsoby:

* Tiller se již nenasazuje do clusteru v oboru názvů *AZDS.*
* Helm ukládá informace o uvolnění v oboru názvů, kde je služba nainstalována namísto oboru názvů *AZDS.*
* Informace o verzi helmu 3 zůstávají v oboru názvů, kde je služba nainstalována po odstranění řadiče.
* Můžete přímo pracovat s jakoukoli verzí spravovanou Azure Dev Spaces ve vašem clusteru pomocí helm3 klienta.

V této příručce se dozvíte, jak povolit Helm 3 pro Azure Dev Spaces k instalaci uživatelských služeb v dev prostorech.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli]

### <a name="register-the-helm3preview-preview-feature"></a>Registrace funkce náhledu Helm3Preview

Chcete-li povolit Azure Dev Spaces používat Helm 3 pro instalaci uživatelských služeb v prostorech pro zpracování, nejprve povolte příznak funkce *Helm3Preview* ve vašem předplatném pomocí příkazu *az feature register:*

> [!WARNING]
> Všechny clustery AKS, které povolíte azure dev spaces pomocí příznaku funkce *Helm3Preview,* budou používat toto prostředí preview. Chcete-li i nadále povolit plně podporované Azure Dev Spaces na clusterech AKS, nepovolujte funkce náhledu v produkčních předplatných. Pro testování funkcí preview použijte samostatné testovací nebo vývojové předplatné Azure.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Trvá několik minut, než se registrace dokončí. Zkontrolujte stav registrace pomocí příkazu *az feature show:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Když je *stav* *Registrováno*, aktualizujte registraci *Microsoft.DevSpaces* pomocí *registru zprostředkovatele az*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Omezení

Následující omezení platí, když je tato funkce ve verzi Preview:

* Tuto funkci nelze použít v clusterech AKS s existujícími úlohami. Je nutné vytvořit nový cluster AKS.

## <a name="create-your-cluster"></a>Vytvoření clusteru

Vytvořte nový cluster AKS v oblasti, která má tuto funkci náhledu. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a nový cluster AKS s názvem *MyAKS*:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Povolení azure dev spaces

Pomocí příkazu *use-dev-spaces* povolte v clusteru AKS funkce Dev Spaces a postupujte podle pokynů. Níže uvedený příkaz povolí dev spaces v clusteru *MyAKS* ve skupině *MyResourceGroup* a vytvoří výchozí dev prostor.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Ověření, že dev Spaces běží helm3

Ověřte, zda kultivátor není spuštěn, a to tak, že vyhotovujete nasazení v oboru názvů *AZDS:*

```cmd
kubectl get deployment -n azds
```

Potvrďte, *že* v oboru názvů AZDS není spuštěna. Například:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md