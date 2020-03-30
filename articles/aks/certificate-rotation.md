---
title: Otočení certifikátů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak otáčet certifikáty v clusteru Služby Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: f299b13baf5811b92bdc2e40b027868617d7574c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368515"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Otočení certifikátů ve službě Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) používá certifikáty pro ověřování s mnoha jeho součástmi. Pravidelně může být nutné tyto certifikáty z bezpečnostních důvodů nebo z důvodů zásad otáčet. Můžete mít například zásadu pro střídání všech certifikátů každých 90 dní.

Tento článek ukazuje, jak otočit certifikáty v clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.77 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certifikáty AKS, certifikační autority a účty služeb

Služba AKS generuje a používá následující certifikáty, certifikační autority a účty služeb:

* Server rozhraní API AKS vytvoří certifikační autoritu (CA) nazvanou Certifikační autorita clusteru.
* Server API má certifikační autoritu clusteru, která podepisuje certifikáty pro jednosměrnou komunikaci ze serveru API na kubelety.
* Každý kubelet také vytvoří žádost o podpis certifikátu (CSR), který je podepsán certifikační autoritou clusteru, pro komunikaci z kubelet na server API.
* Úložiště hodnot klíčů etcd má certifikát podepsaný certifikační autoritou clusteru pro komunikaci z etcd na server rozhraní API.
* Úložiště hodnot klíčů etcd vytvoří certifikační autoritu, která podepisuje certifikáty k ověřování a autorizaci replikace dat mezi replikami etcd v clusteru AKS.
* Agregátor rozhraní API používá certifikační autoritu clusteru k vydávání certifikátů pro komunikaci s jinými rozhraními API, jako je například Open Service Broker pro Azure. Agregátor rozhraní API může mít také vlastní certifikační autoritu pro vydávání těchto certifikátů, ale v současné době používá certifikační autoritu clusteru.
* Každý uzel používá token účtu služby (SA), který je podepsán certifikační autoritou clusteru.
* Klient `kubectl` má certifikát pro komunikaci s clusterem AKS.

> [!NOTE]
> Clustery AKS vytvořené před březnem 2019 mají certifikáty, jejichž platnost vyprší po dvou letech. Jakýkoli cluster vytvořený po březnu 2019 nebo jakýkoli cluster, ve kterých jsou otočeny certifikáty, mají certifikáty certifikační autority clusteru, jejichž platnost vyprší po 30 letech. Platnost všech ostatních certifikátů vyprší po dvou letech. Chcete-li ověřit, kdy `kubectl get nodes` byl vytvořen cluster, použijte k zobrazení *stáří* fondů uzlů.
> 
> Kromě toho můžete zkontrolovat datum vypršení platnosti certifikátu clusteru. Například následující příkaz zobrazí podrobnosti o certifikátu pro cluster *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Otočení certifikátů clusteru

> [!WARNING]
> Otáčení certifikátů pomocí `az aks rotate-certs` může způsobit až 30 minut prostojů pro cluster AKS.

Pomocí [az aks získat přihlašovací údaje][az-aks-get-credentials] pro přihlášení do clusteru AKS. Tento příkaz také stáhne `kubectl` a nakonfiguruje klientský certifikát v místním počítači.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Slouží `az aks rotate-certs` k otočení všech certifikátů, certifikačních autorit a sa ve vašem clusteru.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Dokončení může trvat až `az aks rotate-certs` 30 minut. Pokud se příkaz před `az aks show` dokončením nezdaří, použijte k ověření stavu clusteru *Otáčení certifikátu*. Pokud je cluster ve stavu selhání, znovu spusťte `az aks rotate-certs` otočení certifikátů znovu.

Spuštěním příkazu `kubectl` ověřte, zda staré certifikáty již nejsou platné. Vzhledem k tomu, že `kubectl`jste neaktualizovali certifikáty používané , zobrazí se chyba.  Například:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aktualizujte certifikát `kubectl` používaný `az aks get-credentials`spuštěním programu .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ověřte, zda byly certifikáty aktualizovány spuštěním příkazu, `kubectl` který bude nyní úspěšný. Například:

```console
kubectl get no
```

> [!NOTE]
> Pokud máte nějaké služby, které běží nad AKS, jako je [azure dev spaces][dev-spaces], budete muset [aktualizovat certifikáty související s těmito službami][dev-spaces-rotate] také.

## <a name="next-steps"></a>Další kroky

V tomto článku se ukazuje, jak automaticky otáčet certifikáty clusteru, certifikační autority a sa. Další informace o doporučených postupech zabezpečení AKS najdete [v doporučených postupech pro][aks-best-practices-security-upgrades] zabezpečení AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
