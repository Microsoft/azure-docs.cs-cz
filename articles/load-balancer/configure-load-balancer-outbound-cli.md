---
title: Konfigurace vyrovnávání zatížení a odchozích pravidel pomocí Azure CLI
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat pravidla vyrovnávání zatížení a odchozí pravidla v Standard Load Balancer pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225474"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurace vyrovnávání zatížení a pravidel odchozích přenosů ve službě Standard Load Balancer pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte, jak nakonfigurovat odchozí pravidla v Standard Load Balancer pomocí Azure CLI.  

Až to budete mít, prostředek Load Balancer obsahuje dva front-endové a pravidla, která jsou k nim přidružená: jedno pro příchozí a další pro odchozí.  Každý front-end má odkaz na veřejnou IP adresu a tento scénář používá pro příchozí a odchozí komunikaci jinou veřejnou IP adresu.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení a odchozí pravidlo řídí odchozí překlad adres (NAT) poskytovaný pro virtuální počítač.  Tento rychlý Start používá dva samostatné back-end fondy, jeden pro příchozí a jeden pro odchozí, pro ilustraci možností a umožňuje flexibilitu pro tento scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v umístění *eastus2* :

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s názvem *myvnetoutbound* s podsítí s názvem *mysubnetoutbound* v *myresourcegroupoutbound* pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Vytvoření příchozí veřejné IP adresy 

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Load Balancer úrovně Standard podporuje pouze standardní veřejné IP adresy. Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte standardní veřejnou IP adresu s názvem *mypublicipinbound* v *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Vytvoření odchozí veřejné IP adresy 

Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip)vytvořte standardní IP adresu pro odchozí konfiguraci front-endu Load Balancer.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Vytvoření Azure Load Balanceru

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - IP adresa front-endu, která přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  - Back-end fond, ve kterém front-end IP odesílá síťový provoz s vyrovnáváním zatížení.
  - Back-end fond pro odchozí připojení. 
  - Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  - Příchozí pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.
  - Odchozí pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu z virtuálních počítačů.

### <a name="create-load-balancer"></a>Vytvořit Load Balancer

Vytvořte Load Balancer s příchozí IP adresou pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) Create s názvem disendy, který zahrnuje příchozí IP adresu front-endu a back-end *fondu, který* je přidružený k *mypublicipinboundu* veřejné IP *adresy, kterou* jste vytvořili v předchozím kroku.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Vytvořit odchozí fond

Vytvořte další fond back-end adres pro definování odchozího připojení pro fond virtuálních počítačů pomocí [AZ Network disendu Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem *bepooloutbound*.  Vytvoření samostatného odchozího fondu poskytuje maximální flexibilitu, ale tento krok můžete vynechat a zároveň použít jenom příchozí *bepoolinbound* .

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Vytvořit odchozí IP adresu front-endu
Vytvořte odchozí nastavení IP adresy front-endu pro Load Balancer pomocí [AZ Network disendu-IP Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) , která zahrnuje a odchozí IP adresu front-endu s názvem *myfrontendoutbound* , která je přidružená k veřejné IP adrese *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Vytvořit sondu stavu

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Vytvořit pravidlo vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci IP adresy front-endu pro příchozí provoz a back-end fond pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení *myinboundlbrule* pomocí [AZ Network diskont Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) pro naslouchání na portu 80 ve front-endu *myfrontendinbound* a odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres *bepool* také pomocí portu 80. 

>[!NOTE]
>Toto pravidlo vyrovnávání zatížení zakáže v důsledku tohoto pravidla automatických odchozích překladů adres (NAT) s parametrem--DISABLE-Outbound-SNAT. Odchozí NAT je poskytováno pouze odchozím pravidlem.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

Odchozí pravidlo definuje veřejnou IP adresu front-end, která je reprezentovaná *myfrontendoutbound*front-endu, která se bude používat pro všechny odchozí přenosy NAT i pro back-end fond, na který se toto pravidlo vztahuje.  Vytvořte odchozí pravidlo *myoutboundrule* pro odchozí síťový překlad všech virtuálních počítačů (konfigurace IP adresy nic) ve fondu back-endu *bepool* .  Následující příkaz také změní odchozí časový limit nečinnosti ze 4 na 15 minut a přiděluje 10000 portů SNAT namísto 1024.  Další podrobnosti najdete v podrobnostech o [odchozích pravidlech](https://aka.ms/lboutboundrules) .

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Pokud nechcete používat samostatný odchozí fond, můžete místo toho v předchozím příkazu změnit argument fondu adres, aby bylo možné zadat *bepoolinbound* .  Pro zajištění flexibility a čitelnosti výsledné konfigurace doporučujeme použít samostatné fondy.

V tuto chvíli můžete pokračovat přidáním svého virtuálního počítače do back-end fondu *bepoolinbound* __a__ *bepooloutbound* tak, že aktualizujete konfiguraci IP adres příslušných prostředků nic pomocí [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili Standard Load Balancer, nakonfigurovali jste pravidla pro příchozí provoz vyrovnávání zatížení, nakonfigurovaná a sonda stavu pro virtuální počítače ve fondu back-endu. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
