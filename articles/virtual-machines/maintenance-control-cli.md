---
title: Řízení údržby pro virtuální počítače Azure
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržby pomocí řízení údržby.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: e2eb77bfd000ecaa3bad5fd3c5792d1aa3a81964
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964868"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Verze Preview: řízení aktualizací pomocí řízení údržby a Azure CLI

Spravujte aktualizace platforem, které nevyžadují restart, pomocí řízení údržby. Azure často aktualizuje svoji infrastrukturu, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako jsou hraní her, streamování médií a finanční transakce, neumožňují tolerovat zamrznutí nebo odpojení virtuálních počítačů za účelem údržby ani pár sekund. Řízení údržby vám dává možnost počkat na aktualizace platformy a použít ji v průběhu 35ého okna. 

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure.

Pomocí řízení údržby můžete:
- Dávka se aktualizuje do jednoho balíčku aktualizace.
- Počkejte až 35 dní, než se aktualizace použijí. 
- Automatizujte aktualizace platforem pro okno údržby pomocí Azure Functions.
- Konfigurace údržby pracují v rámci předplatných a skupin prostředků. 

> [!IMPORTANT]
> Řízení údržby je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./linux/dedicated-hosts.md)nebo být vytvořené pomocí [izolované velikosti virtuálního počítače](./linux/isolation.md).
- Po 35 dnech se automaticky použije aktualizace.
- Uživatel musí mít přístup k **přispěvateli prostředků** .


## <a name="install-the-maintenance-extension"></a>Instalace rozšíření údržby

Pokud se rozhodnete nainstalovat rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) místně, budete potřebovat verzi 2.0.76 nebo novější.

Nainstalujte rozhraní příkazového řádku Preview `maintenance` ve verzi Preview místně nebo v Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Vytvořit konfiguraci údržby

K vytvoření konfigurace údržby použijte `az maintenance configuration create`. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s oborem názvů hostitele. 

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

Zkopírujte ID konfigurace z výstupu pro pozdější použití.

Pomocí `--maintenanceScope host` zajistíte, aby se konfigurace údržby používala k řízení aktualizací hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurace musí být pro vaše předplatné jedinečné.

K dostupným konfiguracím údržby se můžete dotázat pomocí `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Pomocí `az maintenance assignment create` přiřaďte konfiguraci k vašemu izolovanému VIRTUÁLNÍmu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Použijte konfiguraci na virtuální počítač s použitím ID konfigurace. Zadejte `--resource-type virtualMachines` a zadejte název virtuálního počítače pro `--resource-name`a skupinu prostředků pro virtuální počítač do `--resource-group`a umístění virtuálního počítače pro `--location`. 

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

Chcete-li použít konfiguraci pro vyhrazeného hostitele, je třeba zahrnout `--resource-type hosts``--resource-parent-name` s názvem skupiny hostitelů a `--resource-parent-type hostGroups`. 

Parametr `--resource-id` je ID hostitele. K získání ID vyhrazeného hostitele můžete použít [příkaz AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) .

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

Můžete ověřit, jestli se konfigurace použila správně, nebo zjistit, jaká konfigurace se aktuálně používá pomocí `az maintenance assignment list`.

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

Pokud chcete zjistit, jestli čekají na aktualizace, použijte `az maintenance update list`. Aktualizace--předplatné jako ID pro předplatné, které obsahuje virtuální počítač.

Pokud nejsou k dispozici žádné aktualizace, příkaz vrátí chybovou zprávu, která bude obsahovat text: `Resource not found...StatusCode: 404`.

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

Chcete-li vyhledat nedokončené aktualizace pro vyhrazeného hostitele (ADH). V tomto příkladu je výstup formátovaný jako tabulka pro čitelnost. Nahraďte hodnoty pro prostředky vlastními.

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

Průběh aktualizací můžete zjistit pomocí `az maintenance applyupdate get`. 

Jako název aktualizace můžete použít `default` k zobrazení výsledků Poslední aktualizace nebo nahrazení `myUpdateName` názvem aktualizace, která byla vrácena při spuštění `az maintenance applyupdate create`.

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

K odstranění konfigurace údržby použijte `az maintenance configuration delete`. Odstranění konfigurace odebere z přidružených prostředků řízení údržby.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).
