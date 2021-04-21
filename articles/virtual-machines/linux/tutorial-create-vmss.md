---
title: 'Kurz: vytvoření sady škálování virtuálních počítačů se systémem Linux'
description: Naučte se používat rozhraní příkazového řádku Azure CLI k vytvoření a nasazení vysoce dostupné aplikace na virtuálních počítačích se systémem Linux pomocí sady škálování virtuálních počítačů.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.date: 06/01/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 4d31bde05158e89168f2a67b820c8743d4cd2729
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769890"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux-with-the-azure-cli"></a>Kurz: Vytvoření škálovací sady virtuálních počítačů a nasazení vysoce dostupné aplikace v Linuxu pomocí Azure CLI

Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto kurzu nasadíte škálovací sadu virtuálních počítačů v Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití cloud-init k vytvoření aplikace určené ke škálování
> * Vytvoření škálovací sady virtuálních počítačů
> * Zvýšení nebo snížení počtu instancí ve škálovací sadě
> * Vytvoření pravidel automatického škálování
> * Zobrazení informací o připojení instancí škálovací sady
> * Použití datových disků ve škálovací sadě

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="scale-set-overview"></a>Přehled škálovací sady
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Virtuální počítače ve škálovací sadě se distribuují napříč logickými doménami selhání a aktualizačními doménami v jedné nebo více *skupinách umístění*. To jsou skupiny podobně nakonfigurovaných virtuálních počítačů, podobně jako [skupiny dostupnosti](tutorial-availability-sets.md).

Virtuální počítače se ve škálovací sadě vytvářejí podle potřeby. Pro řízení, jak a kdy se virtuální počítače přidávají do škálovací sady nebo se z ní odebírají, definujete pravidla automatického škálování. Tato pravidla se můžou aktivovat na základě metrik, jako jsou zatížení procesoru, využití paměti nebo síťový provoz.

Škálovací sady podporují až 1 000 virtuálních počítačů, pokud použijete image platformy Azure. Pro úlohy se značnými požadavky na instalaci nebo přizpůsobení virtuálních počítačů možná budete chtít [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md). Pokud použijete vlastní image, můžete ve škálovací sadě vytvořit až 300 virtuálních počítačů.


## <a name="create-an-app-to-scale"></a>Vytvoření aplikace určené ke škálování
Pro produkční použití možná budete chtít [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md) obsahující vaši nainstalovanou a nakonfigurovanou aplikaci. Pro účely tohoto kurzu přizpůsobíme virtuální počítače při prvním spuštění, abychom rychle viděli škálovací sadu v akci.

V předchozím kurzu jste zjistili, [jak přizpůsobit virtuální počítač s Linuxem při prvním spuštění](tutorial-automate-vm-deployment.md) pomocí cloud-init. Stejný konfigurační soubor cloud-init můžete použít k instalaci serveru NGINX a spuštění jednoduché aplikace Hello World v Node.js.

V aktuálním prostředí vytvořte soubor *cloud-init.txt* a vložte do něj následující konfiguraci. Soubor vytvořte například v Cloud Shellu, pokud nepracujete na místním počítači. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

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


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroupScaleSet* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroupScaleSet --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#az_vmss_create). Následující příklad vytvoří škálovací sadu *myScaleSet*, pomocí souboru cloud-init přizpůsobí virtuální počítač a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Než k aplikaci budete mít přístup, může to ještě několik minut trvat.


## <a name="allow-web-traffic"></a>Povolení webového provozu
Jako součást škálovací sady virtuálních počítačů se automaticky vytvořil nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení s využitím pravidel nástroje pro vyrovnávání zatížení distribuuje provoz mezi sadu definovaných virtuálních počítačů. Další informace o konceptech a konfiguraci nástroje pro vyrovnávání zatížení najdete v dalším kurzu [Vyrovnávání zatížení virtuálních počítačů v Azure](tutorial-load-balancer.md).

Pokud chcete webové aplikaci povolit příjem provozu, vytvořte pravidlo pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Následující příklad vytvoří pravidlo *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testování aplikace
Pokud chcete zobrazit svou aplikaci Node.js na webu, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Následující příklad získá IP adresu *myScaleSetLBPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Zadejte veřejnou IP adresu do webového prohlížeče. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz:

![Spuštěná aplikace Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Pokud chcete vidět škálovací sadu v akci, můžete vynutit aktualizaci webového prohlížeče a zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny virtuální počítače, na kterých je vaše aplikace spuštěná.


## <a name="management-tasks"></a>Úlohy správy
V průběhu životního cyklu škálovací sady možná budete potřebovat spustit jednu nebo více úloh správy. Kromě toho možná budete chtít vytvořit skripty pro automatizaci různých úloh souvisejících s životním cyklem. Azure CLI nabízí rychlý způsob, jak tyto úlohy provést. Tady je několik běžných úloh.

### <a name="view-vms-in-a-scale-set"></a>Zobrazení virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam virtuálních počítačů spuštěných ve vaší škálovací sadě, použijte příkaz [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) následujícím způsobem:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

Výstup se podobá následujícímu příkladu:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="manually-increase-or-decrease-vm-instances"></a>Ruční zvýšení nebo snížení počtu instancí virtuálních počítačů
Pokud chcete zobrazit počet instancí, které aktuálně máte ve škálovací sadě, použijte příkaz [az vmss show](/cli/azure/vmss#az_vmss_show) s dotazem na *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pak můžete ručně navýšit nebo snížit počet virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss scale](/cli/azure/vmss#az_vmss_scale). Následující příklad nastaví počet virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```

### <a name="get-connection-info"></a>Získání informací o připojení
Pokud chcete získat informace o připojení virtuálních počítačů ve škálovací sadě, použijte příkaz [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info). Tento příkaz pro všechny virtuální počítače vypíše veřejnou IP adresu a port, pomocí kterých se k nim můžete připojit přes SSH:

```azurecli-interactive
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Použití datových disků se škálovacími sadami
Můžete vytvořit datové disky a použít je se škálovacími sadami. V předchozím kurzu jste zjistili, jak [spravovat disky Azure](tutorial-manage-disks.md), a seznámili jste se s osvědčenými postupy a vylepšeními výkonu pro sestavování aplikací na datových discích místo disku s operačním systémem.

### <a name="create-scale-set-with-data-disks"></a>Vytvoření škálovací sady s datovými disky
Pokud chcete vytvořit škálovací sadu a připojit k ní datové disky, přidejte do příkazu [az vmss create](/cli/azure/vmss#az_vmss_create) parametr `--data-disk-sizes-gb`. Následující příklad vytvoří škálovací sadu s *50GB* datovým diskem připojeným ke každé instanci:

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Při odebírání instancí ze škálovací sady se odeberou také všechny připojené datové disky.

### <a name="add-data-disks"></a>Použití datových disků
Pokud chcete k instancím ve škálovací sadě přidat datový disk, použijte příkaz [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). Následující příklad přidá ke každé instanci *50* GB disk:

```azurecli-interactive
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Odpojení datových disků
Pokud chcete z instancí ve škálovací sadě odebrat datový disk, použijte příkaz [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). Následující příklad z každé instance odebere datový disk na logické jednotce *2*:

```azurecli-interactive
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili škálovací sadu virtuálních počítačů. Naučili jste se:

> [!div class="checklist"]
> * Použití cloud-init k vytvoření aplikace určené ke škálování
> * Vytvoření škálovací sady virtuálních počítačů
> * Zvýšení nebo snížení počtu instancí ve škálovací sadě
> * Vytvoření pravidel automatického škálování
> * Zobrazení informací o připojení instancí škálovací sady
> * Použití datových disků ve škálovací sadě

Přejděte k dalšímu kurzu, kde najdete další informace o konceptech vyrovnávání zatížení virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vyrovnání zatížení virtuálních počítačů](tutorial-load-balancer.md)
