---
title: Naplánované události pro virtuální počítače s Windows v Azure
description: Naplánované události využívající službu Metadata Azure pro virtuální počítače s Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: c1e9ef8de65912c4f33e17ee2bb2175c76e7ea07
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258674"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Služba metadat Azure: Naplánované události pro virtuální počítače s Windows

Naplánované události je služba metadat Azure, která poskytuje vaší aplikaci čas na přípravu na údržbu virtuálního počítače. Poskytuje informace o nadcházejících událostech údržby (např. restartování), takže se na ně aplikace může připravit a omezit narušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS na Windows i Linux. 

Informace o naplánovaných událostech na Linuxu najdete [v tématu Plánované události pro virtuální počítače s Linuxem](../linux/scheduled-events.md).

> [!Note] 
> Naplánované události jsou obecně dostupné ve všech oblastech Azure. Nejnovější informace o verzi najdete v [tématu Verze a dostupnost oblastí.](#version-and-region-availability)

## <a name="why-scheduled-events"></a>Proč plánované události?

Mnoho aplikací může těžit z času připravit se na údržbu virtuálního počítače. Čas lze použít k provádění úloh specifických pro aplikaci, které zlepšují dostupnost, spolehlivost a provozuschopnost, včetně: 

- Kontrolní bod a obnovení
- Vyprázdnění připojení
- Převzetí služeb při selhání primární repliky 
- Odebrání z fondu vyrovnávání zatížení
- Protokolování událostí
- Bezproblémové vypnutí 

Pomocí naplánovaných událostí může aplikace zjistit, kdy dojde k údržbě, a aktivovat úlohy, které omezí její dopad. Povolení naplánovaných událostí poskytuje virtuálnímu počítači minimální dobu před provedením aktivity údržby. Podrobnosti najdete v části Plánování událostí níže.

Naplánované události poskytují události v následujících případech použití:
- [Údržba iniciovaná platformou](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (například restartování virtuálního počítače, migrace za provozu nebo aktualizace zachování paměti pro hostitele)
- Virtuální počítač běží na [zhoršené hostitelském hardwaru,](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) u kterého se předpokládá, že brzy selže
- Údržba iniciovaná uživatelem (například uživatel restartuje nebo znovu nasadí virtuální počítač)
- Vyřazování instance sady [virtuálních](spot-vms.md) virtuálních počítačů a [škálování bodů](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Základy  

Služba Metadata Azure poskytuje informace o spuštění virtuálních počítačů pomocí koncového bodu REST přístupného z virtuálního počítače. Informace jsou k dispozici prostřednictvím nesměrovatelné IP adresy, takže nejsou vystaveny mimo virtuální hod.

### <a name="endpoint-discovery"></a>Zjišťování koncového bodu
Pro virtuální chod s povoleno virtuální sítě je služba metadat k `169.254.169.254`dispozici ze statické nesměrovatelné IP adresy . Úplný koncový bod pro nejnovější verzi naplánovaných událostí je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Pokud virtuální počítač není vytvořen v rámci virtuální sítě, výchozí případy pro cloudové služby a klasické virtuální počítače, další logika je nutné zjistit IP adresu, kterou chcete použít. V této ukázce se dozvíte, jak [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Dostupnost verze a oblasti
Služba naplánované události je verzí. Verze jsou povinné a aktuální `2019-01-01`verze je .

| Version | Typ vydání | Oblasti | Poznámky k verzi | 
| - | - | - | - |
| 2019-01-01 | Obecná dostupnost | Všechny | <li> Přidána podpora škálovacích sad virtuálních strojů EventType 'Terminate' |
| 2017-11-01 | Obecná dostupnost | Všechny | <li> Přidána podpora pro vyřazovací eventtype spotového virtuálního počítači 'Preempt'<br> | 
| 2017-08-01 | Obecná dostupnost | Všechny | <li> Odebrané předřazené podtržítko z názvů prostředků pro virtuální ms IaaS<br><li>Požadavek hlavičky metadat vynucený pro všechny požadavky | 
| 2017-03-01 | Preview | Všechny |<li>Původní vydaná verze |

> [!NOTE] 
> Předchozí verze preview naplánovaných událostí podporovaných {latest} jako verze rozhraní API. Tento formát již není podporován a v budoucnu bude zastaralá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání plánovaných událostí
Naplánované události jsou pro vaši službu povoleny při prvním vytvoření žádosti o události. Měli byste očekávat opožděnou odpověď při prvním volání až dvě minuty. Měli byste dotaz na koncový bod pravidelně zjistit nadcházející události údržby, jakož i stav činnosti údržby, které jsou prováděny.

Plánované události jsou pro vaši službu zakázány, pokud nepodá požadavek na 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Uživateli iniciovaná údržba virtuálních strojů prostřednictvím portálu Azure Portal, rozhraní API, rozhraní API nebo prostředí PowerShell má za následek naplánovanou událost. To umožňuje otestovat logiku přípravy údržby ve vaší aplikaci a umožňuje vaší aplikaci připravit se na údržbu iniciotě uživatelem.

Restartováním virtuálního počítače naplánujete `Reboot`událost s typem . Opětovné nasazení virtuálního počítače naplánuje `Redeploy`událost s typem .

## <a name="using-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Hlavičky
Při dotazování služby metadat, je `Metadata:true` nutné zadat záhlaví zajistit požadavek nebyl neúmyslně přesměrován. Záhlaví `Metadata:true` je vyžadováno pro všechny naplánované požadavky na události. Pokud nezahrnete záhlaví v požadavku, bude mít za následek odpověď chybného požadavku ze služby metadat.

### <a name="query-for-events"></a>Dotaz na události
Můžete dotaz na plánované události jednoduše provedením následujícího hovoru:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že jsou aktuálně naplánovány žádné události.
V případě, že jsou naplánované události, odpověď obsahuje pole událostí: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentInkarnace je eTag a poskytuje snadný způsob, jak zkontrolovat, pokud události datové části se změnila od posledního dotazu.

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| Eventid | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Vliv na tuto událost způsobí. <br><br> Hodnoty: <br><ul><li> `Freeze`: Virtuální počítač je naplánováno pozastavit na několik sekund. Připojení procesoru a sítě může být pozastaveno, ale nemá žádný vliv na paměť nebo otevřené soubory. <li>`Reboot`: Virtuální počítač je naplánováno restartování (netrvalá paměť je ztracena). <li>`Redeploy`: Virtuální počítač je naplánováno přesunout do jiného uzlu (dočasné disky jsou ztraceny). <li>`Preempt`: Virtuální stroj Spot se odstraňuje (dočasné disky jsou ztraceny). <li> `Terminate`: Virtuální počítač je naplánováno odstranění. |
| ResourceType | Typ prostředku, na který má tato událost vliv. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam zdrojů, které tato událost ovlivňuje. To je zaručeno, že obsahují počítače z maximálně jedné [domény aktualizace](manage-availability.md), ale nemusí obsahovat všechny počítače v UD. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stav události | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánováno spustit po `NotBefore` čase určeném ve vlastnosti.<li>`Started`: Tato událost byla zahájena.</ul> Není `Completed` nikdy uveden žádný nebo podobný status; událost již nebude vrácena po dokončení události.
| Není před| Doba, po jejímž uplynutí může tato událost začít. <br><br> Příklad: <br><ul><li> Po, 19 Zář 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každá událost je naplánována minimální množství času v budoucnu na základě typu události. Tentokrát se odráží ve `NotBefore` vlastnosti události. 

|Typ události  | Minimální oznámení |
| - | - |
| Zmrazit| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |
| Preempt | 30 sekund |
| Terminate | [Uživatelsky konfigurovatelné:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 až 15 minut |

> [!NOTE] 
> V některých případech azure je schopen předpovědět selhání hostitele z důvodu degradovaného hardwaru a pokusí se zmírnit narušení vaší služby plánováním migrace. Ovlivněné virtuální počítače obdrží naplánovanou událost s, `NotBefore` která je obvykle několik dní v budoucnosti. Skutečný čas se liší v závislosti na předpokládaném posouzení rizika selhání. Azure se pokusí dát 7 dní předem oznámení, pokud je to možné, ale skutečný čas se liší a může být menší, pokud předpověď je, že je vysoká pravděpodobnost selhání hardwaru bezprostředně. Chcete-li minimalizovat riziko pro vaši službu v případě, že hardware selže před migrací systému, doporučujeme co nejdříve znovu nasadit virtuální počítač.

### <a name="event-scope"></a>Obor události     
Plánované události jsou doručovány do:
 - Samostatné virtuální počítače
 - Všechny virtuální počítače v cloudové službě      
 - Všechny virtuální počítače v sadě dostupnosti      
 - Všechny virtuální počítače ve skupině umístění škálovací sady.         

V důsledku toho byste `Resources` měli zkontrolovat pole v události k identifikaci, které virtuální chod y budou ovlivněny. 

### <a name="starting-an-event"></a>Zahájení události 

Jakmile jste se dozvěděli o nadcházející události a dokončili svou logiku pro `POST` řádné vypnutí, můžete `EventId`schválit nevyřízené události voláním služby metadat s . To znamená, že Azure, že můžete zkrátit minimální dobu oznámení (pokud je to možné). 

Následuje json očekávaný v `POST` těle požadavku. Požadavek by měl `StartRequests`obsahovat seznam . Každý `StartRequest` obsahuje `EventId` událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potvrzení události umožňuje událost pokračovat pro `Resources` všechny v události, nikoli pouze virtuální počítač, který událost uznává. Můžete se proto rozhodnout zvolit vedoucího, který bude koordinovat potvrzení, `Resources` které může být stejně jednoduché jako první stroj v terénu.


## <a name="powershell-sample"></a>Ukázka PowerShellu 

Následující ukázka se dotazuje služby metadat pro naplánované události a schvaluje každou nevyřízené události.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Další kroky 

- Podívejte se na [ukázku naplánovaných událostí](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) v Azure pátek. 
- Kontrola ukázek kódu naplánovaných událostí v [úložišti GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm) s metadaty metadat instance Azure
- Přečtěte si další informace o rozhraních API dostupných ve [službě Metadata instance](instance-metadata-service.md).
- Přečtěte si o [plánované údržbě virtuálních počítačů s Windows v Azure](planned-maintenance.md).
