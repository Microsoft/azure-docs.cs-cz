---
title: Rychlý Start – nasazení kontejneru Docker do instance kontejneru – PowerShell
description: V tomto rychlém startu použijete Azure PowerShell k rychlému nasazení kontejnerové webové aplikace, která běží v izolované instanci kontejneru Azure.
services: container-instances
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c7002d8a83e58a9089ee3c3840b0397d63e2f198
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89565578"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Rychlý Start: nasazení instance kontejneru v Azure pomocí Azure PowerShell

Použijte Azure Container Instances ke spouštění kontejnerů Docker bez serveru v Azure s využitím jednoduchosti a rychlosti. Pokud nepotřebujete úplnou platformu orchestrace kontejnerů, jako je třeba služba Azure Kubernetes, nasaďte aplikaci na vyžádání do instance kontejneru na vyžádání.

V tomto rychlém startu použijete Azure PowerShell k nasazení izolovaného kontejneru Windows a jeho aplikaci zpřístupníte pomocí plně kvalifikovaného názvu domény (FQDN). Několik sekund po provedení jednoho příkazu pro nasazení můžete procházet do aplikace spuštěné v kontejneru:

![Aplikace nasazená do služby Azure Container Instances zobrazená v prohlížeči][qs-powershell-01]

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Instance kontejnerů Azure, stejně jako všechny prostředky Azure, se musí nasadit do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

Nejprve vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* pomocí následujícího příkazu [New-AzResourceGroup][New-AzResourceGroup] :

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Teď máte skupinu prostředků a můžete spustit kontejner v Azure. Pokud chcete vytvořit instanci kontejneru s Azure PowerShell, zadejte název skupiny prostředků, název instance kontejneru a image kontejneru Docker do rutiny [New-AzContainerGroup][New-AzContainerGroup] . V tomto rychlém startu použijete veřejnou `mcr.microsoft.com/windows/servercore/iis:nanoserver` image. Balíčky imagí, které Microsoft Internetová informační služba (IIS) spustí na nano serveru.

Kontejnery můžete zveřejnit na internetu tak, že zadáte jeden nebo více otevíraných portů, popisek názvu DNS nebo oboje. V tomto rychlém startu nasadíte kontejner s označením názvu DNS, aby byla služba IIS veřejně dosažitelná.

Spusťte příkaz podobný následujícímu jako při spuštění instance kontejneru. Nastavte `-DnsNameLabel` jedinečnou hodnotu v rámci oblasti Azure, ve které jste instanci vytvořili. Pokud se zobrazí chybová zpráva „Popisek názvu DNS není dostupný“, zkuste jiný popisek názvu DNS.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Během několika sekund by se měla zobrazit odezva z Azure. Stav `ProvisioningState` kontejneru má nejdřív hodnotu **Vytváření**, ale během jedné nebo dvou minut by se měla zobrazit hodnota **Úspěšné**. Pomocí rutiny [Get-AzContainerGroup][Get-AzContainerGroup] ověřte stav nasazení:

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

Až budete s kontejnerem hotovi, odeberte ho pomocí rutiny [Remove-AzContainerGroup][Remove-AzContainerGroup] :

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili instanci kontejneru Azure z image ve veřejném registru Docker Hub. Pokud si chcete sestavit image kontejneru a nasadit ji z privátního registru kontejnerů Azure, pokračujte ke kurzu služby Azure Container Instances.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
