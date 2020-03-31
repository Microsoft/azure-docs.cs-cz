---
title: Více web hosting pomocí CLI - Azure Application Gateway
description: Zjistěte, jak vytvořit aplikační bránu, která hostuje více webů pomocí azure cli.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 0e58d9ecfbd0731fc9bf91664763e73d8c56e64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294757"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Vytvoření aplikační brány s více hostováním webu pomocí azure cli

Rozhraní příkazového odpočiženého nastavení příkazu Azure můžete použít ke konfiguraci [hostování více webových serverů](application-gateway-multi-site-overview.md) při vytváření aplikační [brány](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí škálovacích sad virtuálních počítačů. Pak na základě domén, které vám patří, nakonfigurujete naslouchací procesy a pravidla, aby se webový provoz přesměroval na příslušné servery ve fondech. Tento kurz předpokládá, že vlastníte více domén `www.contoso.com` `www.fabrikam.com`a používá příklady a .

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření služby Application Gateway
> * Vytvoření naslouchacích procesů a pravidel směrování
> * Vytvořit z back-endových fondů škálovací sadu virtuálních počítačů
> * Vytvoření záznamu CNAME v doméně

![Příklad směrování na více webů](./media/tutorial-multisite-cli/scenario.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom můžete přidat podsíť s názvem *myBackendSubnet*, kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

### <a name="add-the-backend-pools"></a>Přidání back-endových fondů

Přidejte back-endové fondy s názvem *contosoPool* a *fabrikamPool,* které jsou potřebné k tomu, aby obsahovaly back-endové servery pomocí [vytvoření fondu adres az network application-gateway](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Přidání naslouchacích procesů

Naslouchací proces je potřeba k tomu, aby brána Application Gateway mohla správně směrovat provoz na back-endový fond. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu vytvoříte naslouchací procesy pro domény *www.contoso.com* a *www.fabrikam.com*. 

Přidejte posluchače s názvem *contosoListener* a *fabrikamListener,* které jsou potřebné ke směrování provozu pomocí [az sítě application-gateway http-listener create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Přidání pravidel směrování

Pravidla jsou zpracována v pořadí, ve kterém jsou vytvořeny, a provoz je směrován pomocí prvního pravidla, které odpovídá adrese URL odeslané do brány aplikace. Pokud máte například na stejném portu pravidlo založené na základním naslouchacím procesu a pravidlo založené na naslouchacím procesu pro více webů, musí být pravidlo s naslouchacím procesem pro více webů uvedeno před pravidlem se základním naslouchacím procesem, aby fungovalo podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstraníte výchozí pravidlo, které se vytvořilo při vytvoření aplikační brány. Pravidlo přidáte příkazem [az network application-gateway rule create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte tři škálovací sady virtuálních počítačů, které podporují tři back-endové fondy v aplikační bráně. Vytvořené škálovací sady se budou jmenovat *myvmss1*, *myvmss2* a *myvmss3*. Každá škálovací sada obsahuje dvě instance virtuálních počítačů, na které nainstalujete server NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalace serveru NGINX

```azurecli-interactive
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Vytvoření záznamu CNAME v doméně

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip). Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

Do adresního řádku prohlížeče zadejte název domény. Například http\://www.contoso.com.

![Testování webu Contoso v aplikační bráně](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Změňte adresu na jinou ze svých domén. Měli byste vidět něco podobného jako v následujícím příkladu:

![Testování webu Fabrikam v aplikační bráně](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření služby Application Gateway
> * Vytvoření naslouchacích procesů a pravidel směrování
> * Vytvořit z back-endových fondů škálovací sadu virtuálních počítačů
> * Vytvoření záznamu CNAME v doméně

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](application-gateway-introduction.md)
