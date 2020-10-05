---
title: 'Rychlý Start: směrování webového provozu pomocí rozhraní příkazového řádku'
titleSuffix: Azure Application Gateway
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit Application Gateway Azure, která směruje webový provoz do virtuálních počítačů v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: fb577947942ffce640f91daf0050f6796f93222b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91311627"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure CLI

V tomto rychlém startu pomocí Azure CLI vytvoříte Aplikační bránu. Pak ho otestujete, abyste se ujistili, že funguje správně. 

Aplikační brána směruje webový provoz aplikace do konkrétních prostředků ve fondu back-end. Posluchačům přiřadíte porty, vytvoříte pravidla a přidáte prostředky do back-endového fondu. V zájmu zjednodušení Tento článek používá jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v aplikační bráně, pravidlo základního směrování požadavku a dva virtuální počítače ve fondu back-end.

Tento rychlý Start můžete také dokončit pomocí [Azure PowerShell](quick-create-powershell.md) nebo [Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure CLI verze 2.0.4 nebo novější](/cli/azure/install-azure-cli) (Pokud spouštíte rozhraní příkazového řádku Azure v místním prostředí).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí `az group create` . 

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.  Můžete buď vytvořit novou podsíť pro Application Gateway nebo použít stávající. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery. IP adresu front-endu Application Gateway můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou IP adresu front-endu.

K vytvoření virtuální sítě a podsítě použijte `az network vnet create` . Spusťte `az network public-ip create` pro vytvoření veřejné IP adresy.

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

## <a name="create-the-backend-servers"></a>Vytvoření back-end serverů

Back-end může obsahovat síťové adaptéry, sady škálování virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domény (FQDN) a back-endy s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače, které budou sloužit jako servery back-end pro aplikační bránu. Nainstalujete také službu IIS na virtuální počítače a otestujete tak Aplikační bránu.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

Nainstalujte webový server NGINX na virtuální počítače, abyste ověřili, že byla služba Application Gateway úspěšně vytvořená. K instalaci NGINX a spuštění aplikace Node.js Hello World na virtuálním počítači se systémem Linux můžete použít konfigurační soubor Cloud-init. Další informace o cloud-init najdete v tématu [Podpora Cloud-init pro virtuální počítače v Azure](../virtual-machines/linux/using-cloud-init.md).

Do Azure Cloud Shell zkopírujte a vložte následující konfiguraci do souboru s názvem *cloud-init.txt*. Zadejte *editor cloud-init.txt* pro vytvoření souboru.

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

Vytvořte síťová rozhraní pomocí nástroje `az network nic create` . Pokud chcete vytvořit virtuální počítače, použijte `az vm create` .

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

Vytvořte Aplikační bránu pomocí `az network application-gateway create` . Při vytváření aplikační brány pomocí Azure CLI zadáte informace o konfiguraci, například kapacitu, SKU a nastavení HTTP. Azure potom přidá privátní IP adresy síťových rozhraní jako servery ve fondu back-end služby Application Gateway.

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

- **appGatewayBackendPool**: nachází se na stránce **back-end fondy** . Určuje požadovaný back-end fond.
- **appGatewayBackendHttpSettings**: nachází se na stránce **nastavení protokolu HTTP** . Určuje, že Aplikační brána používá port 80 a protokol HTTP pro komunikaci.
- **appGatewayHttpListener**: nachází se na **stránce naslouchací procesy**. Určuje výchozí naslouchací proces přidružený k **appGatewayBackendPool**.
- **appGatewayFrontendIP**: nachází se na stránce **Konfigurace IP adresy front-endu** . Přiřadí *myAGPublicIPAddress* k **appGatewayHttpListener**.
- **rule1**: nachází se na stránce **pravidel** . Určuje výchozí pravidlo směrování, které je přidruženo k **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když Azure nepotřebuje webový server NGINX k vytvoření aplikační brány, nainstalujete ho v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Pokud chcete získat veřejnou IP adresu nové služby Application Gateway, použijte `az network public-ip show` . 

```azurecli-interactive
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

Pokud již nepotřebujete prostředky, které jste vytvořili pomocí služby Application Gateway, `az group delete` odstraňte skupinu prostředků pomocí příkazu. Když odstraníte skupinu prostředků, odstraníte také aplikační bránu a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)

