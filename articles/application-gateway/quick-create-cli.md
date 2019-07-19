---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI | Microsoft Docs
description: Naučte se používat Azure CLI k vytvoření Application Gateway Azure, která směruje webový provoz do virtuálních počítačů v back-endu fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd68f4a565c28f1dbac7e94442a8f8231af01328
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314898"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Rychlý start: Přímý webový provoz pomocí Azure Application Gateway – Azure CLI

V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit Aplikační bránu.  Po vytvoření služby Application Gateway ji otestujete, abyste se ujistili, že funguje správně. V případě Azure Application Gateway nasměrujete webový provoz aplikace na konkrétní prostředky přiřazením posluchačů k portům, vytvořením pravidel a přidáním prostředků do back-endového fondu. Tento článek používá jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v aplikační bráně, dva virtuální počítače používané pro back-end fond a pravidlo základního směrování požadavku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-cli"></a>Azure CLI

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, spusťte Azure CLI verze 2.0.4 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz **AZ--Version**. Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Resource group

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). 

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Požadované síťové prostředky 

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.  Můžete buď vytvořit novou podsíť pro Application Gateway nebo použít stávající. V tomto příkladu vytvoříte v tomto příkladu dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery. IP adresu front-endu Application Gateway můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.

Chcete-li vytvořit virtuální síť a podsíť, použijte příkaz [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Spusťte [AZ Network Public-IP Create](/cli/azure/network/public-ip) k vytvoření veřejné IP adresy.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

### <a name="backend-servers"></a>Back-endové servery

Back-end může obsahovat síťové adaptéry, sady škálování virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domény (FQDN) a back-endy s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače, které budou sloužit jako servery back-end pro aplikační bránu. Nainstalujete také službu IIS na virtuální počítače a otestujete tak Aplikační bránu.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

Nainstalujte [webový server Nginx](https://docs.nginx.com/nginx/) na virtuální počítače, abyste ověřili, že byla služba Application Gateway úspěšně vytvořená. Pomocí konfiguračního souboru Cloud-init můžete nainstalovat NGINX a spustit aplikaci Node. js "Hello World" na virtuálním počítači se systémem Linux. Další informace o cloud-init najdete v tématu [Podpora Cloud-init pro virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

V Azure Cloud Shell zkopírujte následující konfiguraci do souboru s názvem *Cloud-init. txt*a vložte ho do něj. Zadejte *Editor Cloud-init. txt* pro vytvoření souboru.

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

Vytvořte síťová rozhraní pomocí příkazu [az network nic create](/cli/azure/network/nic#az-network-nic-create). Pokud chcete vytvořit virtuální počítače, použijte příkaz [AZ VM Create](/cli/azure/vm#az-vm-create).

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

Pomocí [AZ Network Application-Gateway Create](/cli/azure/network/application-gateway)vytvořte Aplikační bránu. Při vytváření aplikační brány pomocí Azure CLI zadáte informace o konfiguraci, například kapacitu, SKU a nastavení HTTP. Azure potom přidá privátní IP adresy síťových rozhraní jako servery ve fondu back-end služby Application Gateway.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Může trvat až 30 minut, než Azure vytvoří Aplikační bránu. Po vytvoření můžete zobrazit následující nastavení v části **Nastavení** na stránce **aplikační brány** :

- **appGatewayBackendPool**: Nachází se na stránce **back-end fondy** . Určuje požadovaný back-end fond.
- **appGatewayBackendHttpSettings**: Nachází se na stránce **nastavení protokolu HTTP** . Určuje, že Aplikační brána používá port 80 a protokol HTTP pro komunikaci.
- **appGatewayHttpListener**: Umístěný na **stránce naslouchací procesy**. Určuje výchozí naslouchací proces přidružený k **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Nachází se na stránce **Konfigurace IP adresy front-endu** . Přiřadí *myAGPublicIPAddress* k **appGatewayHttpListener**.
- **rule1**: Nachází se na stránce **pravidla** . Určuje výchozí pravidlo směrování, které je přidruženo k **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když Azure nepotřebuje webový server NGINX k vytvoření aplikační brány, nainstalujete ho v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Pokud chcete získat veřejnou IP adresu nové služby Application Gateway, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče.
    
![Otestování aplikační brány](./media/quick-create-cli/application-gateway-nginxtest.png)

Když aktualizujete prohlížeč, měl by se zobrazit název druhého virtuálního počítače. To znamená, že služba Application Gateway byla úspěšně vytvořena a může se připojit k back-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) . Odebráním skupiny prostředků odeberete také aplikační bránu a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)

