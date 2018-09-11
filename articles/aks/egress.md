---
title: Seznam povolených výchozí přenos z clusteru Azure Kubernetes Service (AKS)
description: Seznam povolených výchozí přenos z clusteru služby Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347796"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Výchozí přenos dat Azure Kubernetes Service (AKS)

Ve výchozím nastavení se náhodně přiřadí adresu výchozí přenos dat z clusteru služby Azure Kubernetes Service (AKS). Tato konfigurace není ideální, pokud by bylo nutné určit IP adresu pro přístup k externím službám. Tento dokument podrobně popisuje, jak vytvořit a udržovat IP adresu staticky přiřazované odchozího přenosu dat v clusteru AKS.

## <a name="egress-overview"></a>Přehled odchozího přenosu dat

Odchozí provoz z clusteru AKS dodržovat konvence nástroje pro vyrovnávání zatížení Azure, které jsou popsány [tady][outbound-connections]. Před první služby Kubernetes typu `LoadBalancer` vytvoření agenta uzly, které nejsou součástí žádné fondu nástroje pro vyrovnávání zatížení Azure. V této konfiguraci jsou uzly bez úrovni instance veřejnou IP adresu. Azure se přeloží odchozí tok veřejné Zdrojová IP adresa, která není konfigurovatelné nebo deterministický.

Jakmile služba Kubernetes typu `LoadBalancer` se vytvoří, uzly jsou přidány do fondu služby Azure Load Balancer agenta. Pro odchozí tok Azure převede do první veřejnou IP adresou nakonfigurovanou na nástroj pro vyrovnávání zatížení.

## <a name="create-a-static-public-ip"></a>Vytvoření statické veřejné IP adresy

Aby se zabránilo náhodné IP adresy z používán, vytvořte statickou IP adresu a ujistěte se, že nástroj pro vyrovnávání zatížení používá tuto adresu. IP adresa je potřeba vytvořit ve službě AKS **uzel** skupinu prostředků.

Získání názvu skupiny prostředků s [az resource show] [ az-resource-show] příkazu. Aktualizujte název skupiny prostředků a název clusteru, aby odpovídaly vašemu prostředí.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Pak pomocí [az network public-ip vytvořit] [ public-ip-create] příkaz pro vytvoření statické veřejné IP adresy. Aktualizujte název skupiny prostředků tak, aby odpovídaly názvu gatherred v posledním kroku.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Vytvoření služby pomocí statické IP adresy

Teď, když máte IP adresu, vytvořte Kubernetes služby s typem `LoadBalancer` a IP adresu přiřadit ke službě.

Vytvořte soubor s názvem `egress-service.yaml` a zkopírujte do následující kód YAML. Aktualizujte IP adresu, aby odpovídaly vašemu prostředí.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Vytvoření služby a nasazení se `kubectl apply` příkazu.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Vytváří se tato služba nakonfiguruje novou IP adresu front-endu na nástroj pro vyrovnávání zatížení Azure. Pokud nemáte jiné IP adresy nakonfigurován, pak **všechny** výchozí přenos byste teď měli použít tuto adresu. Při více adres jsou nakonfigurované na Azure Load Balancer, výchozí přenos dat používá první IP adresa na tento nástroj pro vyrovnávání zatížení.

## <a name="verify-egress-address"></a>Zkontrolujte výstupní adresou

Pokud chcete ověřit, že používá veřejnou IP adresu, použijte službu `checkip.dyndns.org`.

Spuštění a připojení k podu:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

V případě potřeby nainstalujte curl v kontejneru:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, která vrací odchozí IP adresa:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Měli byste vidět, že IP adresa shoduje s statickou IP adresu, které jsou připojené k nástroji pro vyrovnávání zatížení Azure.

## <a name="ingress-controller"></a>Kontroler příchozího přenosu dat

Aby se zabránilo udržování víc veřejných IP adres v nástroji pro vyrovnávání zatížení Azure, vezměte v úvahu pomocí kontroleru příchozího přenosu dat. Příchozí přenos dat řadiče poskytují výhody, jako je Vyrovnávání zatížení, ukončování protokolu SSL/TLS, podporu pro identifikátor URI přepisů a nadřazeného šifrování SSL/TLS. Další informace o řadičích příchozího přenosu dat ve službě AKS najdete v tématu [nakonfigurovat NGINX kontroler příchozího přenosu dat v clusteru AKS] [ ingress-aks-cluster] průvodce.

## <a name="next-steps"></a>Další postup

Další informace o softwaru v tomto dokumentu jsme vám ukázali.

- [Helm CLI][helm-cli-install]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]
- [Odchozí připojení nástroje pro vyrovnávání zatížení Azure][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
