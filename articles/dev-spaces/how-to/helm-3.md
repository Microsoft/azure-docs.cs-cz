---
title: Konfigurace clusteru Azure Dev Spaces pro použití Helm 3 (Preview)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Zjistěte, jak nakonfigurovat cluster prostředí pro vývoj a používání Helm 3.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202257"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Konfigurace clusteru Azure Dev Spaces pro použití Helm 3 (Preview)

Azure Dev Spaces používá standardně Helm 2 k instalaci služeb uživatele do vývojových prostorů v clusteru AKS. Můžete povolit, aby Azure Dev Spaces používat Helm 3 místo Helm 2 instalace uživatelských služeb ve vývojových prostorech. Bez ohledu na to, jakou verzi Helm Azure Dev Spaces používá k instalaci služeb uživatele, můžete i nadále používat klienta Helm 2 nebo 3 ke správě vlastních verzí ve stejném clusteru.

Pokud povolíte Helm 3, Azure Dev Spaces se při instalaci služeb uživatele ve vývojových prostorech liší v následujících ohledech:

* Do vašeho clusteru již není v oboru názvů *azds* nasazen.
* Helm ukládá informace o vydaných verzích do oboru názvů, kde je nainstalována služba místo oboru názvů *azds* .
* Helm 3 informace o vydání zůstanou v oboru názvů, kde se služba nainstaluje po odstranění kontroleru.
* Pomocí klienta Helm 3 můžete přímo komunikovat s libovolnou verzí spravovanou pomocí Azure Dev Spaces v clusteru.

V této příručce se dozvíte, jak povolit Helm 3 Azure Dev Spaces pro instalaci služeb uživatele ve vývojových prostorech.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Před zahájením

### <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli]

### <a name="register-the-helm3preview-preview-feature"></a>Registrace funkce Helm3Preview ve verzi Preview

Pokud chcete povolit, aby Azure Dev Spaces Helm 3 pro instalaci služeb uživatele ve vývojových prostorech, nejdřív u svého předplatného povolte příznak funkce *Helm3Preview* pomocí příkazu *AZ Feature Register* :

> [!WARNING]
> Toto prostředí verze Preview bude používat libovolný cluster AKS, který povolíte Azure Dev Spaces s příznakem funkce *Helm3Preview* . Pokud chcete dál povolit plně podporovaná Azure Dev Spaces v clusterech AKS, nepovolujte funkce Preview na produkčních předplatných. Pro testování funkcí ve verzi Preview použijte samostatný test nebo vývojové předplatné Azure.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Dokončení registrace trvá několik minut. Pomocí příkazu *AZ Feature show* ověřte stav registrace.

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Když je *stav* *zaregistrován*, obnovte registraci *Microsoft. DevSpaces* pomocí *AZ Provider Register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Omezení

Pokud je tato funkce ve verzi Preview, platí následující omezení:

* Tato funkce se nedá použít u clusterů AKS se stávajícími úlohami. Musíte vytvořit nový cluster AKS.

## <a name="create-your-cluster"></a>Vytvoření clusteru

Vytvořte nový cluster AKS v oblasti, která má tuto funkci ve verzi Preview. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a nový cluster AKS s názvem *MyAKS* v oblasti *střed USA – jih* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Povolit Azure Dev Spaces

Pomocí příkazu *použít-dev-Spaces* povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří výchozí místo pro vývoj.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Ověřte, že se na vývojových prostorech běží Helm 3.

Pomocí výpisu nasazení v oboru názvů *azds* ověřte, že není spuštěný.

```cmd
kubectl get deployment -n azds
```

Potvrzení *, že nástroj není* spuštěný v oboru názvů azds. Příklad:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md