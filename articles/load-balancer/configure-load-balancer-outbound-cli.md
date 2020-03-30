---
title: Konfigurace pravidel vyrovnávání zatížení a odchozích pravidel pomocí rozhraní příkazového příkazu Azure
titleSuffix: Azure Load Balancer
description: Tento článek ukazuje, jak nakonfigurovat vyrovnávání zatížení a odchozí pravidla ve standardním vyrovnávání zatížení pomocí rozhraní příkazového příkazového příkazu KOnI Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225474"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurace vyrovnávání zatížení a pravidel odchozích přenosů ve službě Standard Load Balancer pomocí rozhraní příkazového řádku Azure

Tento rychlý start ukazuje, jak nakonfigurovat odchozí pravidla ve standardním vyvyřiču zatížení pomocí rozhraní příkazového příkazového příkazu Azure.  

Po dokončení obsahuje prostředek vyrovnávání zatížení dva front-endy a pravidla s nimi spojená: jeden pro příchozí a druhý pro odchozí.  Každý front-end má odkaz na veřejnou IP adresu a tento scénář používá jinou veřejnou IP adresu pro příchozí versus odchozí.   Pravidlo vyrovnávání zatížení poskytuje pouze příchozí vyrovnávání zatížení a odchozí pravidlo řídí odchozí NAT pro virtuální hod.  Tento rychlý start používá dva samostatné back-endové fondy, jeden pro příchozí a jeden pro odchozí, pro ilustraci schopnosti a umožňují flexibilitu pro tento scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v umístění *eastus2:*

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s názvem *myvnetoutbound* s podsítí s názvem *mysubnetoutbound* v *myresourcegroupboundbound* pomocí [vytvoření sítě az](https://docs.microsoft.com/cli/azure/network/vnet)sítě .

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Vytvořit příchozí veřejnou IP adresu 

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Load Balancer úrovně Standard podporuje pouze standardní veřejné IP adresy. Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) standardní veřejnou IP adresu s názvem *mypublicipinbound* in *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Vytvořit odchozí veřejnou IP adresu 

Vytvořte standardní IP adresu pro front-endovou konfiguraci nástroje pro vyrovnávání zatížení pomocí [vytvoření veřejné ip sítě az](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Vytvoření Azure Load Balanceru

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - Front-endová IP adresa, která přijímá příchozí síťový provoz v systému vyrovnávání zatížení.
  - Back-endový fond, kde front-endová IP adresa odesílá síťový provoz s vyrovnáváním zatížení.
  - Back-endový fond pro odchozí připojení. 
  - Sonda stavu, která určuje stav instancí back-endového virtuálního soudu.
  - Pravidlo příchozího systému vyrovnávání zatížení, které definuje způsob distribuce provozu na virtuální chod.
  - Odchozí pravidlo pro vyrovnávání zatížení, které definuje způsob distribuce provozu z virtuálních virtuálních pozemních).

### <a name="create-load-balancer"></a>Vytvořit balancer zatížení

Vytvořte nástroj pro vyrovnávání zatížení s příchozí IP adresou pomocí [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem *lb,* která obsahuje příchozí front-endovou konfiguraci IP a back-endový *fond,* který je přidružen k veřejné IP adrese, kterou *jste vytvořili* v předchozím kroku.

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

### <a name="create-outbound-pool"></a>Vytvoření odchozího fondu

Vytvořte další fond back-endových adres a definujte odchozí připojení pro fond virtuálních zařízení s [vytvořením fondu adres az network lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem *bepooloutbound*.  Vytvoření samostatného odchozího fondu poskytuje maximální flexibilitu, ale tento krok můžete vynechat a použít také příchozí *bepoolinbound.*

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Vytvořit odchozí front-endip
Vytvořte odchozí front-endovou konfiguraci IP pro nástroj Provydělávací systém zatížení pomocí [vytvoření front-ip služby AZ network lb,](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) která zahrnuje a odchozí front-endovou konfiguraci IP s názvem *myfrontendoutbound,* která je přidružena k veřejné IP adrese *mypublicipoutbound*

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

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci ip adres front-endu pro příchozí provoz a back-endový fond pro příjem přenosů spolu s požadovaným zdrojovým a cílovým portem. Vytvořte pravidlo pro vyrovnávání zatížení *myinboundlbrule* s [az sítě lb pravidlo vytvořit](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) pro poslech portu 80 v frontend fondu *myfrontendinbound* a odesílání zatížení s vyrovnáváním síťového provozu do fondu adres back-end *také* pomocí portu 80. 

>[!NOTE]
>Toto pravidlo vyrovnávání zatížení zakáže automatické odchozí (S)NAT v důsledku tohoto pravidla s parametrem --disable-outbound-snat. Odchozí NAT je poskytována pouze odchozí pravidlo.

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

Odchozí pravidlo definuje front-endovou veřejnou IP adresu, reprezentovanou frontend *frontendoutbound*, která bude použita pro všechny odchozí přenosy NAT a také pro back-endový fond, na který se toto pravidlo vztahuje.  Vytvořte odchozí pravidlo *myoutboundrule* pro odchozí síťový překlad všech virtuálních počítačů (konfigurace IP síťových připojení) v back-endovém fondu *bepool.*  Následující příkaz také změní časový limit odchozí nečinnosti ze 4 na 15 minut a přidělí 10000 portů SNAT namísto 1024.  Další podrobnosti naleznete v [odchozích pravidlech.](https://aka.ms/lboutboundrules)

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

Pokud nechcete použít samostatný odchozí fond, můžete změnit argument fondu adres v předchozím příkazu a místo toho zadat *bepoolinbound.*  Doporučujeme používat samostatné fondy pro flexibilitu a čitelnost výsledné konfigurace.

V tomto okamžiku můžete pokračovat přidáním virtuálních počítačů do back-endového fondu *bepoolinbound* __a__ *bepooloutbound* aktualizací konfigurace IP příslušných prostředků nic pomocí [přidání az network nic ip-config address-pool](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili standardní vyrovnávání zatížení, nakonfigurované příchozí pravidla provozu vykladače zatížení, nakonfigurované a sondy stavu pro virtuální počítače v back-endovém fondu. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
