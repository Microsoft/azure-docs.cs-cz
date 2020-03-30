---
title: Ukončení oznámení pro instance škálovací sady virtuálních strojů Azure
description: Zjistěte, jak povolit oznámení o ukončení pro instance škálovací sady virtuálních strojů Azure
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250748"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Ukončení oznámení pro instance škálovací sady virtuálních strojů Azure
Instance škálovací sady se mohou přihlásit k přijímání oznámení o ukončení instance a nastavit předdefinovaný časový plán zpoždění pro operaci ukončení. Oznámení o ukončení se odesílá prostřednictvím služby Metadata Azure – [naplánované události](../virtual-machines/windows/scheduled-events.md), která poskytuje oznámení a zpoždění působivých operací, jako je restartování a opětovné nasazení. Řešení přidá další událost – Ukončit – do seznamu naplánované události a přidružené zpoždění události ukončení bude záviset na limitu zpoždění, jak je určeno uživateli v jejich konfigurace modelu škálovací sady.

Po registraci do funkce nemusí instance škálovací sady čekat, až vyprší zadaný časový limit, než bude instance odstraněna. Po obdržení oznámení ukončit instance může zvolit, které mají být odstraněny kdykoli před vypršením časového limitu ukončení.

## <a name="enable-terminate-notifications"></a>Povolit ukončit oznámení
Existuje několik způsobů, jak povolit oznámení o ukončení v instancích škálovací sady, jak je podrobně popsáno v následujících příkladech.

### <a name="azure-portal"></a>portál Azure

Následující kroky umožňují ukončit oznámení při vytváření nové škálovací sady. 

1. Přejděte na **Škálovací sady virtuálních strojů**.
1. Výběrem **možnosti + Přidat** vytvoříte novou škálovací sadu.
1. Přejděte na kartu **Správa.** 
1. Vyhledejte část **Ukončení instance.**
1. V **případě oznámení o ukončení instance**vyberte možnost **Zapnuto**.
1. Pro **zpoždění ukončení (minuty)** nastavte požadovaný výchozí časový limit.
1. Po vytvoření nové škálovací sady vyberte **Tlačítko Revize + vytvořit.** 

> [!NOTE]
> Nemůžete nastavit oznámení o ukončení na existujících škálovacích sadách na webu Azure Portal.

### <a name="rest-api"></a>REST API

Následující příklad umožňuje ukončit oznámení na modelu škálovací sady.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Výše uvedený blok určuje časové zpoždění 5 minut (jak je uvedeno *v PT5M)* pro všechny operace ukončení ve všech instancích ve vaší škálovací sadě. Pole *notBeforeTimeout* může trvat libovolnou hodnotu mezi 5 a 15 minut ve formátu ISO 8601. Výchozí časový limit pro operaci ukončení můžete změnit úpravou vlastnosti *notBeforeTimeout* v části *terminateNotificationProfile* popsané výše.

Po povolení *scheduledEventsProfile* na modelu škálovací sady a nastavení *notBeforeTimeout*aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) tak, aby odrážely změny.

> [!NOTE]
>Ukončit oznámení v instancích škálovací sady lze povolit pouze pomocí rozhraní API verze 2019-03-01 a vyšší

### <a name="azure-powershell"></a>Azure PowerShell
Při vytváření nové škálovací sady můžete povolit oznámení o ukončení v škálovací sadě pomocí rutiny [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Tento ukázkový skript provede vytvoření škálovací sady a přidružených prostředků pomocí konfiguračního souboru: [Vytvořte kompletní škálovací sadu virtuálních počítačů](./scripts/powershell-sample-create-complete-scale-set.md). Můžete zadat konfigurovat oznámení o ukončení přidáním parametrů *TerminateScheduledEvents* a *TerminateScheduledEventNotBeforeTimeoutInMinutes* do objektu konfigurace pro vytvoření škálovací sady. Následující příklad umožňuje funkci s časovým časem zpoždění 10 minut.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Pomocí rutiny [Aktualizace AzVmss](/powershell/module/az.compute/update-azvmss) povolte oznámení o ukončení v existující škálovací sadě.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Výše uvedený příklad umožňuje ukončit oznámení na existující škálovací sadu a nastaví časový čas 15 minut pro událost ukončení.

Po povolení naplánovaných událostí v modelu škálovací sady a nastavení časového času aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) tak, aby odrážely změny.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následující příklad je pro povolení oznámení o ukončení při vytváření nové škálovací sady.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

Výše uvedený příklad nejprve vytvoří skupinu prostředků a potom vytvoří novou škálovací sadu s povolenými oznámeními o ukončení pro 10minutový výchozí časový limit.

Následující příklad je pro povolení oznámení o ukončení v existující škálovací sadě.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Získat oznámení o ukončení

Ukončit oznámení se doručují prostřednictvím [naplánovaných událostí](../virtual-machines/windows/scheduled-events.md), což je služba metadat Azure. Služba Metadata Azure poskytuje informace o spuštění virtuálních počítačů pomocí koncového bodu REST přístupného z virtuálního počítače. Informace jsou k dispozici prostřednictvím nesměrovatelné IP tak, aby nebyly vystaveny mimo virtuální hod.

Naplánované události jsou povoleny pro sadu škálování při prvním vytvoření žádosti o události. Můžete očekávat opožděnou odpověď při prvním volání až dvě minuty. Pravidelně se dotazujte na koncový bod, abyste zjistili nadcházející události údržby a stav probíhajících aktivit údržby.

Plánované události jsou pro škálovací sadu zakázány, pokud instance škálovací sady nepožádají o 24 hodin.

### <a name="endpoint-discovery"></a>Zjišťování koncového bodu
Pro virtuální sítě povolené virtuální chod, služba metadat je k dispozici ze statické nesměrovatelné IP, 169.254.169.254.

Úplný koncový bod pro nejnovější verzi naplánovaných událostí je:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Odpověď na dotaz
Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že jsou aktuálně naplánovány žádné události.

V případě, že jsou naplánované události, odpověď obsahuje pole událostí. U události "Ukončit" bude odpověď vypadat takto:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentInkarnace* je eTag a poskytuje snadný způsob, jak zkontrolovat, pokud události datové části se změnila od posledního dotazu.

Další informace o jednotlivých polích výše naleznete v dokumentaci k naplánovaným událostem pro [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) a [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Reagovat na události
Jakmile se dozvíte o nadcházející události a dokončíte svou logiku pro řádné vypnutí, můžete schválit nevyřízené události tím, že post volání služby metadat s EventId. Volání POST označuje Azure, že může pokračovat s odstraněním virtuálního počítače.

Níže je json očekává v těle požadavku POST. Požadavek by měl obsahovat seznam StartRequests. Každý StartRequest obsahuje EventId pro událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Ujistěte se, že každý virtuální virtuální mit ve škálovací sadě schvaluje jenom EventID relevantní pro tento virtuální virtuální ms. Virtuální modul může získat svůj vlastní název virtuálního virtuálního virtuálního serveru [prostřednictvím metadat instance](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Tento název má podobu "{scale-set-name}_{instance-id}" a zobrazí se v části Zdroje popsané výše.

Můžete také odkazovat na ukázky skriptů pro dotazování a odpovídání na události pomocí [PowerShellu](../virtual-machines/windows/scheduled-events.md#powershell-sample) a [Pythonu](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy
-   Ukončit oznámení pouze na 'odstranit' operace – Všechny operace odstranění (ruční odstranění nebo automatické škálování iniciované scale-in) bude generovat Ukončit události, pokud vaše škálovací sada má *scheduledEventsProfile* povoleno. Jiné operace, jako je restartování, reimage, znovu nasadit a stop/navrátit negenerují Terminate události. Ukončit oznámení nelze povolit pro virtuální chody s nízkou prioritou.
-   Žádné povinné čekání na časový limit – operaci ukončení můžete spustit kdykoli po přijetí události a před vypršením časového limitu *NotBefore* události.
-   Povinné odstranění v časovém odnož – neexistuje žádná možnost rozšíření hodnoty časového času po vygenerování události. Po vypršení časového limitu se zpracuje událost čekající na ukončení a virtuální ho se odstraní.
-   Modifikovatelné timeout hodnota – můžete upravit hodnotu časového limitu kdykoli před odstraněním instance úpravou *notBeforeTimeout* vlastnost na škálovací sadu modelu a aktualizace instancí virtuálního počítače na nejnovější model.
-   Schválit všechny čekající odstranění – Pokud je v VM_1 čekající odstranění, které není schváleno, a schválili jste jinou událost ukončení v VM_2, VM_2 se neodstraní, dokud nebude událost ukončení pro VM_1 schválena nebo dokud neuplyne časový limit. Jakmile schválíte událost ukončení pro VM_1, budou odstraněny VM_1 i VM_2.
-   Schválit všechny souběžné odstranění – Rozšíření výše uvedeného příkladu, pokud VM_1 a VM_2 mají stejný *NotBefore* čas, pak obě události ukončení musí být schváleny nebo ani virtuální hod je odstraněn před vypršením časového limitu.

## <a name="troubleshoot"></a>Řešení potíží
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nepodařilo se povolit scheduledEventsProfile.
Pokud se zobrazí chyba BadRequest s chybovou zprávou "Nelze najít člena 'scheduledEventsProfile' na objekt typu VirtualMachineProfile", zkontrolujte verzi rozhraní API použitou pro operace škálovací sady. Je vyžadováno rozhraní API pro výpočetní prostředí **2019-03-01** nebo vyšší. 

### <a name="failure-to-get-terminate-events"></a>Nepodařilo se získat ukončit události
Pokud nezískáváte žádné **ukončit** události prostřednictvím naplánovaných událostí, zkontrolujte verzi rozhraní API použitou pro získání událostí. Metadata Service API verze **2019-01-01** nebo vyšší je vyžadována pro terminate události.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Získání ukončit událost s nesprávnou NotBefore čas  
Po povolení *scheduledEventsProfile* na modelu škálovací sady a nastavení *notBeforeTimeout*aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) tak, aby odrážely změny.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [nasadit aplikaci](virtual-machine-scale-sets-deploy-app.md) na škálovací sady virtuálních strojů.
