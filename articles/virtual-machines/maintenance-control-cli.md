---
title: Řízení údržby
description: Zjistěte, jak řídit, kdy se údržba použije na vaše virtuální počítače Azure pomocí řízení údržby.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250176"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Náhled: Řízení aktualizací pomocí řízení údržby a azure cli

Spravujte aktualizace platformy, které nevyžadují restartování pomocí řízení údržby. Azure často aktualizuje svou infrastrukturu, aby zlepšil spolehlivost, výkon, zabezpečení nebo spustil nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako je hraní her, streamování médií a finanční transakce, nemohou tolerovat ani několik sekund zamrznutí nebo odpojení virtuálního počítače z důvodu údržby. Řízení údržby vám dává možnost čekat na aktualizace platformy a aplikovat je v rámci 35denního rolovacího okna. 

Řízení údržby umožňuje rozhodnout, kdy použít aktualizace pro izolované virtuální počítače a azure dedicated hosts.

S kontrolou údržby můžete:
- Dávkové aktualizace do jednoho balíčku aktualizace.
- Vyčkejte až 35 dní, než chcete nainstalovat aktualizace. 
- Automatizujte aktualizace platformy pro okno údržby pomocí Funkce Azure.
- Konfigurace údržby fungují napříč předplatnými a skupinami prostředků. 

> [!IMPORTANT]
> Řízení údržby je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./linux/dedicated-hosts.md)nebo být vytvořeny pomocí [izolované velikosti virtuálního počítače](./linux/isolation.md).
- Po 35 dnech bude automaticky použita aktualizace.
- Uživatel musí mít přístup **přispěvatele prostředků.**


## <a name="install-the-maintenance-extension"></a>Instalace rozšíření údržby

Pokud se rozhodnete nainstalovat [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) místně, budete potřebovat verzi 2.0.76 nebo novější.

Nainstalujte `maintenance` rozšíření preview CLI místně nebo v cloudovém prostředí. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Vytvoření konfigurace údržby

Slouží `az maintenance configuration create` k vytvoření konfigurace údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s rozsahem hostitele. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Zkopírujte ID konfigurace z výstupu, který chcete použít později.

Použití `--maintenanceScope host` zajišťuje, že konfigurace údržby se používá pro řízení aktualizací hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurací musí být jedinečné pro vaše předplatné.

Můžete dotaz na dostupné konfigurace `az maintenance configuration list`údržby pomocí .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Slouží `az maintenance assignment create` k přiřazení konfigurace k izolovanému virtuálnímu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Použijte konfiguraci na virtuální ms pomocí ID konfigurace. Zadejte `--resource-type virtualMachines` a zadejte název `--resource-name`virtuálního_ virtuálního_ a. `--resource-group`skupiny prostředků pro virtuální `--location`ho d. a umístění virtuálního_ 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Chcete-li použít konfiguraci vyhrazeného hostitele, je třeba zahrnout `--resource-type hosts`, `--resource-parent-name` `--resource-parent-type hostGroups`s názvem skupiny hostitelů a . 

Parametr `--resource-id` je ID hostitele. Můžete použít [az vm hostitele get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) získat ID vašeho vyhrazeného hostitele.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Zkontrolovat konfiguraci

Můžete ověřit, zda byla konfigurace použita správně, nebo zkontrolovat, `az maintenance assignment list`jaká konfigurace je aktuálně použita pomocí aplikace .

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Vyhrazený hostitel 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Vyhledat čekající aktualizace

Slouží `az maintenance update list` k zobrazení, zda existují čekající aktualizace. Update --subscription to be the ID for the subscription that contains the VM.

Pokud nejsou k dispozici žádné aktualizace, příkaz vrátí chybovou `Resource not found...StatusCode: 404`zprávu, která bude obsahovat text: .

Pokud existují aktualizace, bude vrácena pouze jedna, i když existuje více čekajících aktualizací. Data pro tuto aktualizaci budou vrácena v objektu:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Zkontrolujte čekající aktualizace pro izolovaný virtuální ms. V tomto příkladu je výstup formátován jako tabulka pro čitelnost.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Chcete-li vyhledat čekající aktualizace pro vyhrazeného hostitele. V tomto příkladu je výstup formátován jako tabulka pro čitelnost. Nahraďte hodnoty prostředků vlastními.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Instalace aktualizací

Slouží `az maintenance apply update` k použití čekajících aktualizací. Při úspěchu tento příkaz vrátí JSON obsahující podrobnosti o aktualizaci.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Vytvořte požadavek na použití aktualizací na izolovaný virtuální ms.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Vyhrazený hostitel

Použijte aktualizace vyhrazeného hostitele.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Kontrola stavu použití aktualizací 

Průběh aktualizací můžete zkontrolovat pomocí `az maintenance applyupdate get`programu . 

Jako název `default` aktualizace můžete zobrazit výsledky poslední aktualizace nebo `myUpdateName` nahradit názvem aktualizace, která byla `az maintenance applyupdate create`vrácena při spuštění .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime bude čas, kdy aktualizace byla dokončena, buď iniciované vámi, nebo platformou v případě, že okno samoúdržby nebylo použito. Pokud nikdy nebyla provedena aktualizace prostřednictvím řízení údržby, zobrazí se výchozí hodnota.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Odstranění konfigurace údržby

Slouží `az maintenance configuration delete` k odstranění konfigurace údržby. Odstraněníkonfigurace odebere ovládací prvek údržby z přidružených prostředků.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v [tématu Údržba a aktualizace](maintenance-and-updates.md).
