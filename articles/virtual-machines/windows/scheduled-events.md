---
title: Naplánované události pro virtuální počítače s Windows v Azure | Dokumentace Microsoftu
description: Naplánované události pomocí služby Azure Metadata pro na virtuálních počítačích Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 9130bf5c2708f7eecf6fc1b5db2ffbb3c2fffc30
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201274"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Služby Azure Metadata: Naplánované události pro virtuální počítače s Windows

Naplánované události je služba Azure Metadata, která poskytuje vaše aplikace času na přípravu Údržba virtuálního počítače. Poskytuje informace o událostech nadcházející údržby (například restartování), může vaše aplikace připravit pro ně nebo omezit přerušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS ve Windows a Linuxu. 

Informace o službě Scheduled Events v Linuxu najdete v tématu [Scheduled Events pro virtuální počítače s Linuxem](../linux/scheduled-events.md).

> [!Note] 
> Naplánované události je obecně dostupná ve všech oblastech Azure. Zobrazit [verzí a dostupnost oblastí](#version-and-region-availability) pro informace o nejnovější verzi.

## <a name="why-scheduled-events"></a>Proč naplánované události?

Mnoho aplikací můžete těžit z času na přípravu pro údržbu virtuálního počítače. Čas je možné provádět konkrétní úlohy aplikace, které zlepšují dostupnost, spolehlivost a použitelnost včetně: 

- Kontrolní bod a obnovení
- Vyprázdnění připojení
- Převzetí služeb při selhání primární repliky 
- Odebrání z fondu nástroje pro vyrovnávání zatížení
- Protokolování událostí
- Řádné vypnutí 

Pomocí naplánovaných událostí aplikace můžete zjistit, kdy bude údržby dojít k a spouštění úloh a omezit jejich dopad. Povolení naplánované události poskytuje minimální množství času, před provedením aktivity údržby virtuální počítač. V části plánování událostí pod podrobnosti.

Naplánované události poskytuje události v následujících případech použití:
- Údržby iniciované platformy (například aktualizace operačního systému hostitele)
- Údržba (například uživatel restartuje nebo znovu nasadí virtuální počítač), kterou inicioval uživatel

## <a name="the-basics"></a>Základní informace  

Služby Azure Metadata zveřejňuje informace o spouštění virtuálních počítačů pomocí koncového bodu REST, která je přístupná v rámci virtuálního počítače. Informace jsou dostupné prostřednictvím nesměrovatelných IP tak, aby se nevystaví mimo virtuální počítač.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální síť virtuálních počítačů s povoleným, je k dispozici statickou IP adresu nesměrovatelných, služba metadat `169.254.169.254`. Úplné koncový bod pro nejnovější verzi naplánovaných událostí je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Pokud není vytvořený virtuální počítač ve virtuální síti, výchozí případy pro cloud services a klasické virtuální počítače, je další logiku potřebné ke zjišťování IP adres používat. Odkazovat na této ukázce se dozvíte postupy [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verze a dostupnost oblastí
Služba naplánované události se systémovou správou verzí. Verze jsou povinné a aktuální verze je `2017-08-01`.

| Verze | Typ verze | Oblasti | Poznámky k verzi | 
| - | - | - | - |
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Odebrat před podtržítka z názvy prostředků pro virtuální počítače IaaS<br><li>Hlavičku metadat požadavek vynucovat pro všechny požadavky | 
| 2017-03-01 | Preview | Vše |<li>Původní vydaná verze

> [!NOTE] 
> Předchozí verze preview naplánované události, které jsou podporované jako verze api-version {nejnovější}. Tento formát se už nepodporuje a v budoucnu budou zastaralé.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání naplánované události
Naplánované události je povolený pro vaše služby první čas, který může požádat o události. Měli byste očekávat zpožděné odpovědi v první volání až dvě minuty. Koncový bod pravidelně k detekci události nadcházející údržby, jakož i stav činnosti údržby, které jsou prováděny by dotazování.

Naplánované události je zakázaná pro vaši službu, pokud nepoužívá žádost po dobu 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Údržba virtuálního počítače prostřednictvím webu Azure portal, rozhraní API, rozhraní příkazového řádku, kterou inicioval uživatel nebo prostředí PowerShell výsledkem naplánované události. To můžete otestovat ve vaší aplikaci logiky přípravy údržby a umožňuje vaší aplikaci připravit uživatelem spuštěná Údržba.

Restartuje se virtuální počítač naplánuje událost s typem `Reboot`. Znovu se nasazuje virtuální počítač naplánuje událost s typem `Redeploy`.

## <a name="using-the-api"></a>Pomocí rozhraní API

### <a name="headers"></a>Hlavičky
Když odešlete dotaz na Metadata Service, je nutné zadat hlavičku `Metadata:true` zajistit požadavek nebyl přesměrován náhodně. `Metadata:true` Hlavičky je povinná pro všechny požadavky naplánovaných událostí. Chybná žádost odpověď ze služby Metadata způsobí selhání zahrnout hlavičku požadavku.

### <a name="query-for-events"></a>Dotaz pro události
Můžete zadat dotaz na Scheduled Events jednoduše tak, že následující volání:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Odpověď obsahuje celou řadu naplánovaných událostí. Prázdné pole znamená, že aktuálně neexistují žádné události naplánované.
V případě, kdy jsou naplánované události, odpověď obsahuje celou řadu událostí: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```
DocumentIncarnation je značka ETag a poskytuje snadný způsob, jak zkontrolovat, pokud od posledního dotazu změnil datové části události.

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| ID události | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Dopad, který způsobí, že se tato událost. <br><br> Hodnoty: <br><ul><li> `Freeze`: Virtuální počítač je naplánovaná pozastavit několik sekund. Procesor je pozastaveno, ale neexistuje žádný vliv na paměť, otevřené soubory nebo připojení k síti. <li>`Reboot`: Virtuální počítač je naplánovaná restartování (dojde ke ztrátě dočasné paměti). <li>`Redeploy`: Virtuální počítač je naplánovaná přesunout do jiného uzlu (dočasné disky jsou ztraceny). |
| ResourceType | Typ prostředku, který má vliv na tuto událost. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které má vliv na tuto událost. To je zaručeno, obsahují počítače maximálně jednu [aktualizační doména](manage-availability.md), ale nemusí obsahovat všechny počítače ve skupině UD. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stav události | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánované spuštění po dobu určenou v `NotBefore` vlastnost.<li>`Started`: Tato událost se spustila.</ul> Ne `Completed` nebo podobné stav je stále k dispozici; událost již nevrátí se po dokončení události.
| neplatí před| Doba, po jejímž uplynutí může začínat v této události. <br><br> Příklad: <br><ul><li> Pondělí, 19. září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování události
Každé události je naplánovaný minimální množství čas v budoucnosti podle typu události. Tentokrát se projeví v události `NotBefore` vlastnost. 

|Typ události  | Minimální oznámení |
| - | - |
| zablokování| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |

### <a name="event-scope"></a>Události oboru     
Naplánované události se doručují na:        
 - Všechny virtuální počítače v cloudové službě      
 - Všechny virtuální počítače ve skupině dostupnosti      
 - Všechny virtuální počítače ve skupině umístění Škálovací sady.         

Proto byste měli zkontrolovat `Resources` pole v události, chcete-li zjistit, jaké virtuální počítače budou mít vliv. 

### <a name="starting-an-event"></a>Počáteční událost 

Když jste se naučili nadcházející události a dokončení svoji logiku pro řádné vypnutí, tím, že můžete schválit nezpracovaných událostí `POST` volání metadata služby se `EventId`. To znamená do Azure, můžete zkrátit minimální oznámení čas (Pokud je to možné). 

Tady je ve formátu json v očekává `POST` text žádosti. Požadavek by měl obsahovat seznam `StartRequests`. Každý `StartRequest` obsahuje `EventId` chcete urychlit události:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Potvrdil událost umožňuje událostí, aby bylo možné pokračovat pro všechny `Resources` v případě, že, ne jenom virtuální počítač, který potvrzuje události. Proto můžete zvolit vedoucí instanci pro potvrzení, který může být stejně snadné jako prvního počítače v koordinaci `Resources` pole.


## <a name="powershell-sample"></a>Ukázka PowerShellu 

Následující ukázka dotazuje službu metadat pro naplánované události a následně schválí každé nevyřízené události.

```PowerShell
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

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

## <a name="next-steps"></a>Další postup 

- Sledování [naplánované události ukázka](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) v Azure Friday. 
- Projděte si ukázky kódu naplánovaných událostí [Azure Instance Metadata naplánované události úložiště GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Další informace o rozhraní API dostupná v [služba metadat Instance](instance-metadata-service.md).
- Další informace o [plánované údržby pro virtuální počítače Windows v Azure](planned-maintenance.md).
