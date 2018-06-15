---
title: Automatické upgrady operačního systému se virtuální počítač Azure škálování nastaví | Microsoft Docs
description: Zjistěte, jak automaticky upgradovat operační systém v instancích virtuálního počítače ve škálovací sadě
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 28a9b3d68037aac0c1198da4232c045487b01174
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30838219"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatické upgrady operačního systému sadu škálování virtuálního počítače Azure

Automatický upgrade bitové kopie operačního systému je ukázková funkce pro sady škálování virtuálního počítače Azure, která se automaticky upgraduje všechny virtuální počítače na nejnovější bitové kopie operačního systému.

Automatický upgrade operačního systému má následující vlastnosti:

- Po nakonfigurování nejnovější bitové kopie operačního systému vydaná vydavateli bitové kopie automaticky použita pro škálování nastavit bez zásahu uživatele.
- Upgraduje dávek instancí postupného způsobem pokaždé, když nový image platformy je publikována vydavatelem.
- Se integruje s test stavu aplikace (volitelný, ale důrazně doporučujeme pro zabezpečení).
- Platí pro všechny velikosti virtuálních počítačů.
- Funguje pro systém Windows a Linux Image platformy.
- Automatické upgrady můžete zamítnutí kdykoli (upgrady operačního systému lze zahájit také ručně).
- Disk operačního systému virtuálního počítače se nahradí nový Disk operačního systému, které jsou vytvořené pomocí nejnovější verze bitové kopie. Nakonfigurovaných rozšíření a vlastních dat skripty se spouštějí, při trvalých dat, které disky jsou uchovány.


## <a name="preview-notes"></a>Poznámky k verzi Preview 
Zatímco ve verzi preview, platí následující omezení a omezení:

- Automatické operační systém upgraduje podporuje se jen [čtyři SKU OS](#supported-os-images). Neexistuje žádné SLA nebo záruky. Doporučujeme, abyste že nepoužijete automatické upgrady na kritické úlohy produkční verzi Preview.
- Azure disk encryption (momentálně ve verzi preview) je **není** aktuálně podporované škálování virtuálního počítače sady Automatické OS upgradu.


## <a name="register-to-use-automatic-os-upgrade"></a>Zaregistrujte se a použít automatický Upgrade operačního systému
Pokud chcete používat funkci automatického upgradu operačního systému, zaregistrujte zprostředkovatele preview s [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) následujícím způsobem:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Jak dlouho trvá přibližně 10 minut na stav registrace do sestavy jako *registrovaná*. Můžete zkontrolovat aktuální stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Po registraci, ujistěte se, že *Microsoft.Compute* poskytovatel je zaregistrovaný u [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) následujícím způsobem:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Clusterů Service Fabric má své vlastní představu o stavu aplikací, ale sady škálování bez Service Fabric pomocí test stavu nástroje pro vyrovnávání zatížení pro sledování stavu aplikací. Chcete-li zaregistrovat funkci zprostředkovatele pro sondy stavu, použijte [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) následujícím způsobem:
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> Znovu, trvá přibližně 10 minut na stav registrace sestavy jako *registrovaná*. Můžete zkontrolovat aktuální stav registrace s [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Jednou zaregistrován. Ujistěte se, že *Microsoft.Network* poskytovatel je zaregistrovaný u [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) následujícím způsobem:
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>Prostředí portálu
Jakmile budete postupovat podle výše uvedených kroků registrace, můžete přejít na [portálu Azure](https://aka.ms/managed-compute) chcete povolit automatické upgrady operačního systému na škálovací sady a sledovat průběh upgradu:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Podporované bitové kopie operačního systému
Aktuálně jsou podporované jenom určité Image operačního systému platformy. Nelze použít aktuálně vlastními obrázky, abyste měli jste sami vytvořili. *Verze* vlastnost image platformy musí být nastavena na *nejnovější*.

Aktuálně jsou podporovány následující SKU (více se přidají):
    
| Vydavatel               | Nabídka         |  Skladová jednotka (SKU)               | Verze  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | nejnovější   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | nejnovější   |



## <a name="application-health-without-service-fabric"></a>Stav aplikace bez Service Fabric
> [!NOTE]
> Tato část se vztahuje pouze na sady škálování bez Service Fabric. Service Fabric má svou vlastní představu o stavu aplikace. Při použití automatické upgrady operačního systému pomocí Service Fabric, je doména aktualizace podle domény aktualizace kvůli udržení vysoké dostupnosti se služby spuštěné v Service Fabric nasazuje novou bitovou kopii operačního systému. Další informace o vlastnostech odolnost clusterů Service Fabric, najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Během upgradu operačního systému, instance virtuálního počítače ve škálovací sadě upgradují jeden batch najednou. Upgrade by měly pokračovat pouze, pokud je v pořádku na upgradovaná instance virtuálních počítačů zákazníků aplikace. Z tohoto důvodu je nutné, aby aplikace poskytuje stavu signály, které modul upgradu operačního systému sadu škálování. Během upgradu operačního systému platformu zvažuje rozšíření zřizování stavu, pokud instance virtuálního počítače je v pořádku po upgradu a stav napájení virtuálního počítače. Během upgradu operačního systému v instanci virtuálního počítače disk operačního systému v instanci virtuálního počítače se nahradí nový disk založené na nejnovější verzi bitové kopie. Po dokončení upgradu operačního systému, nakonfigurovaných rozšíření se spouštějí na těchto virtuálních počítačích. Jenom v případě, že všechna rozšíření na virtuálním počítači se úspěšně zřízený, aplikace považuje za v pořádku. 

Kromě toho sada škálování *musí* nakonfigurováni testy stavu aplikace platformy poskytnout přesné informace o probíhající stavu aplikace. Testy stavu aplikace jsou vlastní zatížení vyrovnávání sond používané jako signál stavu. Aplikace běžící na instanci škálovací sadu virtuálních počítačů může reagovat na externí HTTP nebo TCP požadavky určující, zda je v pořádku. Další informace o fungování vlastní načíst vyrovnávání sondy najdete v tématu [sondy nástroje pro vyrovnávání zatížení Rady pro pochopení](../load-balancer/load-balancer-custom-probe-overview.md).

Pokud byly sadou škálování je nakonfigurovaný na použití více skupin umístění, sondy pomocí [nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) zapotřebí.

### <a name="important-keep-credentials-up-to-date"></a>Důležité: Aktuálnost přihlašovacích údajů
Pokud škálovací sadu používá všechny přihlašovací údaje pro přístup k externím prostředkům, například pokud rozšíření virtuálního počítače je nakonfigurován používaný SAS token pro účet úložiště, musíte Ujistěte se, že přihlašovací údaje jsou pořád aktuální. Pokud žádné přihlašovací údaje, včetně certifikátů a tokeny mají platnost vypršela, se upgrade nezdaří a první dávky virtuálních počítačů bude ponechána ve stavu selhání.

Doporučený postup obnovení virtuálních počítačů a znovu povolte automatický upgrade operačního systému, pokud dojde k selhání ověřování prostředků jsou:

* Znovu vygenerovat token (nebo jiné přihlašovací údaje) předané do vaší přípony.
* Zajistěte, aby žádné pověření použít z ve virtuálním počítači, aby komunikoval s externí entity aktuální.
* Aktualizujte všechny nové tokeny přípony v modelu sady škálování.
* Nasazení aktualizované škálovací sadu, která aktualizuje všechny instance virtuálních počítačů, včetně těch se nezdařilo. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurace služby Řízení Probe vyrovnávání vlastní zatížení zpráv jako sběru dat stavu aplikace na škále nastavit
Můžete *musí* explicitně vytvořit sondu nástroje pro vyrovnávání zatížení pro sadu škálování stavu. Může použít stejný koncový bod pro existující sondu HTTP nebo TCP testu, ale test stavu může vyžadovat různé chování z sondu tradiční Vyrovnávání zatížení. Například může vrátit sondu nástroje pro vyrovnávání zatížení obvykle není v pořádku, pokud zatížení instance je příliš vysoká, zatímco, nemusí být vhodný pro určování stavu instance během automatického upgradu operačního systému. Konfigurace testu do mají vysokou míru testování méně než dvě minuty.

Test Vyrovnávání zatížení, které může být odkazováno v *networkProfile* měřítka nastavit a může být přidružené buď k interní nebo veřejné přístupných Vyrovnávání zatížení, následujícím způsobem:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Vynucování zásad upgradu bitové kopie operačního systému vašeho předplatného
Pro bezpečné upgrady důrazně doporučujeme k vynucení aktualizace zásad. Tato zásada může vyžadovat sondy stavu aplikací v rámci vašeho předplatného. Tyto zásady Azure Resource Manager odmítne nasazení, které nemají automatizované image OS, upgrade nastavení:

1. Získat definici předdefinovaných zásad Azure Resource Manager s [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) následujícím způsobem:

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


## <a name="configure-auto-updates"></a>Konfigurovat Automatické aktualizace
Chcete-li konfigurovat automatický upgrade, ověřte, zda *automaticOSUpgrade* je nastavena na *true* v měřítka nastavit definice modelu. Tuto vlastnost lze nakonfigurovat Azure PowerShell nebo Azure CLI 2.0.

Následující příklad používá prostředí Azure PowerShell (4.4.1 nebo novější) nakonfigurovat automatické upgrady pro škálování nastavení s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) nakonfigurovat automatické upgrady pro škálování nastavení s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Zkontrolujte stav automatického upgradu operačního systému
Můžete zkontrolovat stav poslední upgrade operačního systému provést na vaše škálování nastavit pomocí prostředí Azure PowerShell, Azure CLI 2.0 nebo rozhraní REST API.

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell (4.4.1 nebo novější) a zkontrolujte stav stupnice nastavení s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) a zkontrolujte stav stupnice nastavení s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Následující příklad používá rozhraní API REST a zkontrolujte stav stupnice nastavení s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

Vrátí volání GET vlastnosti podobné výstupu v následujícím příkladu:

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


## <a name="automatic-os-upgrade-execution"></a>Provádění automatický Upgrade operačního systému
Rozšířit na sondy stavu aplikace, upgrady operačního systému sadu škálování provést následující kroky:

1. Pokud se více než 20 % instancí jsou není v pořádku, zastavte upgradu; v opačném případě pokračujte.
2. Určete další dávku instance virtuálních počítačů, které chcete upgradovat, s dávce s maximální 20 % počet celkový počet instancí.
3. Upgrade operačního systému další dávku instance virtuálních počítačů.
4. Pokud se více než 20 % upgradovaná instance jsou není v pořádku, zastavte upgradu; v opačném případě pokračujte.
5. Pro sady škálování, které nejsou součástí clusteru Service Fabric upgrade čeká, až 5 minut sondy se v pořádku, bude okamžitě pokračovat na další dávku. Měřítko pro sady škálování, které jsou součástí clusteru Service Fabric, nastavte počká 30 minut, než přejdete na další dávku.
6. Pokud zbývají instance, které chcete upgradovat, goto krok 1) pro další dávku; v opačném případě bude dokončen upgrade.

Měřítko nastavit modul Upgrade operačního systému kontroluje celkový stav instance virtuálních počítačů před upgradem každou dávku. Při upgradu dávce, mohou existovat další souběžných plánovaná nebo neplánovaná Údržba děje v datových centrech Azure, který může mít vliv na dostupnost virtuálních počítačů. Proto je možné, že dočasně víc než 20 % instancí může být mimo provoz. V takových případech na konci aktuální dávku nastavit měřítka upgradu zastaví.


## <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Následující šablonu můžete použít k nasazení sadu škálování, který používá automatické upgrady <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatické vrácení upgraduje - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Další postup
Další příklady týkající se používání automatické upgrady operačního systému s sady škálování najdete v tématu [úložiště GitHub pro funkce verze preview](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
