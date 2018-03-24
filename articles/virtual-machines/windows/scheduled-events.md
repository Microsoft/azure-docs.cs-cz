---
title: Naplánované události pro virtuální počítače Windows v Azure | Microsoft Docs
description: Naplánované události pomocí služby Azure Metadata pro na virtuálních počítačích s Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: timlt
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
ms.openlocfilehash: 4740f3f189746a9ea0956d832678858b0528311e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Služba Azure Metadata: Naplánované události pro virtuální počítače Windows

Naplánované události je služba Azure Metadata, která dává vaší aplikace času Příprava pro virtuální počítač údržby. Poskytuje informace o události nadcházející údržby (například restartování) tak, aby vaše aplikace můžete připravit pro ně a omezit přerušení. Je k dispozici pro všechny typy virtuálního počítače Azure, včetně PaaS a IaaS v systému Windows a Linux. 

Informace o naplánované události v systému Linux najdete v tématu [naplánované události pro virtuální počítače s Linuxem](../linux/scheduled-events.md).

> [!Note] 
> Naplánované události je všeobecně dostupná v všech oblastech Azure. V tématu [verze a dostupnost v oblastech](#version-and-region-availability) pro nejnovější informace o verzi.

## <a name="why-scheduled-events"></a>Proč naplánované události?

Mnoho aplikací můžete těžit z času na přípravu údržby virtuálního počítače. Čas slouží k provádění specifických úloh aplikace, které zlepšení dostupnosti, spolehlivosti a použitelnost včetně: 

- Kontrolní bod a obnovení
- Připojení vyprazdňování
- Převzetí služeb při selhání primární repliky 
- Odebrání z fondu vyrovnávání zatížení
- Protokolování událostí
- Řádné vypnutí 

Pomocí naplánované události aplikace může zjistit, kdy bude údržby dojít a aktivuje úlohy omezit její vliv.  

Naplánované události poskytuje události v následujících případech použití:
- Platforma iniciované údržby (např. hostitele operačního systému aktualizace)
- Uživatel spustil údržby (například uživatele restartuje nebo opětovně nasadí virtuální počítač)

## <a name="the-basics"></a>Základní informace  

Služba Azure Metadata zpřístupní informace o spouštění virtuálních počítačů pomocí koncový bod REST, která je přístupná z virtuálního počítače. Informace k dispozici prostřednictvím směrovat IP, takže není nezveřejní virtuálního počítače.

### <a name="endpoint-discovery"></a>Koncový bod zjišťování
Pro virtuální síť povoleno virtuální počítače, služby metadat je k dispozici ze statické IP adresy směrovat, `169.254.169.254`. Úplné koncový bod pro nejnovější verzi naplánované události je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Pokud virtuální počítač není vytvořen v rámci virtuální sítě, výchozí případů pro cloudové služby a klasické virtuální počítače, je potřeba další logiku zjistit adresu IP. Najdete v této ukázce další postup [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Verze a dostupnost v oblastech
Služba naplánované události je verzí. Verze jsou povinné a aktuální verze je `2017-08-01`.

| Verze | Typ verze | Oblasti | Poznámky k verzi | 
| - | - | - | - |
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Přidá jako předpona podtržítka odeberou názvy prostředků pro virtuální počítače Iaas<br><li>Metadata hlavičky požadavku vynucuje pro všechny požadavky | 
| 2017-03-01 | Preview | Vše |<li>Původní vydaná verze

> [!NOTE] 
> Předchozí verze preview naplánované událostí podporovaných {nejnovější} jako verze rozhraní api. Tento formát se už nepodporuje a bude v budoucnu zastaralá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání naplánované události
Naplánované události je povolený pro vaše služba první době může požádat o události. Zpožděné odpověď by měl očekávat při první volání až dvě minuty.

Naplánované události je zakázána pro vaši službu, pokud není ho odešlete požadavek na 24 hodin.

### <a name="user-initiated-maintenance"></a>Uživatel spustil údržby
Uživatel spustil údržby virtuálního počítače prostřednictvím portálu Azure, rozhraní API, rozhraní příkazového řádku, nebo prostředí PowerShell, které jsou výsledkem plánovaná událost. To umožňuje otestovat logiku přípravy údržby v aplikaci a umožňuje aplikaci připravit pro údržbu inicializované uživatelem.

Restartování virtuálního počítače plány událost s typem `Reboot`. Opětovné nasazení virtuálního počítače plány událost s typem `Redeploy`.

## <a name="using-the-api"></a>Pomocí rozhraní API

### <a name="headers"></a>Záhlaví
Při dotazu Metadata služby, je nutné zadat hlavičku `Metadata:true` zajistit požadavek nebyl přesměrován náhodně. `Metadata:true` Záhlaví je povinný u všech požadavků naplánované události. Chybný požadavek odpověď ze služby metadat způsobí selhání zahrnout hlavičky v požadavku.

### <a name="query-for-events"></a>Dotaz pro události
Jednoduše tak, že toto volání se můžete dotazovat pro naplánované události:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Odpověď obsahuje pole naplánované události. Prázdné pole znamená, že aktuálně neexistují žádné události naplánované.
V případě, kde je naplánované události, odpověď obsahuje řadu událostí: 
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

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| ID události | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Dopad, který způsobí, že tato událost. <br><br> Hodnoty: <br><ul><li> `Freeze`: Oprava virtuální počítač se pozastavit několik sekund. Procesor je pozastavená, ale neexistuje žádný vliv na paměti, otevřených souborů nebo připojení k síti. <li>`Reboot`: Restartování je naplánováno virtuálního počítače (dočasnou paměti dojde ke ztrátě). <li>`Redeploy`: Je naplánován virtuální počítač přesunout do jiného uzlu (dočasné disky jsou ztraceny). |
| ResourceType | Typ prostředku, který má dopad na tuto událost. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které má dopad na tuto událost. Představuje záruku obsahovat maximálně jeden počítače [aktualizace domény](manage-availability.md), ale nemusí obsahovat všechny počítače ve UD. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stav události. | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánováno spuštění po dobu uvedenou v `NotBefore` vlastnost.<li>`Started`: Tato událost byla spuštěna.</ul> Ne `Completed` nebo se někdy poskytuje podobné stav, události se nelze vrátit už po dokončení události.
| Neplatí před| Doba, po jejímž uplynutí může spustit tuto událost. <br><br> Příklad: <br><ul><li> MON 19 září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každá událost je naplánováno minimální množství času v budoucnu podle typu události. Tentokrát se odrazí v události `NotBefore` vlastnost. 

|EventType  | Minimální oznámení |
| - | - |
| Zablokování| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |

### <a name="event-scope"></a>Události oboru     
Naplánované události budou doručeny do:        
 - Všechny virtuální počítače v cloudové službě      
 - Všechny virtuální počítače v nastavení dostupnosti      
 - Všechny virtuální počítače ve skupině umístění sady škálování.         

V důsledku toho byste měli zkontrolovat `Resources` pole v události zjistit, jaké virtuální počítače budou mít vliv. 

### <a name="starting-an-event"></a>Událost spuštění 

Jakmile jste se naučili nadcházející události a dokončit logika pro řádné vypnutí, můžete schválit nevyřízené události tak, že `POST` volání na metadata služby s `EventId`. To znamená do Azure, aby se zkrátil minimální oznámení čas (Pokud je to možné). 

Toto je očekávána v kódu json `POST` text žádosti. Žádost musí obsahovat seznam `StartRequests`. Každý `StartRequest` obsahuje `EventId` pro událost, kterou chcete urychlit:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> To v úvahu událost umožňuje událostí, aby bylo možné pokračovat pro všechny `Resources` v případě, že, ne jenom virtuální počítač, který uznává události. Proto můžete zvolit vedoucí ke koordinaci potvrzení, který může být stejně jednoduché jako první počítač v `Resources` pole.


## <a name="powershell-sample"></a>Ukázkové prostředí PowerShell 

Následující příklad dotazu na metadata službu pro naplánované události a schválí všechny nevyřízené události.

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
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

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
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Další postup 

- Sledování [naplánované události ukázku](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) v Azure pátek. 
- Přečtěte si ukázky kódu naplánované události v [Azure Instance Metadata naplánované události úložiště Github](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Další informace o rozhraní API dostupná v [Instance Metadata služby](instance-metadata-service.md).
- Další informace o [plánované údržby pro virtuální počítače s Windows v Azure](planned-maintenance.md).
