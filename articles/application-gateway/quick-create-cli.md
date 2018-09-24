---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI | Microsoft Docs
description: Přečtěte si o tom, jak pomocí Azure CLI vytvořit službu Azure Application Gateway, která bude směrovat webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 62c4e51cd160ed7830eb42943225847857dc4963
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963624"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI

Pomocí služby Azure Application Gateway můžete směrovat webový provoz aplikací na konkrétní prostředky tak, že přiřadíte naslouchací procesy portům, vytvoříte pravidla a přidáte prostředky do back-endového fondu.

V tomto rychlém startu se dozvíte, jak pomocí Azure CLI rychle vytvořit aplikační bránu se dvěma virtuálními počítači v back-endovém fondu. Také se naučíte bránu otestovat, abyste si mohli ověřit, že správně funguje.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Prostředky je potřeba vytvářet vždy ve skupině prostředků. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Aby mohla aplikační brána komunikovat s jinými prostředky, musíte vytvořit virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro virtuální počítače. 

Vytvořte virtuální síť a podsíť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az-public-ip-create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, které se použijí jako servery back-end pro aplikační bránu. 

### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

Také na virtuální počítače nainstalujete server NGINX, abyste ověřili, že se aplikační brána úspěšně vytvořila. K instalaci serveru NGINX a spuštění aplikace Hello World v Node.js na virtuálním počítači s Linuxem můžete použít konfigurační soubor cloud-init. 

V aktuálním prostředí vytvořte soubor cloud-init.txt a zkopírujte následující konfiguraci a vložte ji do prostředí. Ujistěte se, že správně kopírujete celý soubor cloud-init, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Vytvořte síťová rozhraní pomocí příkazu [az network nic create](/cli/azure/network/nic#az-network-nic-create). Vytvořte virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte aplikační bránu pomocí příkazu [az network application-gateway create](/cli/azure/network/application-gateway#az-application-gateway-create). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Privátní IP adresy síťových rozhraní se přidají jako servery do back-endového fondu aplikační brány.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Vytváření aplikační brány může trvat až 30 minut. Po vytvoření aplikační brány se zobrazí tyto její funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Instalace serveru NGINX není pro vytvoření aplikační brány nutná, v tomto rychlém startu jste ji ale provedli, abyste mohli ověřit, jestli se aplikační brána úspěšně vytvořila. K získání veřejné IP adresy aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Otestování aplikační brány](./media/quick-create-cli/application-gateway-nginxtest.png)

Po aktualizaci prohlížeče by se měl zobrazit název druhého virtuálního počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejdřív prozkoumejte prostředky vytvořené pomocí aplikační brány a potom, až je nebudete potřebovat, odstraňte pomocí příkazu [az group delete](/cli/azure/group#az-group-delete) skupinu prostředků, aplikační bránu a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)

