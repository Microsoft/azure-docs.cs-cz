---
title: Oznámení o ukončení pro instance škálovací sady virtuálních počítačů Azure
description: Naučte se, jak povolit koncová oznámení pro instance sady škálování virtuálních počítačů Azure.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: c4d6de1b3406e6d82bdac5ff9b5c72a2286da988
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747745"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Oznámení o ukončení pro instance škálovací sady virtuálních počítačů Azure
Instance sady škálování můžou vyjádřit výslovný souhlas s přijetím oznámení o ukončení instance a nastavením předem definovaného časového limitu prodlevy na operaci ukončení. Oznámení ukončení se odesílá prostřednictvím služby Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md), která poskytuje oznámení a zpoždění ovlivněných operací, jako je třeba restartování a opětovné nasazení. Řešení přidá další událost – ukončit – do seznamu Scheduled Events a přidružená prodleva události ukončení bude záviset na limitu zpoždění zadaného uživateli ve svých konfiguracích modelu sady škálování.

Po zaregistrování do funkce nemusí instance sady škálování čekat na vypršení platnosti zadaného časového limitu, než se instance odstraní. Po přijetí oznámení o ukončení se může instance kdykoli odstranit, a to až do vypršení časového limitu ukončení.

## <a name="enable-terminate-notifications"></a>Povolit ukončení oznámení
Existuje několik způsobů, jak povolit koncová oznámení na instancích sady škálování, jak je popsáno v následujících příkladech.

### <a name="azure-portal"></a>portál Azure

Následující kroky umožňují ukončit oznámení při vytváření nové sady škálování. 

1. Přejít na **Virtual Machine Scale Sets**.
1. Vyberte **+ Přidat** a vytvořte novou sadu škálování.
1. Přejít na kartu **Správa** . 
1. Vyhledejte část **ukončení instance** .
1. V případě **oznámení o ukončení instance** vyberte **zapnuto**.
1. Pro **zpoždění ukončení (minuty)** nastavte požadovaný výchozí časový limit.
1. Až budete hotovi s vytvářením nové sady škálování, vyberte tlačítko **zkontrolovat + vytvořit** . 

> [!NOTE]
> Pro existující sady škálování v Azure Portal nemůžete nastavit oznámení o ukončení.

### <a name="rest-api"></a>REST API

Následující příklad umožňuje ukončení oznámení na modelu sady škálování.

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

Výše uvedený blok Určuje prodlevu s časovým limitem 5 minut (jak je uvedeno v *PT5M*) pro všechny operace ukončení u všech instancí ve vaší sadě škálování. Pole *notBeforeTimeout* může ve formátu ISO 8601 trvat od 5 do 15 minut libovolnou hodnotu. Výchozí časový limit pro operaci ukončení můžete změnit úpravou vlastnosti *notBeforeTimeout* v části *terminateNotificationProfile* popsané výše.

Po povolení *scheduledEventsProfile* v modelu sady škálování a nastavení *notBeforeTimeout* aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby se změny projevily.

> [!NOTE]
>Oznámení o ukončení u instancí sady škálování je možné povolit jenom pomocí rozhraní API verze 2019-03-01 a vyšší.

### <a name="azure-powershell"></a>Azure PowerShell
Při vytváření nové sady škálování můžete v sadě škálování povolit oznámení o ukončení pomocí rutiny [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) .

Tento vzorový skript vás provede vytvořením sady škálování a přidružených prostředků pomocí konfiguračního souboru: [vytvořte úplnou sadu škálování virtuálního počítače](./scripts/powershell-sample-create-complete-scale-set.md). Můžete poskytnout oznámení o ukončení konfigurace přidáním parametrů *TerminateScheduledEvents* a *TerminateScheduledEventNotBeforeTimeoutInMinutes* do objektu konfigurace pro vytvoření sady škálování. Následující příklad povoluje funkci s časovým limitem zpoždění 10 minut.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Pomocí rutiny [Update-AzVmss](/powershell/module/az.compute/update-azvmss) povolte koncová oznámení v existující sadě škálování.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Výše uvedený příklad umožňuje ukončit oznámení v existující sadě škálování a nastavit časový limit 15 minut pro událost ukončení.

Po povolení plánovaných událostí v modelu sady škálování a nastavení časového limitu aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby se změny projevily.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následující příklad slouží k povolení oznámení ukončení při vytváření nové sady škálování.

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

Výše uvedený příklad vytvoří skupinu prostředků a pak vytvoří novou sadu škálování s povolenými oznámeními ukončení pro výchozí časový limit 10 minut.

Následující příklad slouží k povolení oznámení ukončení v existující sadě škálování.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Získat oznámení o ukončení

Oznámení o ukončení se doručují prostřednictvím [Scheduled Events](../virtual-machines/windows/scheduled-events.md), což je Azure metadata Service. Služba Azure metadata Service zpřístupňuje informace o spouštění Virtual Machines pomocí koncového bodu REST přístupného v rámci virtuálního počítače. Tyto informace jsou k dispozici prostřednictvím IP adresy bez směrování, takže nejsou vystaveny mimo virtuální počítač.

Scheduled Events je povolená pro sadu škálování při prvním vytvoření žádosti o události. Můžete očekávat opožděnou odpověď v prvním volání až do dvou minut. Pravidelné dotazování koncového bodu pro detekci nadcházejících událostí údržby a stavu probíhajících aktivit údržby

Pokud instance sady škálování nevytváří požadavek na 24 hodin, je Scheduled Events pro sadu škálování zakázané.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální počítače s povolenými VIRTUÁLNÍmi sítěmi je Metadata Service k dispozici ze statické IP adresy, která není směrovatelný, 169.254.169.254.

Úplný koncový bod pro nejnovější verzi Scheduled Events je:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Odpověď na dotaz
Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že aktuálně nejsou naplánovány žádné události.

V případě naplánovaných událostí obsahuje odpověď pole událostí. V případě události "ukončit" bude odpověď vypadat takto:
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
*DocumentIncarnation* je ETag a poskytuje snadný způsob, jak zkontrolovat, jestli se od posledního dotazu změnila datová část událostí.

Další informace o jednotlivých polích výše naleznete v dokumentaci Scheduled Events pro [systémy Windows](../virtual-machines/windows/scheduled-events.md#event-properties) a [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Reakce na události
Jakmile se seznámíte s nadcházející událostí a dokončíte logiku pro řádné vypnutí, můžete tuto událost schválit voláním POST služby metadat pomocí ID události. Volání POST indikuje službě Azure, že může pokračovat v odstranění virtuálního počítače.

Níže je v textu požadavku POST očekáván kód JSON. Požadavek by měl obsahovat seznam StartRequests. Jednotlivé StartRequest obsahují ID události pro událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Zajistěte, aby každý virtuální počítač v sadě škálování schválil jenom ID události relevantní pouze pro tento virtuální počítač. VIRTUÁLNÍ počítač může získat vlastní název virtuálního počítače [prostřednictvím metadat instance](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Tento název má podobu "{Scale-set-Name} _ {instance-ID}" a zobrazí se v části "prostředky" odpovědi na dotaz popsané výše.

Můžete se také podívat na ukázky skriptů pro dotazování a reakci na události [Pythonu](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy
-   Ukončit oznámení pouze na operacích DELETE – všechny operace odstranění (ruční odstranění nebo škálování iniciované automaticky v měřítku) vygenerují události ukončení, pokud je vaše sada škálování zapnutá *scheduledEventsProfile* . Jiné operace, jako je restartování, obnovení bitové kopie, opětovné nasazení a zastavení nebo zrušení přidělení, negenerují události ukončení. Pro virtuální počítače s nízkou prioritou nejde povolit oznámení ukončení.
-   Žádné povinné čekání na časový limit – operaci ukončení můžete spustit kdykoli po přijetí události a vypršení doby *NotBefore* události.
-   Povinné odstranění v časovém limitu – neexistují žádné možnosti rozšíření hodnoty časového limitu po vygenerování události. Po vypršení časového limitu se zpracuje událost ukončení, která čeká na dokončení, a virtuální počítač se odstraní.
-   Upravitelná hodnota časového limitu – hodnotu časového limitu můžete změnit kdykoli před odstraněním instance, úpravou vlastnosti *notBeforeTimeout* v modelu sady škálování a aktualizací instancí virtuálních počítačů na nejnovější model.
-   Schválit všechna nevyřízená odstranění – Pokud dojde k neschválenému odstranění na VM_1, která není schválená a jste schválili další událost ukončení na VM_2, VM_2 se neodstraní, dokud není schválená událost ukončení pro VM_1 nebo uplynul časový limit. Po schválení události ukončení pro VM_1 se odstraní oba VM_1 a VM_2.
-   Schválení všech současných odstranění – rozšířením výše uvedeného příkladu, pokud VM_1 a VM_2 mají stejný čas *NotBefore* , musí být obě události ukončení schválené nebo se žádný virtuální počítač neodstranil před vypršením časového limitu.

## <a name="troubleshoot"></a>Řešení potíží
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nepovedlo se povolit scheduledEventsProfile.
Pokud se zobrazí chyba "důvodu chybného požadavku" s chybovou zprávou "nebylo možné najít člena" scheduledEventsProfile "u objektu typu" VirtualMachineProfile ", ověřte verzi rozhraní API použitou pro operace sady škálování. Je vyžadováno rozhraní COMPUTE API verze **2019-03-01** nebo vyšší. 

### <a name="failure-to-get-terminate-events"></a>Nepovedlo se získat události ukončení.
Pokud nezískáváte žádné události **ukončení** prostřednictvím Scheduled Events, zkontrolujte verzi rozhraní API, která se používá pro získání událostí. Pro události ukončení se vyžaduje Metadata Service rozhraní API verze **2019-01-01** nebo vyšší.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Získání události ukončení s nesprávným NotBefore časem  
Po povolení *scheduledEventsProfile* v modelu sady škálování a nastavení *notBeforeTimeout* aktualizujte jednotlivé instance na [nejnovější model](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby se změny projevily.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [nasadit vaši aplikaci do služby](virtual-machine-scale-sets-deploy-app.md) Virtual Machine Scale Sets.
