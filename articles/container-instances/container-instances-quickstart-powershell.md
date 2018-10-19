---
title: Rychlý start – spuštění aplikace ve službě Azure Container Instances
description: V tomto rychlém startu nasadíte pomocí prostředí Azure PowerShell kontejner Dockeru ve službě Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 33444e810a2deebee11e535c73ce3e249f42b340
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854639"
---
# <a name="quickstart-run-an-application-in-azure-container-instances"></a>Rychlý start: Spuštění aplikace ve službě Azure Container Instances

Spouštějte kontejnery Dockeru v Azure rychle a snadno pomocí Azure Container Instances. Nemusíte nasazovat virtuální počítače ani používat úplnou platformu orchestrace kontejnerů jako Kubernetes. V tomto rychlém startu pomocí webu Azure Portal vytvoříte kontejner s Windows v Azure a zpřístupníte jeho aplikaci s použitím plně kvalifikovaného názvu domény. Několik sekund po provedení příkazu k jednomu nasazení můžete přejít k běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][qs-powershell-01]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.5 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

Nejdřív pomocí následujícího příkazu [New-AzureRmResourceGroup][New-AzureRmResourceGroup] vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus*:

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Teď máte skupinu prostředků a můžete spustit kontejner v Azure. K vytvoření instance kontejneru pomocí Azure PowerShellu zadejte do rutiny [New-AzureRmContainerGroup][New-AzureRmContainerGroup] název skupiny prostředků, název instance kontejneru a image kontejneru Dockeru. Kontejnery můžete zveřejnit na internetu tak, že zadáte jeden nebo více otevíraných portů, popisek názvu DNS nebo oboje. V tomto rychlém startu nasadíte kontejner s popiskem názvu DNS, který je hostitelem Internetové informační služby (IIS) běžící na serveru Nano.

Spuštěním následujícího příkazu spusťte instanci kontejneru. Hodnota `-DnsNameLabel` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Během několika sekund by se měla zobrazit odezva z Azure. Stav `ProvisioningState` kontejneru má nejdřív hodnotu **Vytváření**, ale během jedné nebo dvou minut by se měla zobrazit hodnota **Úspěšné**. Zkontrolujte stav nasazení pomocí rutiny [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Ve výstupu rutiny se zobrazí stav zřizování kontejneru a jeho plně kvalifikovaný název domény a IP adresa:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Jakmile má kontejner u stavu `ProvisioningState` hodnotu **Úspěšné**, přejděte v prohlížeči na jeho položku `Fqdn`. Pokud zobrazená webová stránka vypadá přibližně takto, blahopřejeme! Úspěšně jste nasadili aplikaci spuštěnou v kontejneru Dockeru do Azure.

![Služba IIS nasazená pomocí služby Azure Container Instances zobrazená v prohlížeči][qs-powershell-01]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až s kontejnerem skončíte, odeberte ho pomocí rutiny [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
