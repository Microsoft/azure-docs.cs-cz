---
title: 'Úvodní příručka: Přímý webový provoz pomocí rozhraní se ksisa'
titleSuffix: Azure Application Gateway
description: Zjistěte, jak pomocí rozhraní příkazového příkazového příkazu Azure vytvořit aplikační bránu Azure, která směruje webový provoz do virtuálních počítačů v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f60b26756c0affffbd45c8596fdf73d11ffa8e81
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239516"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI

V tomto rychlém startu použijete azure cli k vytvoření aplikační brány. Pak jej otestujete, abyste se ujistili, že funguje správně. 

Aplikační brána směruje webový provoz aplikací na konkrétní prostředky v back-endovém fondu. Naslouchací procesy přiřazujete k portům, vytváříte pravidla a přidáváte prostředky do back-endového fondu. Z důvodu jednoduchosti tento článek používá jednoduché nastavení s veřejnou front-endovou IP adresou, základní naslouchací proces pro hostování jedné sítě na aplikační bráně, základní pravidlo směrování požadavků a dva virtuální počítače v back-endovém fondu.

Tento rychlý start můžete taky dokončit pomocí [Azure PowerShellu](quick-create-powershell.md) nebo [portálu Azure](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure CLI verze 2.0.4 nebo novější](/cli/azure/install-azure-cli) (pokud spustíte Azure CLI místně).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělujete související prostředky skupině prostředků. Vytvořte skupinu `az group create`prostředků pomocí aplikace . 

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat pouze aplikační brány. Nejsou povoleny žádné další prostředky.  Můžete buď vytvořit novou podsíť pro aplikační bránu, nebo použít existující. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu. Front-endovou IP adresu aplikační brány můžete nakonfigurovat jako veřejnou nebo soukromou podle případu použití. V tomto příkladu zvolíte veřejnou front-endovou IP adresu.

Chcete-li vytvořit virtuální síť `az network vnet create`a podsíť, použijte . Spuštěním `az network public-ip create` vytvořte veřejnou IP adresu.

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

## <a name="create-the-backend-servers"></a>Vytvoření back-endových serverů

Back-end může mít síťové karty, škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domén (FQDN) a back-endy s více tenanty, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače, které se použijí jako back-endové servery pro aplikační bránu. Službu IIS můžete také nainstalovat do virtuálních počítačů a otestovat bránu aplikace.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

Nainstalujte webový server NGINX do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena. K instalaci NGINX a spuštění aplikace Node.js "Hello World" na virtuálním počítači s Linuxem můžete použít konfigurační soubor cloud-init. Další informace o cloud-init, najdete [v tématu Podpora Cloud-init pro virtuální počítače v Azure](../virtual-machines/linux/using-cloud-init.md).

Ve svém Prostředí Azure Cloud Shell zkopírujte a vložte následující konfiguraci do souboru s názvem *cloud-init.txt*. Chcete-li soubor vytvořit, zadejte *editor cloud-init.txt.*

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

Vytvořte síťová `az network nic create`rozhraní s programem . Chcete-li vytvořit virtuální `az vm create`počítače, použijte .

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

Vytvořte aplikační `az network application-gateway create`bránu pomocí aplikace . Při vytváření aplikační brány pomocí příkazového příkazu k řešení Azure zadáte informace o konfiguraci, jako je kapacita, skladová položka a nastavení HTTP. Azure pak přidá privátní IP adresy síťových rozhraní jako servery v back-endovém fondu aplikační brány.

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

Vytvoření aplikační brány může azure trvat až 30 minut. Po vytvoření můžete zobrazit následující nastavení v části **Nastavení** na stránce **Aplikační brána:**

- **appGatewayBackendPool**: Nachází se na stránce **back-endových fondů.** Určuje požadovaný back-endový fond.
- **appGatewayBackendHttpSettings**: Nachází se na stránce **nastavení HTTP.** Určuje, že aplikační brána používá port 80 a protokol HTTP pro komunikaci.
- **appGatewayHttpListener**: Nachází se na **stránce Naslouchací procesy**. Určuje výchozí naslouchací proces přidružený k **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Nachází se na stránce **Konfigurace IP front-endu.** Přiřazuje *adresu myAGPublicIPAddress* **společnosti appGatewayHttpListener**.
- **pravidlo1**: Nachází se na stránce **Pravidla.** Určuje výchozí pravidlo směrování, které je přidruženo k **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

Přestože Azure k vytvoření aplikační brány nevyžaduje webový server NGINX, nainstalovali jste ho v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil aplikační bránu. Chcete-li získat veřejnou IP adresu `az network public-ip show`nové aplikační brány, použijte . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Zkopírujte a vložte veřejnou IP adresu do adresního řádku prohlížeče.
    
![Otestování aplikační brány](./media/quick-create-cli/application-gateway-nginxtest.png)

Při aktualizaci prohlížeče, měli byste vidět název druhého virtuálního počítače. To znamená, že aplikační brána byla úspěšně vytvořena a může se připojit k back-endu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili `az group delete` pomocí aplikační brány, pomocí příkazu odstraňte skupinu prostředků. Když odstraníte skupinu prostředků, odstraníte také bránu aplikace a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)

