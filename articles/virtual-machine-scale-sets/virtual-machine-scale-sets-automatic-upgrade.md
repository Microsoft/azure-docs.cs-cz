---
title: Škálovací sady automatické upgrady bitové kopie operačního systému pomocí virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak automaticky aktualizovat image operačního systému na instancích virtuálních počítačů ve škálovací sadě
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: 1ca0ec7185707d9b9f9712c2ace8dacb361f7b5b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394365"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatické upgrady bitové kopie operačního systému sady škálování virtuálních počítačů Azure

Automatický upgrade operačního systému image je funkce škálovací sady virtuálních počítačů Azure, která automaticky upgraduje všechny virtuální počítače na nejnovější image operačního systému.

Automatický upgrade operačního systému má následující vlastnosti:

- Po nakonfigurování se automaticky použije nejnovější image operačního systému, publikováno vydavatelů imagí do škálovací sady, bez zásahu uživatele.
- Upgraduje dávky instancí postupné způsobem pokaždé, když se publikuje nový image platformy vydavatele.
- Se integruje s sondu stavu aplikace.
- Funguje pro všechny velikosti virtuálních počítačů a pro Image platformy Windows i Linuxem.
- Automatické upgrady můžete zrušit kdykoli (upgrady operačního systému lze zahájit také ručně).
- Disk s operačním systémem virtuálního počítače se nahradí nový Disk s operačním systémem vytvořené pomocí nejnovější verze image. Nakonfigurované rozšíření a vlastních dat skripty se spouštějí, při trvalých dat, které disky jsou zachovány.
- Azure disk encryption (ve verzi preview) se aktuálně nepodporuje.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak funguje automatické operačního systému image upgradu práce?

Upgrade funguje tak, že nahradíte novým vytvořené pomocí nejnovější verze image disku s operačním systémem virtuálního počítače. Žádné nakonfigurované rozšíření a vlastních dat skripty se spouštějí při trvalých dat, které disky jsou zachovány. Aby se minimalizovaly výpadky aplikací, upgrady probíhat v dávkách počítačů s více než 20 % škálovací sady, upgrade kdykoli. Máte také možnost integrovat sondu stavu aplikací Azure Load Balancer. Důrazně doporučujeme začlenit prezenčního signálu aplikaci a ověřit upgrade byl úspěšný pro jednotlivé dávky v procesu upgradu. Postup spuštění je: 

1. Před zahájením procesu upgradu, orchestrator zajistí, že více než 20 % instancí nejsou v pořádku. 
2. Identifikujte batch instancí virtuálních počítačů, které chcete provést upgrade, službou batch s maximálně 20 % počet celkový počet instancí.
3. Aktualizace image operačního systému tuto dávku instancí virtuálních počítačů.
4. Pokud zákazník má nakonfigurovanou sond stavu aplikace, upgrade čeká, až 5 minut, než se testy se obnoví dobrý stav, než budete pokračovat k upgradu další dávku. 
5. Pokud nezbývají instance, které chcete upgradovat, goto krok 1) pro další dávku; v opačném případě nebude dokončen upgrade.

Škálovací sada operačního systému upgradu nástroje orchestrator kontroluje celkový stav instance virtuálních počítačů před upgradem každou dávku. Při upgradu dávky, může být jiné souběžné plánovaná nebo neplánovaná Údržba děje v datových centrech Azure, které můžou ovlivnit dostupnost vašich virtuálních počítačů. Proto je možné, že dočasně více než 20 % instancí může být mimo provoz. V takovém případě škálovací sady na konci aktuální dávky upgradu zastaví.

## <a name="supported-os-images"></a>Podporované Image operačního systému
Aktuálně jsou podporovány pouze určité Image platformy operačního systému. Nelze použít aktuálně vlastních imagí, abyste měli jste sami vytvořili. 

Aktuálně jsou podporovány následující skladové jednotky (přibudou další v budoucnu):
    
| Vydavatel               | Nabídka operačního systému      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04 LTS *        | 
| Neautorizovaný Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016 Datacenter s kontejnery |

* Podpora pro tyto Image se právě zavádí a bude brzy k dispozici ve všech oblastech Azure. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Požadavky na konfiguraci automatický upgrade bitové kopie operačního systému

- *Verze* vlastnost image platformy musí být nastavena na *nejnovější*.
- Pomocí sond stavu aplikace bez Service Fabric škálovacích sad.
- Ujistěte se, že prostředky, že modelu škálovací sady odkazující na je k dispozici a pořád aktuální. 
  Identifikátor URI Exa.SAS samozaváděcí datové části ve vlastnostech rozšíření virtuálního počítače, datová část v účtu úložiště, odkazují na k tajným kódům v modelu. 

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurovat automatický upgrade bitové kopie operačního systému
Chcete-li konfigurovat automatický upgrade operačního systému image, ověřte, zda *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* je nastavena na *true* v škálovací sady definice modelu. 

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

Podpora konfigurace této vlastnosti prostřednictvím Azure Powershellu nebo CLI 2.0 se začne distribuovat na 10/09.

## <a name="using-application-health-probes"></a>Stav aplikace pomocí sond 

Během upgradu operačního systému se upgradovat instance virtuálních počítačů ve škálovací sadě jednu dávku najednou. Upgrade by měly pokračovat pouze, pokud zákazník aplikace je v pořádku na upgradovaných instancí virtuálních počítačů. Doporučujeme vám, že aplikace poskytuje signálů stavu pro modul upgradu operačního systému škálovací sady. Ve výchozím nastavení během upgradu operačního systému platformy bere v úvahu stav napájení virtuálního počítače a k určení, zda instance virtuálního počítače je v pořádku po upgradu se stav zřizování rozšíření. Během upgradu operačního systému na instanci virtuálního počítače disk s operačním systémem na instancích virtuálních počítačů se nahradí nový disk založené na nejnovější verzi image. Po dokončení upgradu operačního systému, nakonfigurované rozšíření jsou spuštěny na tyto virtuální počítače. Pouze v případě, že všechna rozšíření na virtuálním počítači se úspěšně zřízený, aplikace považuje za v pořádku. 

Škálovací sada může být volitelně nakonfigurovat s sondy stavu aplikace platformě poskytnout přesné informace o probíhající stav aplikace. Sondy stavu aplikace jsou vlastní zatížení vyrovnávání testy, které se používají jako signál o stavu. Aplikace běžící na instanci škálovací sady virtuálních počítačů může reagovat na externí žádosti protokolu HTTP nebo TCP určující, zda je v pořádku. Další informace o fungování vlastní načíst sondy nástroje pro vyrovnávání najdete v tématu [porozumění testům nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Stav aplikace testu není vyžadován pro automatické upgrady operačního systému, ale důrazně doporučujeme.

Pokud škálovací sada je konfigurován pro použití více skupin umístění, sondy pomocí [Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) muset používat.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurují se vlastní zatížení sondy jako sondu stavu aplikaci na škálovací sadu
Jako osvědčený postup vytvoření explicitně sondy nástroje pro vyrovnávání zatížení, pro škálovací sadu stavu. Stejný koncový bod pro existující sondu protokolu HTTP nebo TCP sondy mohou být použity, ale sondu stavu může vyžadovat různé chování z testu tradiční – nástroj pro vyrovnávání zatížení. Test nástroje pro vyrovnávání zatížení může například vrátit není v pořádku, pokud v instanci je příliš vysoké, vzhledem k tomu, který nemusí být vhodný pro zjištění stavu instance během automatický upgrade operačního systému. Konfigurace testu a mají zjišťování vysoký méně než dvě minuty.

Test paměti nástroje pro vyrovnávání zatížení můžete odkazovat *položky networkProfile* stupnice nastavit a může být přidružené buď interní nebo veřejný internetový – nástroj pro vyrovnávání zatížení následujícím způsobem:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Pokud používáte automatické upgrady operačního systému s využitím Service Fabric, image nového operačního systému nasazení aktualizační doména podle aktualizačních domén udržet vysokou dostupnost služby spuštěné v Service Fabric. Další informace o odolnosti charakteristiky clustery Service Fabric najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Aktualizovat přihlašovací údaje
Pokud vaše škálovací sada používá žádné přihlašovací údaje pro přístup k externím prostředkům, například pokud je nakonfigurovaná rozšíření virtuálního počítače využívající SAS token pro účet úložiště, musíte zajistit, aby že přihlašovací údaje jsou tak pořád aktuální. Pokud žádné přihlašovací údaje, včetně certifikátů a tokeny mají platnost, se upgrade nezdaří a první dávku virtuálních počítačů zůstanou ve stavu selhání.

Doporučené kroky pro obnovení virtuálních počítačů a znovu povolte automatický upgrade operačního systému, pokud dojde k selhání ověřování prostředků jsou:

* Znovu vygenerovat token (nebo jiné přihlašovací údaje) předaná do vašeho rozšíření.
* Ujistěte se, že žádné přihlašovací údaje používané z uvnitř virtuálního počítače ke komunikaci s externí entity je aktuální.
* Aktualizace rozšíření v modelu škálovací sady s všechny nové tokeny.
* Nasazení aktualizované škálovací sady, která aktualizuje všechny instance virtuálních počítačů, včetně neúspěšných. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Zobrazit historii automatické upgrady bitové kopie operačního systému 
Můžete zkontrolovat historii nejnovější upgrade operačního systému provést ve škálovací sadě pomocí Azure Powershellu, příkazového řádku Azure CLI 2.0 nebo rozhraní REST API. Zobrazit historii posledních pěti pokusů upgradu operačního systému v posledních dvou měsíců.

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Následující příklad používá rozhraní příkazového řádku Azure (2.0.20 nebo novější) a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
Následující příklad používá rozhraní REST API a zkontrolujte stav pro škálovací sadu s názvem *myVMSS* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Podrobnosti najdete v dokumentaci pro toto rozhraní API: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak získat nejnovější verzi image platformy operačního systému? 

Pro automatické operační systém upgradovat podporované skladové položky pomocí verze image získáte následujících rutin prostředí PowerShell: 

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

## <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Následující šablony můžete použít k nasazení škálovací sady, který používá automatické upgrady <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatické postupných upgradů – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Další postup
Další příklady o tom, jak používat automatické upgrady operačního systému se škálovacími sadami najdete v článku [úložiště GitHub pro funkce ve verzi preview](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
