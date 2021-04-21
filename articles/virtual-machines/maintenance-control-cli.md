---
title: Řízení údržby pro virtuální počítače Azure pomocí rozhraní příkazového řádku
description: Naučte se řídit, kdy se na virtuální počítače Azure použije údržba pomocí řízení údržby a CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765894"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Řízení aktualizací pomocí řízení údržby a Azure CLI

Řízení údržby vám umožní určit, kdy se mají použít aktualizace platforem pro hostování infrastruktury pro izolované virtuální počítače a vyhrazené hostitele Azure. Toto téma popisuje možnosti rozhraní příkazového řádku Azure CLI pro řízení údržby. Další informace o výhodách použití řízení údržby, jejich omezení a dalších možností správy najdete v tématu [Správa aktualizací platformy pomocí řízení údržby](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Vytvoření konfigurace údržby

Použijte `az maintenance configuration create` k vytvoření konfigurace údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s oborem názvů hostitele. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Zkopírujte ID konfigurace z výstupu pro pozdější použití.

Pomocí nástroje `--maintenance-scope host` je zajištěno, že se konfigurace údržby používá pro řízení aktualizací infrastruktury hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurace musí být pro vaši skupinu prostředků jedinečné.

K dostupným konfiguracím údržby se můžete dotázat pomocí `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Vytvoření konfigurace údržby pomocí plánovaného okna
Můžete také deklarovat naplánované okno, když Azure použije aktualizace vašich prostředků. Tento příklad vytvoří konfiguraci údržby s názvem myConfig s plánovaným oknem 5 hodin ve čtvrtém pondělí každého měsíce. Po vytvoření naplánovaného okna už aktualizace nemusíte instalovat ručně.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> **Doba trvání** údržby musí být *2 hodiny* nebo déle. **Opakování** údržby musí být nastavené na nejméně jednou za 35 dní.

Opakování údržby může být vyjádřeno jako denní, týdenní nebo měsíční. Tady je několik příkladů:
- **denní** údržba – okno – opakování – každé: "den" **nebo** "3Days"
- **týdenní**– údržba – okno – opakování – každé: "3Weeks" **nebo** "týden sobotu, neděle"
- **měsíčně**– údržba – okno – opakování – každé: "měsíc day23, day24" **nebo** "Month Last neděle" **nebo** "Month čtvrté pondělí"


## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Použijte `az maintenance assignment create` k přiřazení konfigurace vašemu izolovanému virtuálnímu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Použijte konfiguraci na virtuální počítač s použitím ID konfigurace. Zadejte `--resource-type virtualMachines` a zadejte název virtuálního počítače pro `--resource-name` a skupinu prostředků pro virtuální počítač v nástroji `--resource-group` a umístění virtuálního počítače pro `--location` . 

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

Chcete-li použít konfiguraci pro vyhrazeného hostitele, je třeba zahrnout `--resource-type hosts` `--resource-parent-name` s názvem skupiny hostitelů a `--resource-parent-type hostGroups` . 

Parametr `--resource-id` je ID hostitele. K získání ID vyhrazeného hostitele můžete použít [příkaz AZ VM Host Get-instance-View](/cli/azure/vm/host#az_vm_host_get_instance_view) .

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

## <a name="check-configuration"></a>Ověřit konfiguraci

Můžete ověřit, jestli se konfigurace použila správně, nebo zkontrolovat, která konfigurace se v tuto chvíli používá `az maintenance assignment list` .

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

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


## <a name="check-for-pending-updates"></a>Vyhledat nedokončené aktualizace

Použijte `az maintenance update list` k zobrazení, zda čekají na aktualizace. Aktualizace--předplatné jako ID pro předplatné, které obsahuje virtuální počítač.

Pokud nejsou k dispozici žádné aktualizace, příkaz vrátí chybovou zprávu, která bude obsahovat text: `Resource not found...StatusCode: 404` .

Pokud jsou k dispozici aktualizace, bude vrácena pouze jedna, i když je dokončeno více aktualizací. Data pro tuto aktualizaci budou vrácena v objektu:

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

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vyhledejte nedokončené aktualizace pro izolovaný virtuální počítač. V tomto příkladu je výstup formátovaný jako tabulka pro čitelnost.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Pro kontrolu nedokončených aktualizací pro vyhrazeného hostitele. V tomto příkladu je výstup formátovaný jako tabulka pro čitelnost. Nahraďte hodnoty pro prostředky vlastními.

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

Použijte `az maintenance apply update` k instalaci nedokončených aktualizací. Po úspěšném provedení tohoto příkazu vrátí JSON obsahující podrobnosti o aktualizaci.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vytvořte žádost o použití aktualizací pro izolovaný virtuální počítač.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Vyhrazený hostitel

Použijte aktualizace na vyhrazeného hostitele.

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

## <a name="check-the-status-of-applying-updates"></a>Ověřte stav použití aktualizací. 

Průběh aktualizací můžete zjistit pomocí `az maintenance applyupdate get` . 

Můžete použít `default` jako název aktualizace k zobrazení výsledků Poslední aktualizace nebo nahradit `myUpdateName` názvem aktualizace, která byla vrácena při spuštění `az maintenance applyupdate create` .

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
LastUpdateTime bude čas, kdy se aktualizace dokončila, ať už iniciovaná vámi, nebo platformou v případě, že se okno samoobslužné údržby nepoužilo. Pokud se v rámci řízení údržby nikdy nepoužila aktualizace, zobrazí se výchozí hodnota.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

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


## <a name="delete-a-maintenance-configuration"></a>Odstraní konfiguraci údržby.

Slouží `az maintenance configuration delete` k odstranění konfigurace údržby. Odstranění konfigurace odebere z přidružených prostředků řízení údržby.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).
