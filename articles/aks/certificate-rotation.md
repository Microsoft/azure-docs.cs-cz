---
title: Otočení certifikátů ve službě Azure Kubernetes (AKS)
description: Naučte se, jak tyto certifikáty otočit v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 02bfdbc840065558003b249e1e3ea52f46ec64d6
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596263"
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
* Agregátor rozhraní API používá certifikační autoritu clusteru k vydávání certifikátů pro komunikaci s jinými rozhraními API, jako je například Open Service Broker pro Azure. Agregátor rozhraní API může mít také vlastní certifikační autoritu pro vydávání certifikátů, ale aktuálně používá certifikační autoritu clusteru.
* Každý uzel používá token účtu služby (SA), který je podepsaný certifikační autoritou clusteru.
* Klient `kubectl` má certifikát pro komunikaci s clusterem AKS.

> [!NOTE]
> Clustery AKS vytvořené před vydáním března 2019 obsahují certifikáty, jejichž platnost vyprší po dvou letech. Každý cluster vytvořený po uplynutí března 2019 nebo jakýkoli cluster s otočenými certifikáty má certifikáty, jejichž platnost vyprší po 30 letech. Pokud chcete ověřit, kdy byl cluster vytvořen, použijte `kubectl get nodes` k zobrazení *stáří* fondů uzlů.
> 
> Navíc můžete kontrolovat datum vypršení platnosti certifikátu vašeho clusteru. Například následující příkaz zobrazí podrobnosti o certifikátu pro cluster *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Otočení certifikátů clusteru

> [!WARNING]
> Střídání certifikátů pomocí `az aks rotate-certs` může pro cluster AKS způsobit až 30 minut výpadků.

Přihlaste se ke svému clusteru AKS pomocí [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz také stáhne a nakonfiguruje `kubectl` klientský certifikát na místním počítači.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

K otočení všech certifikátů, certifikačních autorit a SAs v clusteru použijte `az aks rotate-certs`.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Dokončení `az aks rotate-certs` může trvat až 30 minut. Pokud se příkaz před dokončením nepovede, ověřte pomocí `az aks show`, jestli je stav clusteru *rotující*. Pokud je cluster ve stavu selhání, znovu znovu spusťte `az aks rotate-certs` pro otočení certifikátů.

Spuštěním `kubectl` příkazu ověřte, že staré certifikáty již nejsou platné. Vzhledem k tomu, že jste neaktualizovali certifikáty používané v `kubectl`, zobrazí se chyba.  Příklad:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aktualizujte certifikát, který používá `kubectl` spuštěním `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ověřte, že se certifikáty aktualizovaly spuštěním příkazu `kubectl`, který teď bude úspěšný. Příklad:

```console
kubectl get no
```

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak automaticky střídat certifikáty, certifikační autority a SAs v clusteru. [Osvědčené postupy pro zabezpečení a upgrady clusterů najdete v tématu Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] , kde najdete další informace o osvědčených postupech zabezpečení AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
