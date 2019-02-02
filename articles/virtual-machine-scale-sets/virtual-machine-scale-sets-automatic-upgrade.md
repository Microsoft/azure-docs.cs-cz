---
title: Škálovací sady automatické upgrady bitové kopie operačního systému pomocí virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak automaticky aktualizovat image operačního systému na instancích virtuálních počítačů ve škálovací sadě
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: manayar
ms.openlocfilehash: 757ff087b7bb12528779f0477aadb629ea94c73e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566070"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatické upgrady bitové kopie operačního systému sady škálování virtuálních počítačů Azure

Povolení automatického image operačního systému se upgraduje na správu aktualizací pomáhá snadno vaše škálovací sada bezpečně a automaticky upgradujte disk s operačním systémem pro všechny instance ve škálovací sadě.

Automatický upgrade operačního systému má následující vlastnosti:

- Po nakonfigurování se automaticky použije nejnovější image operačního systému, publikováno vydavatelů imagí do škálovací sady, bez zásahu uživatele.
- Upgraduje dávky instancí postupné způsobem pokaždé, když se publikuje nový image platformy vydavatele.
- Se integruje s sond stavu aplikace a [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md).
- Funguje pro všechny velikosti virtuálních počítačů a pro Image platformy Windows i Linuxem.
- Automatické upgrady můžete zrušit kdykoli (upgrady operačního systému lze zahájit také ručně).
- Disk s operačním systémem virtuálního počítače se nahradí nový Disk s operačním systémem vytvořené pomocí nejnovější verze image. Nakonfigurované rozšíření a vlastních dat skripty se spouštějí, při trvalých dat, které disky jsou zachovány.
- [Nastavení posloupnosti rozšíření](virtual-machine-scale-sets-extension-sequencing.md) je podporována.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak funguje automatické operačního systému image upgradu práce?

Upgrade funguje tak, že nahradíte nový disk vytvořené pomocí nejnovější verze image disku s operačním systémem virtuálního počítače. Žádné nakonfigurované rozšíření a vlastních dat skripty jsou spuštěny na disk s operačním systémem, při trvalých dat, které disky jsou zachovány. Aby se minimalizovaly výpadky aplikací, upgrady probíhat v dávkách, s více než 20 % škálovací sady, upgrade kdykoli. Můžete také integrovat sondu stavu aplikací Azure Load Balancer nebo [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md). Doporučené začlenění prezenční signál application jsme ověřit upgrade byl úspěšný pro jednotlivé dávky v procesu upgradu.

Proces upgradu funguje takto:
1. Před zahájením procesu upgradu, orchestrator zajistí, že více než 20 % instancí v celé škálovací sady nejsou v pořádku (z jakéhokoliv důvodu).
2. Upgrade produktu orchestrator identifikuje batch instancí virtuálních počítačů k upgradu s jakékoli jednu dávku maximálně 20 % počet celkový počet instancí.
3. Disk s operačním systémem vybrané dávky instancí virtuálních počítačů je nahrazen nový disk s operačním systémem vytvořené z nejnovější image a všechny zadané rozšíření a konfigurace v modelu škálovací sady se použijí u je upgradovaná instance.
4. Pro škálovací sady pomocí sond stavu konfigurované aplikace nebo rozšíření stavu aplikace upgrade čeká, až 5 minut pro instanci se obnoví dobrý stav, než budete pokračovat k upgradu další dávku.
5. Upgrade produktu orchestrator také sleduje procento instancí, které se stanou upgradu není v pořádku příspěvku. Upgrade zastaví, pokud více než 20 % upgradovaná Instance špatného během procesu upgradu.
6. Výše uvedené proces pokračuje, dokud se neupgradují všechny instance ve škálovací sadě.

Škálovací sada operačního systému kontroly upgradu nástroje orchestrator pro celkového stavu škálovací sady před upgradem každou dávku. Při upgradu dávky, může být jiné souběžné plánované i neplánované údržby aktivity, které by mohlo mít vliv na stav instancím škálovací sady. V takových případech Pokud více než 20 % instancí škálovací sady špatného, pak škálovací sady upgradu zastaví na konci aktuální dávku.

## <a name="supported-os-images"></a>Podporované Image operačního systému
Aktuálně jsou podporovány pouze určité Image platformy operačního systému. Vlastní Image se momentálně nepodporují.

V současnosti jsou podporované následující skladové položky (a více příležitostně přidávat):

| Vydavatel               | Nabídka operačního systému      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04 LTS          |
| Neautorizovaný Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Požadavky na konfiguraci automatický upgrade bitové kopie operačního systému

- *Verze* vlastnost image platformy musí být nastavena na *nejnovější*.
- Pomocí sond stavu aplikace nebo [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md) Service Fabric škálovací sad.
- Ujistěte se, že externí prostředky zadané v modelu škálovací sady jsou k dispozici a aktualizované. Příklady zahrnují identifikátor URI SAS pro spuštění datové části ve vlastnostech rozšíření virtuálního počítače, datová část v účtu úložiště, odkazují k tajným kódům v modelu a další.

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurovat automatický upgrade bitové kopie operačního systému
Chcete-li konfigurovat automatický upgrade operačního systému image, ověřte, zda *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* je nastavena na *true* v škálovací sady definice modelu.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak nastavit automatické upgrady operačního systému v modelu škálovací sady:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Použití [aktualizace AzVmss](/powershell/module/az.compute/update-azvmss) rutiny zkontrolujte historii upgradu operačního systému pro škálovací sadu. V následujícím příkladu nakonfigurujeme automatické upgrady pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Použití [az vmss update](/cli/azure/vmss#az-vmss-update) ke kontrole historie upgradu operačního systému pro škálovací sadu. Použití Azure CLI 2.0.47 nebo vyšší. V následujícím příkladu nakonfigurujeme automatické upgrady pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Stav aplikace pomocí sond

Během upgradu operačního systému se upgradovat instance virtuálních počítačů ve škálovací sadě jednu dávku najednou. Upgrade by měly pokračovat pouze, pokud zákazník aplikace je v pořádku na upgradovaných instancí virtuálních počítačů. Doporučujeme, abyste že aplikace poskytuje signálů stavu pro modul upgradu operačního systému škálovací sady. Ve výchozím nastavení během upgradu operačního systému platformy bere v úvahu stav napájení virtuálního počítače a k určení, zda instance virtuálního počítače je v pořádku po upgradu se stav zřizování rozšíření. Během upgradu operačního systému na instanci virtuálního počítače disk s operačním systémem na instancích virtuálních počítačů se nahradí nový disk založené na nejnovější verzi image. Po dokončení upgradu operačního systému, nakonfigurované rozšíření jsou spuštěny na tyto virtuální počítače. Aplikace se považuje za v pořádku, pouze v případě, že jsou všechna rozšíření na instance úspěšně nezřídí.

Škálovací sada může být volitelně nakonfigurovat s sondy stavu aplikace platformě poskytnout přesné informace o probíhající stav aplikace. Sondy stavu aplikace jsou vlastní zatížení vyrovnávání testy, které se používají jako signál o stavu. Aplikace běžící na instanci škálovací sady virtuálních počítačů může reagovat na externí žádosti protokolu HTTP nebo TCP určující, zda je v pořádku. Další informace o fungování vlastní načíst sondy nástroje pro vyrovnávání najdete v tématu [porozumění testům nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Stav aplikace testu není vyžadován pro škálovací sady Service Fabric, ale doporučuje. Bez Service Fabric škálovací sady vyžadují sondy stavu aplikace buď nástroj pro vyrovnávání zatížení nebo [stav aplikací – rozšíření](virtual-machine-scale-sets-health-extension.md).

Pokud škálovací sada je konfigurován pro použití více skupin umístění, sondy pomocí [Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) muset používat.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurují se vlastní zatížení sondy jako sondu stavu aplikaci na škálovací sadu
Jako osvědčený postup vytvoření explicitně sondy nástroje pro vyrovnávání zatížení, pro škálovací sadu stavu. Můžete použít stejný koncový bod pro existující sondu protokolu HTTP nebo TCP testu, ale sondu stavu může vyžadovat různé chování z testu tradiční – nástroj pro vyrovnávání zatížení. Test nástroje pro vyrovnávání zatížení například může vrátit není v pořádku, pokud je příliš vysoké zatížení na instanci, ale, který by nebylo vhodné pro zjištění stavu instance během automatický upgrade operačního systému. Konfigurace testu a mají zjišťování vysoký méně než dvě minuty.

Test paměti nástroje pro vyrovnávání zatížení můžete odkazovat *položky networkProfile* stupnice nastavit a může být přidružené buď interní nebo veřejný internetový – nástroj pro vyrovnávání zatížení následujícím způsobem:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Pokud používáte automatické upgrady operačního systému s využitím Service Fabric, image nového operačního systému nasazení aktualizační doména podle aktualizačních domén udržet vysokou dostupnost služby spuštěné v Service Fabric. Aby se začala používat automatické upgrady operačního systému v Service Fabric musí být váš cluster nastavená úroveň Silver odolnosti nebo vyšší. Další informace o odolnosti charakteristiky clustery Service Fabric najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Aktualizovat přihlašovací údaje
Pokud vaše škálovací sada používá žádné přihlašovací údaje pro přístup k externím prostředkům, například pokud je nakonfigurovaná rozšíření virtuálního počítače využívající SAS token pro účet úložiště, ujistěte se, že přihlašovací údaje se aktualizují. Pokud žádné přihlašovací údaje, včetně certifikátů a tokeny, vypršela platnost, se upgrade nezdaří a první dávku virtuálních počítačů zůstanou ve stavu selhání.

Doporučené kroky pro obnovení virtuálních počítačů a znovu povolte automatický upgrade operačního systému, pokud dojde k selhání ověřování prostředků jsou:

* Znovu vygenerovat token (nebo jiné přihlašovací údaje) předaná do vašeho rozšíření.
* Ujistěte se, že žádné přihlašovací údaje používané z uvnitř virtuálního počítače ke komunikaci s externí entity je aktuální.
* Aktualizace rozšíření v modelu škálovací sady s všechny nové tokeny.
* Nasazení aktualizované škálovací sady, která aktualizuje všechny instance virtuálních počítačů, včetně neúspěšných.

## <a name="using-application-health-extension"></a>Pomocí rozšíření stavu aplikace
Rozšíření stav aplikací – to je nasazený v instanci virtuálního počítače škálovací sady a sestavy o stavu virtuálních počítačů v rámci instance ve škálovací sadě. Můžete nakonfigurovat rozšíření na koncový bod aplikace pro zjišťování a aktualizovat stav žádosti pro tuto instanci. Tento stav instance se kontroluje Azure k určení, zda je instance vhodné k upgradu operations.

Jako rozšíření sestavy stavu z v rámci virtuálního počítače, rozšíření lze použít v situacích, kde externí testy, jako je například sondy stavu aplikace (které využívají vlastní Azure Load Balancer [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nelze použít.

Existuje více způsobů nasazení stavu aplikace, jak je uvedeno v příkladech v Nastaví rozšíření škálovací [v tomto článku](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Zobrazit historii automatické upgrady bitové kopie operačního systému
Můžete zkontrolovat historii nejnovější upgrade operačního systému provést ve škálovací sadě pomocí Azure Powershellu, příkazového řádku Azure CLI 2.0 nebo rozhraní REST API. Zobrazit historii posledních pěti pokusů upgradu operačního systému v posledních dvou měsíců.

### <a name="rest-api"></a>REST API
Následující příklad používá [rozhraní REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) ke kontrole stavu pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

Vrátí volání GET vlastnosti podobně jako následující příklad výstupu:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Použití [Get-AzVmss](/powershell/module/az.compute/get-azvmss) rutiny zkontrolujte historii upgradu operačního systému pro škálovací sadu. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Použití [az vmss get-os--Historie upgradů](/cli/azure/vmss#az-vmss-get-os-upgrade-history) ke kontrole historie upgradu operačního systému pro škálovací sadu. Použití Azure CLI 2.0.47 nebo vyšší. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak získat nejnovější verzi image platformy operačního systému?

Pro automatické operační systém upgradovat podporované skladové položky pomocí verze image získáte následující příklady:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Šablony můžete použít k nasazení škálovací sady s automatické upgrady operačního systému pro podporované Image, jako [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Další postup
Další příklady o tom, jak používat automatické upgrady operačního systému se škálovacími sadami, přečtěte si [úložiště GitHub se vzorovými](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
