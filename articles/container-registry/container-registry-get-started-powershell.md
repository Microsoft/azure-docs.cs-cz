---
title: Rychlý start – vytvoření privátního registru Dockeru v Azure – PowerShell
description: Rychle se naučíte, jak vytvořit privátní registr kontejneru Dockeru v Azure pomocí PowerShellu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7dcdca594949a3b20000b31db681370ee4f9eac3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255274"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rychlý start: Vytvoření kontejneru soukromého registru pomocí Azure Powershellu

Azure Container Registry je spravovaná privátní služba registru kontejneru Dockeru, která se používá k vytváření, ukládání a obsluze imagí kontejnerů Dockeru. V tomto rychlém startu se naučíte vytvořit registr kontejneru Azure pomocí PowerShellu. Po vytvoření registru do něho odešlete image kontejneru a pak nasadíte kontejner z registru do Azure Container Instances (ACI).

## <a name="prerequisites"></a>Požadavky

Tento rychlý start vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Svou nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Je také nutné mít Docker nainstalovaný místně. Docker poskytuje balíčky pro systémy [macOS][docker-mac], [Windows][docker-windows] a [Linux][docker-linux].

Azure Cloud Shell neobsahuje všechny požadované součásti Dockeru (démon `dockerd`), a proto pro tento rychlý start nelze Cloud Shell použít.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzureRmAccount][Connect-AzureRmAccount] a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Po ověření v Azure vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Skupina prostředků je logický kontejner, ve kterém nasazujete a spravujete prostředky Azure.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Vytvoření registru kontejneru

Dále vytvořte v nové skupině prostředků registr kontejneru pomocí příkazu [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Následující příklad vytvoří registr s názvem myContainerRegistry007. Nahraďte v následujícím příkazu název *myContainerRegistry007* a pak spuštěním tohoto příkazu vytvořte registr:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Přihlášení k registru

Před odesíláním a vyžadováním imagí kontejnerů se musíte přihlásit k registru. Nejprve pomocí příkazu [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] získejte přihlašovací údaje správce pro registr:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Potom k přihlášení spusťte [docker login][docker-login]:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Po úspěšném přihlášení se vrátí zpráva `Login Succeeded`:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Odeslání image do registru

Teď, když jste přihlášení k registru, do něho můžete odesílat image kontejnerů. Pokud chcete získat image, kterou budete moct odeslat do registru, vyžádejte si veřejnou image [aci-helloworld][aci-helloworld-image] z Docker Hubu. Image [aci-helloworld][aci-helloworld-github] je malá aplikace Node.js, která poskytuje statickou stránku HTML, která zobrazuje logo Azure Container Instances.

```powershell
docker pull microsoft/aci-helloworld
```

Když se image vyžádá, bude výstup vypadat přibližně takto:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Před odesláním image do registru kontejneru Azure ji musíte označit plně kvalifikovaným názvem domény vašeho registru. Plně kvalifikované názvy domén registrů kontejnerů Azure jsou ve formátu *\<název_registru\>.azurecr.io*.

Zadejte do proměnné značku úplné image. Určete přihlašovací server, název úložiště (aci-helloworld) a verzi image (v1):

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

K označení image teď použijte [docker tag][docker-tag]:

```powershell
docker tag microsoft/aci-helloworld $image
```

A nakonec k odeslání image do registru použijte [docker push][docker-push]:

```powershell
docker push $image
```

Když klient Dockeru odešle image, měl by výstup vypadat přibližně takto:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Blahopřejeme! Právě jste do registru odeslali první image kontejneru.

## <a name="deploy-image-to-aci"></a>Nasazení image do služby ACI

Teď, když je image v registru, nasaďte kontejner přímo do Azure Container Instances, abyste viděli, jak běží v Azure.

Nejprve převeďte přihlašovací údaje registru na *PSCredential*. Příkaz `New-AzureRmContainerGroup`, který použijete k vytvoření instance kontejneru, je vyžaduje v tomto formátu.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Kromě toho popisek názvu DNS pro váš kontejner musí být jedinečný v rámci oblasti Azure, ve které se vytváří. Spuštěním následujícího příkazu zadejte do proměnné vygenerovaný název:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Nakonec spuštěním příkazu [New-AzureRmContainerGroup][New-AzureRmContainerGroup] nasaďte kontejner z image ve vašem registru s jedním jádrem procesoru a pamětí o velikosti 1 GB:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Z Azure byste měli získat první odezvu s podrobnostmi o kontejneru, jeho stav by měl napřed být Čeká.

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Pokud chcete sledovat jeho stav a určit, kdy běží, spusťte několikrát příkaz [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Spuštění kontejneru by mělo trvat méně než minutu.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Tady můžete vidět, že zřizování kontejneru má nejprve stav *Vytváření*. Po spuštění se stav změní na *Proběhlo úspěšně*:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>Zobrazení spuštěné aplikace

Po úspěšném nasazení do služby ACI a spuštění vašeho kontejneru přejděte v prohlížeči na jeho plně kvalifikovaný název domény, abyste viděli aplikaci běžící v Azure.

Plně kvalifikovaný název domény pro váš kontejner získáte pomocí příkazu [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

Výstupem příkazu je plně kvalifikovaný název domény vaší instance kontejneru:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

Když získáte plně kvalifikovaný název domény, přejděte na něj v prohlížeči:

![Aplikace Hello World v prohlížeči][qs-psh-01-running-app]

Blahopřejeme! Máte kontejner s běžící aplikací v Azure, která je nasazená přímo z image kontejneru ve vašem privátním registru kontejneru Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až skončíte práci s prostředky, které jste vytvořili v tomto rychlém startu, odeberte skupinu prostředků, registr kontejneru a instanci kontejneru pomocí příkazu [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup]:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí Azure CLI vytvořili službu Azure Container Registry a ve službě Azure Container Instances jste spustili její instanci. Pokračujte ke kurzu služby Azure Container Instances, kde najdete podrobnější přehled ACI.

> [!div class="nextstepaction"]
> [Kurz služby Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
