---
title: Škálovací sady automatické upgrady operačního systému pomocí virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak automaticky upgradovat operační systém na instance virtuálních počítačů ve škálovací sadě
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 935b3ff0fe03984b02dc2e1137f48e53b06ce0c2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995104"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatické upgrady operačního systému sady škálování virtuálních počítačů Azure

Automatický upgrade operačního systému image je funkce ve verzi preview pro Azure virtual machine scale sets, která automaticky upgraduje všechny virtuální počítače na nejnovější image operačního systému.

Automatický upgrade operačního systému má následující vlastnosti:

- Po nakonfigurování se automaticky použije nejnovější image operačního systému, publikováno vydavatelů imagí do škálovací sady, bez zásahu uživatele.
- Upgraduje dávky instancí postupné způsobem pokaždé, když se publikuje nový image platformy vydavatele.
- Se integruje s sondu stavu aplikace (volitelný, ale důrazně doporučujeme pro zabezpečení).
- Funguje pro všechny velikosti virtuálních počítačů.
- Image platformy funguje pro Windows a Linux.
- Automatické upgrady můžete zrušit kdykoli (upgrady operačního systému může být spuštěna ručně i).
- Disk s operačním systémem virtuálního počítače se nahradí nový Disk s operačním systémem vytvořené pomocí nejnovější verze image. Nakonfigurované rozšíření a vlastních dat skripty se spouštějí, při trvalých dat, které disky jsou zachovány.


## <a name="preview-notes"></a>Poznámky k verzi Preview 
Ve verzi preview, platí následující omezení a omezení:

- Automatické OS upgradů podporují jenom [čtyři skladové jednotky operačního systému](#supported-os-images). Neexistuje žádná smlouva SLA nebo záruky. Doporučujeme, abyste že nepoužíváte automatické upgrady na kritické úlohy v produkčním prostředí ve verzi preview.
- Azure disk encryption je **není** aktuálně podporovány s virtuálního počítače škálovací sady automatický upgrade operačního systému.


## <a name="register-to-use-automatic-os-upgrade"></a>Zaregistrujte se a použijte automatický Upgrade operačního systému
Pokud chcete použít funkci automatického upgradu operačního systému, zaregistrujte poskytovatele ve verzi preview pomocí Azure Powershellu nebo příkazového řádku Azure CLI 2.0.

### <a name="powershell"></a>PowerShell

1. Zaregistrovat s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. Ohlásit příspěvek jako trvá přibližně 10 minut pro stav registrace *registrované*. Můžete zkontrolovat aktuální stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). 

3. Po registraci, ujistěte se, že *Microsoft.Compute* poskytovatel je zaregistrovaný. Následující příklad používá prostředí Azure Powershell s [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. Zaregistrovat s [az funkce register](/cli/azure/feature#az-feature-register):

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. Ohlásit příspěvek jako trvá přibližně 10 minut pro stav registrace *registrované*. Můžete zkontrolovat aktuální stav registrace s [az funkce Zobrazit](/cli/azure/feature#az-feature-show). 
 
3. Po registraci, ujistěte se, že *Microsoft.Compute* poskytovatel je zaregistrovaný. Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) s [az provider register](/cli/azure/provider#az-provider-register):

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Clustery Service Fabric mají své vlastní pojem stavu aplikace, ale škálovací sady bez Service Fabric použijte sondu stavu nástroje pro vyrovnávání zatížení pro sledování stavu aplikací. 
>
> ### <a name="azure-powershell"></a>Azure Powershell
>
> 1. Registrovat funkci zprostředkovatele pro sondy stavu s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. Znovu, trvá přibližně 10 minut pro stav registrace Ohlásit příspěvek jako *registrované*. Můžete zkontrolovat aktuální stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature)
>
> 3. Jednou zaregistrovaný. Ujistěte se, že *Microsoft.Network* zprostředkovatel je registrován pomocí [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. Registrovat funkci zprostředkovatele pro sondy stavu s [az funkce register](/cli/azure/feature#az-feature-register):
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. Znovu, trvá přibližně 10 minut pro stav registrace Ohlásit příspěvek jako *registrované*. Můžete zkontrolovat aktuální stav registrace s [az funkce Zobrazit](/cli/azure/feature#az-feature-show). 
>
> 3. Jednou zaregistrovaný. Ujistěte se, že *Microsoft.Network* zprostředkovatel je registrován pomocí [az provider register](/cli/azure/provider#az-provider-register) následujícím způsobem:
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>Prostředí portálu
Jakmile budete postupovat podle výše uvedených kroků registrace, můžete přejít na [na webu Azure portal](https://aka.ms/managed-compute) , povolte automatické upgrady operačního systému ve škálovacích sadách a sledovat průběh upgradu:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Podporované Image operačního systému
Aktuálně jsou podporovány pouze určité Image platformy operačního systému. Nelze použít aktuálně vlastních imagí, které jste sami vytvořili. *Verze* vlastnost image platformy musí být nastavena na *nejnovější*.

Aktuálně jsou podporovány následující skladové jednotky (přibudou další):
    
| Vydavatel               | Nabídka         |  Skladová jednotka (SKU)               | Verze  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2016 Datacenter s kontejnery | nejnovější   |



## <a name="application-health-without-service-fabric"></a>Stav aplikace bez Service Fabric
> [!NOTE]
> Tato část platí jenom pro škálovací sady bez Service Fabric. Service Fabric má svůj vlastní pojem stavu aplikace. Pokud používáte automatické upgrady operačního systému s využitím Service Fabric, image nového operačního systému nasazení aktualizační doména podle aktualizačních domén udržet vysokou dostupnost služby spuštěné v Service Fabric. Další informace o odolnosti charakteristiky clustery Service Fabric najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Během upgradu operačního systému se upgradovat instance virtuálních počítačů ve škálovací sadě jednu dávku najednou. Upgrade by měly pokračovat pouze, pokud zákazník aplikace je v pořádku na upgradovaných instancí virtuálních počítačů. Z tohoto důvodu, aplikace musí poskytovat signálů stavu pro modul upgradu operačního systému škálovací sady. Během upgradu operačního systému platformy bere v úvahu stav napájení virtuálního počítače a k určení, zda instance virtuálního počítače je v pořádku po upgradu se stav zřizování rozšíření. Během upgradu operačního systému na instanci virtuálního počítače disk s operačním systémem na instancích virtuálních počítačů se nahradí nový disk založené na nejnovější verzi image. Po dokončení upgradu operačního systému, nakonfigurované rozšíření jsou spuštěny na tyto virtuální počítače. Pouze v případě, že všechna rozšíření na virtuálním počítači se úspěšně zřízený, aplikace považuje za v pořádku. 

Kromě toho škálovací sady *musí* mít nakonfigurovanou sondy stavu aplikace poskytují platformu, se správnými informacemi o probíhající stavu aplikace. Sondy stavu aplikace jsou vlastní zatížení vyrovnávání testy, které se používají jako signál o stavu. Aplikace běžící na instanci škálovací sady virtuálních počítačů může reagovat na externí žádosti protokolu HTTP nebo TCP určující, zda je v pořádku. Další informace o fungování vlastní načíst sondy nástroje pro vyrovnávání najdete v tématu [porozumění testům nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md).

Pokud škálovací sada je konfigurován pro použití více skupin umístění, sondy pomocí [Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) muset používat.

### <a name="important-keep-credentials-up-to-date"></a>Důležité: Keep pověření aktuální
Pokud vaše škálovací sada používá žádné přihlašovací údaje pro přístup k externím prostředkům, musíte zajistit, aby že přihlašovací údaje jsou tak pořád aktuální. Například lze nakonfigurovat rozšíření virtuálního počítače do účtu úložiště pomocí tokenu SAS. Pokud žádné přihlašovací údaje, včetně certifikátů a tokeny mají platnost, se upgrade nezdaří a první dávku virtuálních počítačů zůstanou ve stavu selhání.

Doporučené kroky pro obnovení virtuálních počítačů a znovu povolte automatický upgrade operačního systému, pokud dojde k selhání ověřování prostředků jsou:

* Znovu vygenerovat token (nebo jiné přihlašovací údaje) předaná do vašeho rozšíření.
* Ujistěte se, že žádné přihlašovací údaje používané z uvnitř virtuálního počítače ke komunikaci s externí entity je aktuální.
* Aktualizace rozšíření v modelu škálovací sady s všechny nové tokeny.
* Nasazení aktualizované škálovací sady, která aktualizuje všechny instance virtuálních počítačů, včetně neúspěšných. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurují se vlastní zatížení sondy jako sondu stavu aplikaci na škálovací sadu
Můžete *musí* explicitně vytvořte sondu nástroje pro vyrovnávání zatížení pro škálovací sadu stavu. Můžete použít stejný koncový bod pro existující sondu protokolu HTTP nebo TCP testu, ale sondu stavu může vyžadovat různé chování z testu tradiční – nástroj pro vyrovnávání zatížení. Například test nástroje pro vyrovnávání zatížení můžou se zobrazovat v pořádku, pokud v instanci je příliš vysoké. Naopak, která nemusí být vhodný pro zjištění stavu instance během automatický upgrade operačního systému. Konfigurace testu a mají zjišťování vysoký méně než dvě minuty.

Test paměti nástroje pro vyrovnávání zatížení můžete odkazovat *položky networkProfile* stupnice nastavit a může být přidružené buď interní nebo veřejný internetový – nástroj pro vyrovnávání zatížení následujícím způsobem:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Vynucovat zásady upgradu bitové kopie operačního systému v rámci vašeho předplatného
Pro bezpečné upgrady důrazně doporučujeme k vynucování zásad upgradu. Tyto zásady můžete vyžadovat, sondy stavu aplikace v rámci vašeho předplatného. Tyto zásady Azure Resource Manageru odmítne nasazení, která nemají upgradovat konfiguraci nastavení automatizovaných bitovou kopii operačního systému:

### <a name="powershell"></a>PowerShell
1. Získat integrované definice zásad Azure Resource Manageru s [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) následujícím způsobem:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Přiřazení zásad na předplatné s [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) následujícím způsobem:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>CLI 2.0
Přiřazení zásad na předplatné s předdefinovaných zásad Azure Resource Manageru:

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>Konfigurace automatických aktualizací
Konfigurace automatických upgradů, ujistěte se, že *automaticOSUpgrade* je nastavena na *true* v škálovací sady definice modelu. Tuto vlastnost můžete nakonfigurovat pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

### <a name="powershell"></a>PowerShell
Následující příklad používá prostředí Azure PowerShell (4.4.1 nebo novější) ke konfiguraci automatické upgrady pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) ke konfiguraci automatické upgrady pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Kontrola stavu automatický upgrade operačního systému
Můžete zkontrolovat stav poslední upgrade operačního systému provést na škálovací sadě pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API.

### <a name="powershell"></a>PowerShell
Následující příklad používá prostředí Azure PowerShell (4.4.1 nebo novější) a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli"></a>Azure CLI

Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Následující příklad používá rozhraní REST API a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

Vrátí volání GET vlastnosti podobně jako následující příklad výstupu:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Spuštění automatický Upgrade operačního systému
K rozbalení na používání sond stavu aplikace, upgrady operačního systému škálovací sady provést následující kroky:

1. Pokud více než 20 % instancí není v pořádku, zastavte upgrade; v opačném případě pokračujte.
2. Určete další dávku instancí virtuálních počítačů, které chcete provést upgrade, službou batch s 20 % maximální počet celkový počet instancí.
3. Upgradujte operační systém další dávku instancí virtuálních počítačů.
4. Pokud více než 20 % upgradovaná instance není v pořádku, zastavte upgrade; v opačném případě pokračujte.
5. Pro škálovací sady, které nejsou součástí clusteru Service Fabric upgrade čeká, až 5 minut, než se testy se obnoví dobrý stav, bude okamžitě pokračovat na další dávku. Pro škálovací sady, které jsou součástí clusteru Service Fabric škálovací sada počká 30 minut, než budete pokračovat k další dávku.
6. Pokud nezbývají instance, které chcete upgradovat, goto krok 1) pro další dávku; v opačném případě nebude dokončen upgrade.

Škálovací sada operační systém upgradovat modul kontroluje celkový stav instance virtuálních počítačů před upgradem každou dávku. Při upgradu dávky, může být jiné souběžné plánovaná nebo neplánovaná Údržba děje v datových centrech Azure, které můžou ovlivnit dostupnost vašich virtuálních počítačů. Proto je možné, že dočasně více než 20 % instancí může být mimo provoz. V takovém případě škálovací sady na konci aktuální dávky upgradu zastaví.


## <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Následující šablony můžete použít k nasazení škálovací sady, který používá automatické upgrady <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatické postupných upgradů – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Další postup
Další příklady o tom, jak používat automatické upgrady operačního systému se škálovacími sadami najdete v článku [úložiště GitHub pro funkce ve verzi preview](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
