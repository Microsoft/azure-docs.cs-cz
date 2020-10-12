---
title: Otočení certifikátů ve službě Azure Kubernetes (AKS)
description: Naučte se, jak tyto certifikáty otočit v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 90526b78e65c335f07a2a9d2d152b54b47233082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211027"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Otočení certifikátů ve službě Azure Kubernetes (AKS)

Služba Azure Kubernetes Service (AKS) používá certifikáty pro ověřování s mnoha jeho součástmi. V pravidelných intervalech může být nutné tyto certifikáty otočit z důvodů zabezpečení nebo zásad. Můžete mít například zásadu pro otočení všech certifikátů každých 90 dní.

V tomto článku se dozvíte, jak otočit certifikáty v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.77 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS certifikáty, certifikační autority a účty služeb

AKS generuje a používá následující certifikáty, certifikační autority a účty služeb:

* Server AKS API vytvoří certifikační autoritu (CA), která se nazývá certifikační autorita clusteru.
* Server API má Clusterovou certifikační autoritu, která podepisuje certifikáty pro jednosměrnou komunikaci ze serveru rozhraní API do kubelets.
* Každý kubelet také vytvoří žádost o podepsání certifikátu (CSR), která je podepsána certifikační autoritou clusteru, pro komunikaci z kubelet na server rozhraní API.
* Úložiště hodnot klíčů etcd má certifikát podepsaný certifikační autoritou clusteru za účelem komunikace z etcd k serveru rozhraní API.
* Úložiště hodnot klíčů etcd vytvoří certifikační autoritu, která podepisuje certifikáty k ověřování a autorizaci replikace dat mezi replikami etcd v clusteru AKS.
* Agregátor rozhraní API používá certifikační autoritu clusteru k vystavování certifikátů pro komunikaci s jinými rozhraními API. Agregátor rozhraní API může mít také vlastní certifikační autoritu pro vydávání certifikátů, ale aktuálně používá certifikační autoritu clusteru.
* Každý uzel používá token účtu služby (SA), který je podepsaný certifikační autoritou clusteru.
* `kubectl`Klient nástroje má certifikát pro komunikaci s clusterem AKS.

> [!NOTE]
> Clustery AKS vytvořené před vydáním března 2019 obsahují certifikáty, jejichž platnost vyprší po dvou letech. Každý cluster vytvořený po březnu 2019 nebo jakémkoli clusteru, který má jeho certifikáty otočený, má certifikáty certifikační autority clusteru, jejichž platnost vyprší po 30 letech. Platnost všech ostatních certifikátů vyprší po dvou letech. Pokud chcete ověřit, kdy byl cluster vytvořen, použijte `kubectl get nodes` k zobrazení *stáří* fondů uzlů.
> 
> Navíc můžete kontrolovat datum vypršení platnosti certifikátu vašeho clusteru. Například následující příkaz bash zobrazí podrobnosti o certifikátu pro cluster *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Otočení certifikátů clusteru

> [!WARNING]
> Otáčení certifikátů pomocí `az aks rotate-certs` může způsobit až 30 minut výpadků clusteru AKS.

Přihlaste se ke svému clusteru AKS pomocí [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz také stáhne a nakonfiguruje `kubectl` klientský certifikát na místním počítači.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Použijte `az aks rotate-certs` k otočení všech certifikátů, certifikačních autorit a SAS v clusteru.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Dokončení může trvat až 30 minut `az aks rotate-certs` . Pokud příkaz před dokončením skončí, použijte `az aks show` k ověření stavu clusteru *otočení certifikátu*. Pokud je cluster ve stavu selhání, znovu ho znovu `az aks rotate-certs` natočit.

Spuštěním příkazu ověřte, že staré certifikáty již nejsou platné `kubectl` . Vzhledem k tomu, že jste neaktualizovali certifikáty používané v `kubectl` , zobrazí se chyba.  Například:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aktualizujte certifikát používaný spuštěním `kubectl` `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ověřte, že se certifikáty aktualizovaly spuštěním `kubectl` příkazu, který teď bude úspěšný. Například:

```console
kubectl get no
```

> [!NOTE]
> Pokud máte nějaké služby, které běží na AKS, například [Azure dev Spaces][dev-spaces], možná budete muset [aktualizovat i certifikáty související s těmito službami][dev-spaces-rotate] .

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak automaticky střídat certifikáty, certifikační autority a SAs v clusteru. [Osvědčené postupy pro zabezpečení a upgrady clusterů najdete v tématu Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] , kde najdete další informace o osvědčených postupech zabezpečení AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
