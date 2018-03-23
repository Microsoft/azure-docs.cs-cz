---
title: Rychlý start – Vytvoření privátního registru Dockeru v Azure pomocí PowerShellu
description: Rychle se naučíte, jak vytvořit privátní registr Dockeru pomocí PowerShellu.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2bae45955cf3c2b157acce2544b1f35fbddd0170
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Vytvoření služby Azure Container Registry pomocí PowerShellu

Azure Container Registry je spravovaná služba registru kontejnerů Dockeru sloužící k ukládání privátních imagí kontejnerů Dockeru. Tato příručka podrobně popisuje vytvoření instance služby Azure Container Registry pomocí PowerShellu, nasdílení image kontejneru do registru a nakonec nasazení kontejneru z registru do služby Azure Container Instances (ACI).

Tento rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Je také nutné mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru na jakémkoli [Macu][docker-mac] nebo systému [Windows][docker-windows] či [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Login-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vytvořte instanci ACR pomocí příkazu [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá *myContainerRegistry007*. Aktualizujte název na jedinečnou hodnotu.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Přihlášení ke službě ACR

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k instanci služby ACR. Nejprve pomocí příkazu [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) získejte přihlašovací údaje správce pro instanci ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Potom se pomocí příkazu [docker login][docker-login] přihlaste k instanci ACR.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Příkaz po dokončení vrátí zprávu `Login Succeeded` (Přihlášení bylo úspěšné). Může se zobrazit také upozornění zabezpečení doporučující použití parametru `--password-stdin`. I když je jeho použití nad rámec tohoto článku, doporučujeme řídit se osvědčeným postupem. Další informace najdete v referenčních informacích k příkazu [docker login][docker-login].

## <a name="push-image-to-acr"></a>Nasdílení image do služby ACR

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. V případě potřeby si přetáhněte předem vytvořenou image z Docker Hubu spuštěním následujícího příkazu.

```powershell
docker pull microsoft/aci-helloworld
```

Image musí být označená pomocí názvu přihlašovacího serveru ACR. Použijte k tomu příkaz [docker tag][docker-tag]. 

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Nakonec pomocí příkazu [docker push][docker-push] nasdílejte image do služby ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Nasazení image do služby ACI
Pokud chcete nasadit image jako instanci kontejneru ve službě Azure Container Instances (ACI), nejprve převeďte přihlašovací údaje registru na objekt PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Pokud chcete z registru kontejneru nasadit svou image kontejneru s 1 jádrem procesoru a 1 GB paměti, spusťte následující příkaz:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Z Azure Resource Manageru byste měli získat první odezvu s podrobnostmi o kontejneru. Pokud chcete monitorovat stav kontejneru a zkontrolovat, kdy je spuštěný, zopakujte příkaz [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Mělo by to trvat necelou minutu.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Příklad výstupu: `Succeeded`

## <a name="view-the-application"></a>Zobrazení aplikace
Po úspěšném nasazení do služby ACI načtěte veřejnou IP adresu kontejneru pomocí příkazu [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Příklad výstupu: `"13.72.74.222"`

Pokud chcete zobrazit spuštěnou aplikaci, v oblíbeném prohlížeči přejděte na tuto veřejnou IP adresu. Výsledek by měl vypadat přibližně takto:

![Aplikace Hello World v prohlížeči][qs-portal-15]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, služby Azure Container Registry a všech instancí kontejnerů Azure použít příkaz [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup].

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí Azure CLI vytvořili službu Azure Container Registry a ve službě Azure Container Instances jste spustili její instanci. Pokračujte ke kurzu služby Azure Container Instances, kde najdete podrobnější přehled ACI.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
