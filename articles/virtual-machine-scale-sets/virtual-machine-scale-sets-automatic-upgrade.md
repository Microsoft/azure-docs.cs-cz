---
title: Automatické upgrady bitových obrázků operačního systému pomocí škálovacích sad virtuálních strojů Azure
description: Zjistěte, jak automaticky upgradovat bitovou kopii operačního systému na instancích virtuálních počítačí ve škálovací sadě
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: mimckitt
ms.openlocfilehash: b1e5ad60041e9d3b902a06a4875206fa061c73e6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269903"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Škálovatautomatické upgrady bitových obrázků virtuálního počítače Azure

Povolení automatických upgradů bitových kopií operačního systému ve škálovací sadě pomáhá usnadnit správu aktualizací tím, že bezpečně a automaticky upgraduje disk operačního systému pro všechny instance ve škálovací sadě.

Automatický upgrade operačního systému má následující charakteristiky:

- Po nakonfigurování se nejnovější bitová kopie operačního systému publikovaná vydavateli bitových stránek automaticky použije na škálovací sadu bez zásahu uživatele.
- Upgraduje dávky instancí postupným způsobem při každém publikování novébitové kopie vydavatelem.
- Integruje se sondy stavu aplikace a [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md).
- Funguje pro všechny velikosti virtuálních počítačích a pro ibitové symboly Windows i Linux.
- Automatické upgrady můžete kdykoli odhlásit (upgrady operačního systému lze iniciovat i ručně).
- Disk operačního systému virtuálního počítače je nahrazen novým diskem operačního systému vytvořeným nejnovější verzí bitové kopie. Nakonfigurovaná rozšíření a vlastní datové skripty jsou spuštěny, zatímco trvalé datové disky jsou zachovány.
- [Sekvenování rozšíření](virtual-machine-scale-sets-extension-sequencing.md) je podporováno.
- Automatický upgrade bitové kopie operačního systému lze povolit v škálovací sadě libovolné velikosti.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak funguje automatický upgrade bitových obrázků operačního systému?

Upgrade funguje tak, že nahradí disk operačního systému virtuálního počítače novým diskem vytvořeným pomocí nejnovější verze bitové kopie. Všechna nakonfigurovaná rozšíření a vlastní datové skripty jsou spouštěny na disku operačního systému, zatímco trvalé datové disky jsou zachovány. Chcete-li minimalizovat prostoje aplikace, upgrady probíhají v dávkách, s maximálně 20 % škálovací sady upgrade kdykoli. Můžete také integrovat sondu stavu aplikace Azure Load Balancer nebo [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md). Doporučujeme začlenit prezenční signál aplikace a ověřit úspěšnost upgradu pro každou dávku v procesu upgradu.

Proces upgradu funguje takto:
1. Před zahájením procesu upgradu orchestrátor zajistí, že ne více než 20 % instancí v celé škálovací sadě není v pořádku (z jakéhokoli důvodu).
2. Upgrade orchestrator identifikuje dávku instancí virtuálních počítačů k upgradu, přičemž každá dávka má maximálně 20 % celkového počtu instancí, s výhradou minimální velikosti dávky jednoho virtuálního počítače.
3. Disk operačního systému vybrané dávky instancí virtuálních počítačů je nahrazen novým diskem operačního systému vytvořeným z nejnovější bitové kopie. Všechna zadaná rozšíření a konfigurace v modelu škálovací sady jsou použity pro inovanou instanci upgradované.
4. Pro škálovací sady s nakonfigurovanými sondami stavu aplikace nebo rozšířením Stavu aplikace čeká upgrade až 5 minut, než se instance stane v pořádku, než přejde k upgradu další dávky. Pokud instance neobnoví svůj stav do 5 minut po upgradu, pak ve výchozím nastavení předchozí disk operačního systému pro instanci je obnovena.
5. Upgrade orchestrator také sleduje procento instancí, které se stanou nezdravé po upgradu. Upgrade se zastaví, pokud více než 20 % upgradovaných instancí se během procesu upgradu stane nefunkčním.
6. Výše uvedený proces pokračuje, dokud nebyly upgradovány všechny instance v škálovací sadě.

Škálovací sada orchestrator upgradu operačního systému kontroluje celkový stav škálovací sady před upgradem každé dávky. Při upgradu dávky mohou existovat další souběžné plánované nebo neplánované aktivity údržby, které by mohly ovlivnit stav instancí škálovací sady. V takových případech, pokud více než 20 % instancí škálovací sady se stanou nefunkční, upgrade škálovací sady se zastaví na konci aktuální dávky.

## <a name="supported-os-images"></a>Podporované obrázky operačního systému
V současné době jsou podporovány pouze určité image platformy operačního systému. Vlastní podpora obrázků je k dispozici [v náhledu](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) pro vlastní obrázky prostřednictvím [Galerie sdílených obrázků](shared-image-galleries.md).

V současné době jsou podporovány následující jednotky SKU platformy (a další jsou přidávány pravidelně):

| Vydavatel               | Nabídka operačního es      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datové centrum    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-s-kontejnery |
| Microsoft Corporation   | WindowsServer | 2019-Datové centrum |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datové centrum s kontejnery |
| Microsoft Corporation   | WindowsServer | Datové centrum-Core-1903-s kontejnery-malý disk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Požadavky na konfiguraci automatického upgradu bitových obrázků operačního systému

- Vlastnost *verze* obrázku musí být nastavena na *nejnovější*.
- Pro škálovací sady prostředků fabric, které nejsou součástí služby, použijte sondy stavu aplikace nebo [rozšíření Stavu aplikace.](virtual-machine-scale-sets-health-extension.md)
- Použijte rozhraní API pro výpočetní prostředky verze 2018-10-01 nebo vyšší.
- Ujistěte se, že jsou k dispozici a aktualizovány externí prostředky zadané v modelu škálovací sady. Mezi příklady patří Identifikátor URI SAS pro zaváděcí datovou část ve vlastnostech rozšíření virtuálního počítače, datová část v účtu úložiště, odkaz na tajné kódy v modelu a další.
- U škálovacích sad používajících virtuální počítače s Windows, počínaje rozhraním Compute API verze 2019-03-01, musí vlastnost *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* nastavit v definici modelu škálovací sady hodnotit *hodnotu false.* Výše uvedená vlastnost umožňuje inovace v modulu v provozu, kde "Windows Update" používá opravy operačního systému bez nahrazení disku operačního systému. Při automatickém upgradu bitové kopie operačního systému povolené na škálovací sadě, další aktualizace prostřednictvím "Windows Update" není vyžadována.

### <a name="service-fabric-requirements"></a>Požadavky na service fabric

Pokud používáte Service Fabric, ujistěte se, že jsou splněny následující podmínky:
-   Úroveň [odolnosti](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) service fabric je stříbrná nebo zlatá, a ne bronzová.
-   Rozšíření Service Fabric na definici modelu škálovací sady musí mít TypeHandlerVersion 1.1 nebo vyšší.
-   Úroveň životnosti by měla být stejná v rozšíření clusteru Service Fabric a Service Fabric v definici modelu škálovací sady.

Ujistěte se, že nastavení odolnosti nejsou neodpovídající clusteru Service Fabric a service fabric rozšíření, jako neshoda bude mít za následek chyby upgradu. Úrovně odolnosti lze upravit podle pokynů uvedených na [této stránce](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Automatický upgrade obrazu operačního systému pro vlastní obrázky (náhled)

> [!IMPORTANT]
> Automatický upgrade bitové kopie operačního systému pro vlastní obrázky je aktuálně ve veřejné verzi Preview. K použití funkce public preview popsané níže je nutný postup přihlášení.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automatický upgrade bitové kopie operačního systému je k dispozici v náhledu pro vlastní bitové kopie nasazené prostřednictvím [Galerie sdílených obrázků](shared-image-galleries.md). Jiné vlastní image nejsou podporovány pro automatické upgrady bitových kopií operačního systému.

Povolení funkce náhledu vyžaduje jednorázové přihlášení pro funkci *AutomaticOSUpgradeWithGalleryImage* na předplatné, jak je podrobně popsáno níže.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak povolit náhled předplatného:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

Registrace funkcí může trvat až 15 minut. Kontrola stavu registrace:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Jakmile je funkce zaregistrována pro vaše předplatné, dokončete proces přihlášení tak, že změnu rozšíříte do poskytovatele výpočetních prostředků.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) povolte náhled pro vaše předplatné.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Registrace funkcí může trvat až 15 minut. Kontrola stavu registrace:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Jakmile je funkce zaregistrována pro vaše předplatné, dokončete proces přihlášení tak, že změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [registru funkcí az](/cli/azure/feature#az-feature-register) povolte náhled předplatného.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Registrace funkcí může trvat až 15 minut. Kontrola stavu registrace:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Jakmile je funkce zaregistrována pro vaše předplatné, dokončete proces přihlášení tak, že změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Další požadavky na vlastní obrázky
- Výše popsaný proces přihlášení je třeba dokončit pouze jednou za předplatné. Po dokončení opt-in, automatické upgrady operačního systému lze povolit pro libovolné škálování sady v tomto předplatném.
- Galerie sdílených obrázků může být v libovolném předplatném a nevyžaduje samostatné přihlášení. Pouze předplatné škálovací sady vyžaduje funkci opt-in.
- Proces konfigurace pro automatický upgrade bitové kopie operačního systému je stejný pro všechny škálovací sady, jak je podrobně popsáno v [části konfigurace](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) této stránky.
- Instance škálovacísady nakonfigurované pro automatické upgrady bitových obrázků operačního systému budou upgradovány na nejnovější verzi bitové kopie Galerie sdílených obrázků, když je publikována nová verze bitové kopie a [replikována](shared-image-galleries.md#replication) do oblasti této škálovací sady. Pokud nová bitová kopie není replikována do oblasti, kde se nasazuje měřítko, instance škálovací sady nebudou upgradovány na nejnovější verzi. Regionální replikace bitových obrázků umožňuje řídit zavádění nové bitové kopie pro škálovací sady.
- Nová verze obrázku by neměla být vyloučena z nejnovější verze pro tento obrázek galerie. Verze obrázků vyloučené z nejnovější verze obrázku galerie nejsou zavedeny do škálovací sady prostřednictvím automatického upgradu bitové kopie operačního systému.

> [!NOTE]
>Škálovací sada může trvat až 2 hodiny, než získá první spuštění bitové kopie po konfiguraci škálovací sady pro automatické upgrady operačního systému. Jedná se o jednorázové zpoždění na sadu škálování. Následné zavedení obrázku se bez tohoto zpoždění aplikují na škálovací sadu.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurace automatického upgradu bitové kopie operačního systému
Chcete-li nakonfigurovat automatický upgrade bitové kopie operačního systému, ujistěte se, že vlastnost *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* je v definici modelu škálovací sady nastavena na *hodnotu true.*

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak nastavit automatické upgrady operačního systému na modelu škálovací sady:

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
Pomocí rutiny [Aktualizace AzVmss](/powershell/module/az.compute/update-azvmss) můžete nakonfigurovat automatické upgrady bitových obrázků operačního systému pro škálovací sadu. Následující příklad konfiguruje automatické inovace škálovací sady s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [aktualizace az vmss](/cli/azure/vmss#az-vmss-update) nakonfigurujte automatické upgrady bitových obrázků operačního systému pro škálovací sadu. Použijte Azure CLI 2.0.47 nebo vyšší. Následující příklad konfiguruje automatické inovace škálovací sady s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Použití sond stavu aplikace

Během upgradu operačního systému jsou instance virtuálních virtuálních mitálových vozidli upgradovány po jedné dávce. Upgrade by měl pokračovat pouze v případě, že je aplikace zákazníka v pořádku na instancích upgradovaného virtuálního počítače. Doporučujeme, aby aplikace poskytuje signály stavu pro modul upgradu operačního systému škálování. Ve výchozím nastavení během upgradu operačního systému platforma považuje stav napájení virtuálního soudu a stav zřizování rozšíření k určení, jestli je instance virtuálního aplikace po upgradu v pořádku. Během upgradu operačního systému instance virtuálního počítače je disk operačního systému na instanci virtuálního počítače nahrazen novým diskem založeným na nejnovější verzi bitové kopie. Po dokončení upgradu operačního systému jsou na těchto virtuálních počítačích spuštěna nakonfigurovaná rozšíření. Aplikace se považuje za v pořádku pouze v případě, že jsou úspěšně zřízena všechna rozšíření v instanci.

Škálovací sadu lze volitelně nakonfigurovat pomocí sond stavu aplikace, která platformě poskytuje přesné informace o probíhajícím stavu aplikace. Sondy stavu aplikace jsou vlastní sondy pro vyrovnávání zatížení, které se používají jako signál stavu. Aplikace spuštěná na instanci virtuálního počítače škálovací sady může reagovat na externí požadavky HTTP nebo TCP označující, zda je v pořádku. Další informace o tom, jak vlastní sondy pro vyrovnávání zatížení fungují, naleznete [v tématu Principy sond pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Sondy stavu aplikace nejsou podporovány pro škálovací sady Service Fabric. Škálovací sady neservisního materiálu vyžadují sondy stavu aplikace Vyrovnávání zatížení nebo [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md).

Pokud je škálovací sada nakonfigurována tak, aby používala více skupin umístění, je třeba použít sondy používající [standardní vytažitelé zatížení.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurace vlastní sondy pro vyrovnávání zatížení jako sondy stavu aplikace na škálovací sadě
Jako osvědčený postup vytvořte sondu pro vyrovnávání zatížení explicitně pro stav škálovací sady. Stejný koncový bod pro existující sondu HTTP nebo TCP sondu lze použít, ale sonda stavu může vyžadovat jiné chování než tradiční sonda nástroj pro vyrovnávání zatížení. Například tradiční sonda nástroje pro vyrovnávání zatížení může vrátit není v pořádku, pokud zatížení instance je příliš vysoká, ale to by nebylo vhodné pro určení stavu instance během automatického upgradu operačního systému. Nakonfigurujte sondu tak, aby měla vysokou rychlost zjišťování kratší než dvě minuty.

Sonda pro vyrovnávání zatížení může být odkazována v *sítiProfil* škálovací sady a může být přidružena k internímu nebo veřejnému vyvažovači zatížení následujícím způsobem:

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
> Při použití automatických upgradů operačního systému s service fabric, nová bitová kopie operačního systému je zavedena aktualizovat doménu aktualizací domény zachovat vysokou dostupnost služeb spuštěných v Service Fabric. Chcete-li využít automatické upgrady operačního systému v service fabric, musí být váš cluster nakonfigurován tak, aby používal úroveň odolnosti silver nebo vyšší. Další informace o vlastnostech životnosti clusterů Service Fabric naleznete v [této dokumentaci](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Udržovat přihlašovací údaje aktuální
Pokud vaše škálovací sada používá všechna pověření pro přístup k externím prostředkům, jako je například rozšíření virtuálního počítače nakonfigurované pro použití tokenu SAS pro účet úložiště, ujistěte se, že se přihlašovací údaje aktualizují. Pokud vypršela platnost všech přihlašovacích údajů, včetně certifikátů a tokenů, upgrade se nezdaří a první dávka virtuálních discích zůstane ve stavu selhání.

Doporučené kroky k obnovení virtuálních zařízení a opětovné povolení automatického upgradu operačního systému, pokud dojde k chybě ověřování prostředků, jsou:

* Znovu vygenerovat token (nebo jiné přihlašovací údaje) předány do rozšíření.Generate the token (or any other credentials) passed into your extension(s).
* Ujistěte se, že všechna pověření použitá z vnitřního virtuálního virtuálního aplikace k rozhovoru s externími entitami jsou aktuální.
* Aktualizujte rozšíření v modelu škálovací sady s novými tokeny.
* Nasaďte aktualizovanou škálovací sadu, která aktualizuje všechny instance virtuálních počítačů včetně těch, které selhaly.

## <a name="using-application-health-extension"></a>Použití rozšíření Stav aplikace
Rozšíření Stav aplikace se nasadí uvnitř instance škálovací sady virtuálních strojů a hlásí stav virtuálního počítače z instance škálovací sady. Můžete nakonfigurovat rozšíření pro sondování na koncovém bodu aplikace a aktualizovat stav aplikace v této instanci. Tento stav instance je kontrolována Azure k určení, zda instance je způsobilá pro operace upgradu.

Jako rozšíření hlásí stav z v rámci virtuálního počítače, rozšíření lze použít v situacích, kdy externí sondy, jako jsou sondy stavu aplikace (které využívají vlastní azure nástroje pro vyrovnávání zatížení [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nelze použít.

Existuje několik způsobů nasazení rozšíření Stav aplikace pro škálovací sady, jak je podrobně popsáno v příkladech v [tomto článku](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Získejte historii automatických upgradů obrázků operačního systému
Můžete zkontrolovat historii nejnovější upgrade operačního systému provádí na škálovací sadu s Azure PowerShell, Azure CLI 2.0 nebo REST API. Můžete získat historii za posledních pět pokusů o upgrade operačního systému během posledních dvou měsíců.

### <a name="rest-api"></a>REST API
Následující příklad používá [rozhraní REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) ke kontrole stavu škálovací sady s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

Volání GET vrátí vlastnosti podobné následujícímu příkladu výstupu:

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
Pomocí [rutiny Get-AzVmss](/powershell/module/az.compute/get-azvmss) zkontrolujte historii upgradu operačního systému pro vaši škálovací sadu. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro škálovací sadu s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [historie získání az vmss get-os-upgrade](/cli/azure/vmss#az-vmss-get-os-upgrade-history) zkontrolujte historii upgradu operačního systému pro vaši škálovací sadu. Použijte Azure CLI 2.0.47 nebo vyšší. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro škálovací sadu s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak získat nejnovější verzi image operačního systému platformy?

Můžete získat dostupné verze bitových označení pro automatické upgrady operačního systému podporované soupoložky s využitím následujících příkladů:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Ruční aktivace upgradů bitových obrázků operačního systému
Při automatickém upgradu bitové kopie operačního systému povolené na škálovací sadě není nutné ručně spouštět aktualizace obrázků v škálovací sadě. Orchestrátor upgradu operačního systému automaticky použije nejnovější dostupnou verzi bitové kopie pro instance škálovací sady bez jakéhokoli ručního zásahu.

Pro konkrétní případy, kdy nechcete čekat na orchestrator použít nejnovější obrázek, můžete spustit upgrade obrazu operačního systému ručně pomocí níže uvedených příkladů.

> [!NOTE]
> Ruční aktivační událost upgradů bitových obrázků operačního systému neposkytuje možnosti automatického vrácení zpět. Pokud instance neobnoví svůj stav po operaci upgradu, její předchozí disk operačního systému nelze obnovit.

### <a name="rest-api"></a>REST API
Pomocí volání rozhraní API [pro upgrade operačního systému spuštění](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) postupného upgradu přesuňte všechny instance škálovací sady virtuálních strojů na nejnovější dostupnou verzi operačního systému image. Instance, které jsou již spuštěna nejnovější dostupnou verzi operačního systému nejsou ovlivněny. Následující příklad podrobně popisuje, jak můžete spustit postupný upgrade operačního systému na škálovací sadě s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) zkontrolujte historii upgradu operačního systému pro škálovací sadu. Následující příklad podrobně popisuje, jak můžete spustit postupný upgrade operačního systému na škálovací sadě s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [počátečníinovace az vmss můžete](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) zkontrolovat historii upgradu operačního systému pro škálovací sadu. Použijte Azure CLI 2.0.47 nebo vyšší. Následující příklad podrobně popisuje, jak můžete spustit postupný upgrade operačního systému na škálovací sadě s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Nasazení pomocí šablony

Šablony můžete použít k nasazení škálovací sady s automatickými upgrady operačního systému pro podporované obrázky, jako je [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Další kroky
Další příklady použití automatických upgradů operačního systému se škálovacími sadami najděte [úložiště GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
