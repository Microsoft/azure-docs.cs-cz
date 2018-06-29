---
title: Seznam povolených adres odchozí provoz z clusteru Azure Kubernetes služby (AKS)
description: Seznam povolených adres odchozí provoz z clusteru služby Azure Kubernetes služby (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100141"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure odchozí Kubernetes služby (AKS)

Ve výchozím nastavení je náhodně přiřadí adresu odchozí z clusteru Azure Kubernetes služby (AKS). Tato konfigurace není ideální, pokud museli identifikovat IP adresu pro přístup k externích služeb. Tento dokument podrobně popisuje postup vytváření a údržbu na IP adresu staticky přiřazené odchozí v clusteru služby AKS.

## <a name="egress-overview"></a>Přehled odchozí

Odchozí provoz z clusteru služby AKS dodržovat konvence prostředí vyrovnávání zatížení Azure, které jsou popsány [sem][outbound-connections]. Před prvním Kubernetes služby typu `LoadBalancer` je vytvořen, agent uzly nejsou součástí žádného fondu vyrovnávání zatížení Azure. V této konfiguraci uzly jsou bez úrovni instance veřejnou IP adresu. Azure překládá odchozího toku veřejné zdrojové IP adresy, které nejsou konfigurovatelné nebo deterministický.

Jednou Kubernetes službu typu `LoadBalancer` je vytvořen, agent uzly jsou přidány do fondu vyrovnávání zatížení Azure. Pro odchozí tok Azure převede do první veřejnou IP adresou nakonfigurovanou na nástroje pro vyrovnávání zatížení.

## <a name="create-a-static-public-ip"></a>Vytvoření statické veřejné IP adresy

Chcete-li zabránit používá náhodné IP adresy, vytvořte statickou IP adresu a ujistěte se, že služba Vyrovnávání zatížení používá tuto adresu. IP adresa musí být vytvořen v AKS **uzlu** skupinu prostředků.

Získat název skupiny prostředků s [az prostředků zobrazit] [ az-resource-show] příkaz. Aktualizujte název skupiny prostředků a název clusteru tak, aby odpovídaly prostředí.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Pak pomocí [vytvoření veřejné sítě az-ip] [ public-ip-create] příkaz pro vytvoření statickou veřejnou IP adresu. Aktualizujte název skupiny prostředků tak, aby odpovídaly gatherred název v posledním kroku.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Vytvoření služby se statickou IP adresu

Teď, když máte IP adresu, vytvoření Kubernetes služby s typem `LoadBalancer` a přiřadit IP adresu ke službě.

Vytvořte soubor s názvem `egress-service.yaml` a zkopírujte následující YAML. Aktualizujte IP adresu, kterou odpovídaly vašemu prostředí.

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

Vytvoření služby a nasazení pomocí `kubectl apply` příkaz.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Vytvoření této služby Konfiguruje novou IP front-endu na nástroje pro vyrovnávání zatížení Azure. Pokud nemáte žádné jiné IP adresy konfigurace, potom **všechny** odchozí provoz by měl nyní použijte tuto adresu. Pokud na Vyrovnávání zatížení Azure jsou nakonfigurované více adresy, odchozí použije první IP na tento nástroj pro vyrovnávání zatížení.

## <a name="verify-egress-address"></a>Ověřte adresu odchozí

Pokud chcete ověřit, jestli se používá veřejnou IP adresu, použijte službu `checkip.dyndns.org`.

Spuštění a přiřadit pod:

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

Měli byste vidět, že IP adresa odpovídá statickou IP adresu, které jsou připojené k nástroji pro vyrovnávání zatížení Azure.

## <a name="ingress-controller"></a>Příjem příchozích dat řadiče

Abyste se vyhnuli, udržování víc veřejných IP adres zařízení na Vyrovnávání zatížení Azure, zvažte použití řadič příchozí. Příjem příchozích dat řadiče poskytují výhod, třeba Vyrovnávání zatížení, ukončení protokolu SSL/TLS, se podpora pro identifikátor URI přepisů a nadřazeného šifrování SSL/TLS. Další informace o řadičích příjem příchozích dat v AKS najdete v tématu [konfigurace NGINX příjem příchozích dat řadiče v clusteru služby AKS] [ ingress-aks-cluster] průvodce.

## <a name="next-steps"></a>Další postup

Další informace o softwaru ukázáno v tomto dokumentu.

- [Helm rozhraní příkazového řádku][helm-cli-install]
- [Řadič NGINX příjem příchozích dat][nginx-ingress]
- [Odchozí připojení nástroje pro vyrovnávání zatížení Azure][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
