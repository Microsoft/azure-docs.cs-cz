---
title: Rychlý start – spuštění aplikace ve službě Azure Container Instances – PowerShell
description: V tomto rychlém startu použijete Azure PowerShell k nasazení aplikace typu kontejner Dockeru do služby Azure Container Instances pomocí Azure Powershellu
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8cb84523288f45dfb719d69e4f7d227039598a9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806908"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>Rychlý start: Spuštění aplikace typu kontejner ve službě Azure Container Instances pomocí Azure Powershellu

Spouštějte kontejnery Dockeru v Azure rychle a snadno pomocí Azure Container Instances. Nemusíte nasazovat virtuální počítače ani používat úplnou platformu orchestrace kontejnerů jako Kubernetes. V tomto rychlém startu pomocí webu Azure Portal vytvoříte kontejner s Windows v Azure a zpřístupníte jeho aplikaci s použitím plně kvalifikovaného názvu domény. Několik sekund po provedení příkazu k jednomu nasazení můžete přejít k běžící aplikaci:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][qs-powershell-01]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento kurz vyžaduje modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

Nejprve vytvořte skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění následujícím [New-AzResourceGroup] [ New-AzResourceGroup] příkaz:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Teď máte skupinu prostředků a můžete spustit kontejner v Azure. Pokud chcete vytvořit instanci kontejneru s využitím Azure Powershellu, zadejte název skupiny prostředků, název instance kontejneru a image kontejneru Dockeru pro [AzContainerGroup nový] [ New-AzContainerGroup] rutiny. V tomto rychlém startu použijete `microsoft/iis:nanoserver` image Windows z veřejného registru Docker Hub. Tento obrázek balíčky Internetové informační služby (IIS) ke spuštění na Nano serveru.

Kontejnery můžete zveřejnit na internetu tak, že zadáte jeden nebo více otevíraných portů, popisek názvu DNS nebo oboje. V tomto rychlém startu nasadíte kontejner s použitím popisku názvu DNS tak, aby služba IIS je veřejně dostupný.

Spuštěním následujícího příkazu spusťte instanci kontejneru. Hodnota `-DnsNameLabel` musí být jedinečná v rámci oblasti Azure, ve které vytváříte instanci. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Během několika sekund by se měla zobrazit odezva z Azure. Stav `ProvisioningState` kontejneru má nejdřív hodnotu **Vytváření**, ale během jedné nebo dvou minut by se měla zobrazit hodnota **Úspěšné**. Zkontrolujte stav nasazení se [Get-AzContainerGroup] [ Get-AzContainerGroup] rutiny:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Ve výstupu rutiny se zobrazí stav zřizování kontejneru a jeho plně kvalifikovaný název domény a IP adresa:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


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

Jakmile budete hotovi s kontejnerem, odeberte ji [odebrat AzContainerGroup] [ Remove-AzContainerGroup] rutiny:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
