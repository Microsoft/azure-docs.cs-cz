---
title: 'Kurz: Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi pomocí Azure CLI'
titleSuffix: Azure Load Balancer
description: Začněte s tímto kurzem nasazení Azure Load Balancer mezi oblastmi pomocí Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225179"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Kurz: vytvoření Azure Load Balancer pro různé oblasti pomocí rozhraní příkazového řádku Azure

Nástroj pro vyrovnávání zatížení mezi oblastmi zajišťuje globálně dostupnou službu napříč několika oblastmi Azure. Pokud dojde k výpadku jedné oblasti, provoz se směruje na další nejbližší zdravý Nástroj pro vyrovnávání zatížení.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte Nástroj pro vyrovnávání zatížení mezi oblastmi.
> * Vytvořte pravidlo nástroje pro vyrovnávání zatížení.
> * Vytvořte back-end fond, který obsahuje dvě místní nástroje pro vyrovnávání zatížení.
> * Otestujte Nástroj pro vyrovnávání zatížení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.
- Dvě **standardní** SKU služby Vyrovnávání zatížení Azure s back-end fondy nasazenými ve dvou různých oblastech Azure.
    - Informace o tom, jak vytvořit místní standardní nástroj pro vyrovnávání zatížení a virtuální počítače pro back-endové fondy, najdete v tématu [rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure CLI](quickstart-load-balancer-standard-public-cli.md).
        - Připojit názvy nástrojů pro vyrovnávání zatížení a virtuálních počítačů do každé oblasti s přepínači **-R1** a **-R2**. 
- Rozhraní příkazového řádku Azure je nainstalované místně nebo Azure Cloud Shell.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Přihlášení k rozhraní příkazového řádku Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi

V této části vytvoříte Nástroj pro vyrovnávání zatížení mezi oblastmi, veřejnou IP adresu a pravidlo vyrovnávání zatížení.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create):

* S názvem **myResourceGroupLB-CR**.
* V umístění **westus** .

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi pomocí [AZ Network mezi oblastmi-](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)diskont Create:

* S názvem **myLoadBalancer-CR**.
* Front-endového fondu s názvem **myFrontEnd-CR**.
* Back-end fond s názvem **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network mezi oblastmi – vytvořit pravidlo](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Pojmenovaný **myHTTPRule-CR**
* Naslouchání na **portu 80** ve front **-Endu myFrontEnd-CR**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool-CR** pomocí **portu 80**. 
* Protokol **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Vytvoření back-endového fondu

V této části přidáte do back-endu nástroje pro vyrovnávání zatížení mezi oblastmi dva místní standardní nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> Pokud chcete tento postup provést, ujistěte se, že ve vašem předplatném je nasazené dvě místní nástroje pro vyrovnávání zatížení s back-end fondy.  Další informace najdete v tématu **[rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí](quickstart-load-balancer-standard-public-cli.md)** rozhraní příkazového řádku Azure.

### <a name="add-the-regional-frontends-to-load-balancer"></a>Přidání místních front-endu do nástroje pro vyrovnávání zatížení

V této části umístíte ID prostředků dvou místních nástrojů pro vyrovnávání zatížení, které se probíhají do proměnných.  Pak použijete proměnné k přidání front-endu do fondu back-end adres nástroje pro vyrovnávání zatížení mezi oblastmi.

Načtěte ID prostředků pomocí [AZ Network Endu-IP show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

K přidání front-endu, které jste umístili do proměnných ve fondu back-end nástroje pro vyrovnávání zatížení mezi oblastmi, použijte [příkaz AZ Network mezi oblastmi a adresou fondu přidat](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) .

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

V této části otestujete Nástroj pro vyrovnávání zatížení mezi oblastmi. Připojíte se k veřejné IP adrese ve webovém prohlížeči.  Virtuální počítače zabráníte v jednom z back-endového fondu služby pro vyrovnávání zatížení a budete sledovat převzetí služeb při selhání.

1. Pokud chcete získat veřejnou IP adresu nástroje pro vyrovnávání zatížení, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

3. Zastavte virtuální počítače v back-end fondu jednoho z regionálních nástrojů pro vyrovnávání zatížení.

4. Aktualizujte webový prohlížeč a sledujte převzetí služeb při selhání připojení k druhému místnímu nástroji pro vyrovnávání zatížení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Vytvořil se nástroj pro vyrovnávání zatížení mezi oblastmi.
* Bylo vytvořeno pravidlo vyrovnávání zatížení.
* Přidání místních nástrojů pro vyrovnávání zatížení do back-endu pro nástroj pro vyrovnávání zatížení mezi oblastmi.
* Otestování nástroje pro vyrovnávání zatížení.

V dalším článku se naučíte, jak...
> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
