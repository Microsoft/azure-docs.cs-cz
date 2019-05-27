---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI | Microsoft Docs
description: Přečtěte si o tom, jak pomocí Azure CLI vytvořit službu Azure Application Gateway, která bude směrovat webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a4f6cc2af7b9e044e5a72767898f876932fbf973
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133956"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Rychlý start: Přímé webového provozu s využitím Azure Application Gateway – rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte, jak používat rozhraní příkazového řádku Azure k vytvoření služby application gateway.  Po vytvoření aplikační brány, můžete otestovat a ujistěte se, že funguje správně. Pomocí služby Azure Application Gateway řízení provozu webové aplikace ke konkrétním prostředkům prostřednictvím přiřazení naslouchacích procesů k portům, vytváření pravidel a přidávání prostředků do back-endový fond. Z důvodu zjednodušení tento článek používá jednoduché uspořádání s veřejnou IP adresu front-endu, základní naslouchací proces pro hostování jedné lokalitě v této brány application gateway, dva virtuální počítače pro back-endového fondu a pravidel směrování základní požadavek.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-cli"></a>Azure CLI

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, spusťte Azure CLI verze 2.0.4 nebo novější. Pokud chcete zjistit verzi, spusťte **příkaz az--verze**. Informace o instalaci nebo upgradu najdete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Skupina prostředků

V Azure přidělení související prostředky do skupiny prostředků. Vytvořte skupinu prostředků s použitím [vytvořit skupiny az](/cli/azure/group#az-group-create). 

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Požadovaným síťovým zdrojům 

U Azure ke komunikaci mezi prostředky, že vytvoříte potřebuje virtuální sítě.  Podsítě služby application gateway může obsahovat jenom aplikační brány. Jsou povoleny žádné další prostředky.  Můžete vytvořit novou podsíť pro aplikační bránu, nebo použijte již existující. V tomto příkladu vytvoříte v tomto příkladu dvě podsítě: jednu pro application gateway a jinou pro back-end serverů. Můžete nakonfigurovat IP front-endu služby Application Gateway jako veřejné a privátní podle vašemu případu použití. V tomto příkladu vybereme možnost veřejná IP adresa front-endu.

Pokud chcete vytvořit virtuální síť a podsíť, můžete použít [az network vnet vytvořit](/cli/azure/network/vnet#az-network-vnet-create). Spustit [az network public-ip vytvořit](/cli/azure/network/public-ip) vytvořit veřejnou IP adresu.

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

### <a name="backend-servers"></a>Back-end serverů

Back-end může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače pro Azure a použít jako servery back-end pro službu application gateway. Také nainstalujte IIS na virtuální počítače k ověření úspěšného vytvoření aplikační brány Azure.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

Nainstalujte [webový server NGINX](https://docs.nginx.com/nginx/) na virtuálních počítačích k ověření aplikace byla úspěšně vytvořena brány. Konfigurační soubor cloud-init můžete použít k instalaci serveru NGINX a spuštění aplikace "Hello World" v Node.js na virtuálním počítači s Linuxem. Další informace o cloud-init najdete v tématu [Cloud-init podporu pro virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Ve službě Azure Cloud Shell, zkopírujte a vložte do něj následující konfiguraci do souboru s názvem *cloud-init.txt*. Zadejte *editor cloud-init.txt* k vytvoření souboru.

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

Vytvořte síťová rozhraní pomocí příkazu [az network nic create](/cli/azure/network/nic#az-network-nic-create). K vytvoření virtuálních počítačů, použijete [az vm vytvořit](/cli/azure/vm#az-vm-create).

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

Vytvoření služby application gateway s využitím [az network application-gateway vytvořit](/cli/azure/network/application-gateway). Když vytvoříte službu application gateway pomocí Azure CLI, zadejte informace o konfiguraci, jako je například kapacity, SKU a HTTP nastavení. Azure pak přidá privátní IP adresy síťových rozhraní jako servery ve fondu back-endu služby application gateway.

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

Může trvat až 30 minut, než Azure k vytvoření služby application gateway. Po vytvoření je můžete zobrazit na následující nastavení **nastavení** část **Application gateway** stránky:

- **appGatewayBackendPool**: Na **back-endové fondy** stránky. Určuje požadované back-endový fond.
- **appGatewayBackendHttpSettings**: Na **nastavení HTTP** stránky. Určuje, že službu application gateway používá port 80 a protokolu HTTP pro komunikaci.
- **appGatewayHttpListener**: Na **naslouchacích procesů stránky**. Určuje výchozí naslouchací proces přidružené **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Na **konfigurací protokolu IP front-endu** stránky. Přiřadí *myAGPublicIPAddress* k **appGatewayHttpListener**.
- **pravidla 1**: Na **pravidla** stránky. Určuje, který je spojen s výchozí pravidlo směrování **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když Azure nevyžaduje, aby webový server NGINX k vytvoření služby application gateway, nainstalovaná v tomto rychlém startu k ověření úspěšného vytvoření aplikační brány Azure. Chcete-li získat veřejnou IP adresu novou aplikační bránu, použijte [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Zkopírujte a vložte veřejnou IP adresu do adresního řádku prohlížeče.
    
![Otestování aplikační brány](./media/quick-create-cli/application-gateway-nginxtest.png)

Když aktualizujete prohlížeč, měli byste vidět název druhého virtuálního počítače. Platné odpovědi ověří, jestli application gateway se úspěšně vytvořil, se může úspěšně připojit s back-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili ve službě application gateway, použijte [odstranění skupiny az](/cli/azure/group#az-group-delete) příkazu odeberte skupinu prostředků. Odstraněním skupiny prostředků je taky odebrat application gateway a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)

