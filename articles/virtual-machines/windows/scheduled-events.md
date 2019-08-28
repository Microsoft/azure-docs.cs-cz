---
title: Scheduled Events pro virtuální počítače s Windows v Azure | Microsoft Docs
description: Naplánované události, které využívají službu Azure metadata Service pro na virtuálních počítačích s Windows.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 087f27b3857363c0b5f244ecd52ebd64105626b5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102405"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Metadata Service Azure: Scheduled Events pro virtuální počítače s Windows

Scheduled Events je Azure Metadata Service, který poskytuje čas vaší aplikace při přípravě na údržbu virtuálních počítačů. Poskytuje informace o nadcházejících událostech údržby (třeba restartování), takže se aplikace může připravit na jejich přerušení a omezit jejich přerušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS v systémech Windows i Linux. 

Informace o Scheduled Events v systému Linux najdete v tématu [Scheduled Events pro virtuální počítače se systémem Linux](../linux/scheduled-events.md).

> [!Note] 
> Scheduled Events je všeobecně dostupná ve všech oblastech Azure. Nejnovější informace o verzi najdete v tématu [dostupnost verze a oblasti](#version-and-region-availability) .

## <a name="why-scheduled-events"></a>Proč Scheduled Events?

Mnoho aplikací může mít čas na přípravu údržby virtuálních počítačů. Čas lze použít k provádění úloh specifických pro aplikace, které zlepšují dostupnost, spolehlivost a dostupnost služby, včetně: 

- Kontrolní bod a obnovení
- Vyprazdňování připojení
- Převzetí služeb při selhání primární repliky 
- Odebrání z fondu nástroje pro vyrovnávání zatížení
- Protokolování událostí
- Řádné vypnutí 

Pomocí Scheduled Events může aplikace zjistit, kdy se bude provádět údržba, a aktivovat úkoly, které omezují jeho dopad. Povolením naplánovaných událostí zajistíte, aby virtuální počítač měl před provedením aktivity údržby minimální dobu. Podrobnosti najdete níže v části Plánování událostí.

Scheduled Events poskytuje události v následujících případech použití:
- [Údržba iniciovaná platformou](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (například restartování virtuálního počítače, migrace za provozu nebo zachovávání aktualizací v paměti pro hostitele)
- Snížený hardware
- Údržba iniciovaná uživatelem (třeba restartováním nebo opětovným nasazením virtuálního počítače)
- [Vyřazení virtuálních počítačů s nízkou prioritou](https://azure.microsoft.com/blog/low-priority-scale-sets) v sadách škálování

## <a name="the-basics"></a>Základní informace  

Služba Azure metadata Service zpřístupňuje informace o spouštění Virtual Machines pomocí koncového bodu REST přístupného v rámci virtuálního počítače. Tyto informace jsou k dispozici prostřednictvím IP adresy, která není směrovatelný, takže se nezveřejňuje mimo virtuální počítač.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální počítače s povolenými VIRTUÁLNÍmi sítěmi je služba metadat dostupná ze statické IP adresy, `169.254.169.254`která není směrovatelný. Úplný koncový bod pro nejnovější verzi Scheduled Events je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Pokud se virtuální počítač nevytvoří v Virtual Network, výchozí případy cloudových služeb a klasických virtuálních počítačů vyžadují další logiku pro zjištění IP adresy, která se má použít. Informace o tom, jak [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), najdete v této ukázce.

### <a name="version-and-region-availability"></a>Dostupnost verze a oblasti
Služba Scheduled Events má verzi. Verze jsou povinné a aktuální verze je `2017-11-01`.

| Version | Typ verze | Regions | Poznámky k verzi | 
| - | - | - | - |
| 2017-11-01 | Všeobecná dostupnost | Vše | <li> Přidala se podpora pro vyřazení virtuálních počítačů s nízkou prioritou, EventType.<br> | 
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Z názvů prostředků pro virtuální počítače s IaaS se odebraly předpony s podtržítkem.<br><li>Požadavek na hlavičku metadat vynutil pro všechny požadavky | 
| 2017-03-01 | Náhled | Vše |<li>Původní vydaná verze

> [!NOTE] 
> Předchozí verze Preview s naplánovanými událostmi, které jsou podporované {nejnovější} jako verze API. Tento formát se už nepodporuje a v budoucnu se už nepoužívá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zákaz Scheduled Events
Scheduled Events je pro vaši službu povolený při prvním vytvoření žádosti o události. Při prvním volání až dvou minut byste měli očekávat opožděnou odpověď. Pravidelně byste měli zadat dotaz na koncový bod a zjistit nadcházející události údržby a také stav prováděných aktivit údržby.

Scheduled Events je pro vaši službu zakázaný, pokud nevytvoří žádost na 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Uživatelem iniciovaná údržba virtuálního počítače pomocí Azure Portal, rozhraní API, CLI nebo PowerShellu způsobí naplánovanou událost. To vám umožní testovat logiku přípravy údržby ve vaší aplikaci a umožňuje aplikaci připravit se na údržbu iniciované uživatelem.

Po restartování virtuálního počítače se naplánuje událost `Reboot`s typem. Když se znovu nasadí virtuální počítač, naplánuje `Redeploy`se událost s typem.

## <a name="using-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Záhlaví
Při dotazování na metadata Service musíte zadat hlavičku `Metadata:true` , abyste zajistili, že se žádost neúmyslně přesměrovala. `Metadata:true` Hlavička je vyžadována pro všechny požadavky na naplánované události. Nepovedlo se zahrnout hlavičku do žádosti. výsledkem bude nesprávná odpověď na požadavek od Metadata Service.

### <a name="query-for-events"></a>Dotaz na události
Dotaz na Scheduled Events můžete jednoduše provést následujícím voláním:

#### <a name="powershell"></a>Prostředí PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že aktuálně nejsou naplánovány žádné události.
V případě naplánovaných událostí obsahuje odpověď pole událostí: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation je ETag a poskytuje snadný způsob, jak zkontrolovat, jestli se od posledního dotazu změnila datová část událostí.

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| ID události | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Dopad této události způsobí. <br><br> Hodnota <br><ul><li> `Freeze`: U virtuálního počítače se naplánovalo pozastavení na několik sekund. Může být pozastaveno připojení k procesoru a k síti, ale neexistuje žádný vliv na paměť nebo otevřené soubory. <li>`Reboot`: U virtuálního počítače je naplánován restart (netrvalá paměť). <li>`Redeploy`: Virtuální počítač má naplánovaný přesun na jiný uzel (dočasné disky se ztratí). <li>`Preempt`: Odstraňuje se virtuální počítač s nízkou prioritou (dojde ke ztrátě dočasných disků).|
| Typ prostředku | Typ prostředku, na který tato událost ovlivňuje. <br><br> Hodnota <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které tato událost ovlivňuje. Je zaručeno, že bude obsahovat počítače z jedné [aktualizační domény](manage-availability.md), ale nemusí obsahovat všechny počítače v ud. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stav události | Stav této události <br><br> Hodnota <ul><li>`Scheduled`: Tato událost je naplánována na spuštění po uplynutí doby zadané ve `NotBefore` vlastnosti.<li>`Started`: Tato událost je spuštěná.</ul> V `Completed` tuto chvíli není k dispozici žádný nebo podobný stav. po dokončení události již událost nebude vrácena.
| NotBefore| Čas, po kterém se tato událost může spustit. <br><br> Příklad: <br><ul><li> Pondělí 19. září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každé události je naplánováno minimální množství času v budoucnu na základě typu události. Tato doba se projeví ve `NotBefore` vlastnosti události. 

|Typ události  | Minimální oznámení |
| - | - |
| Uvolnění| 15 minut |
| Restartovat | 15 minut |
| Nasaďte | 10 minut |
| Přerušen | 30 sekund |

### <a name="event-scope"></a>Rozsah události     
Naplánované události jsou doručovány do:
 - Samostatné Virtual Machines
 - Všechny Virtual Machines v cloudové službě      
 - Všechny Virtual Machines ve skupině dostupnosti      
 - Všechny Virtual Machines ve skupině umístění sady škálování.         

V důsledku toho byste měli zaškrtnout `Resources` pole v události a určit, které virtuální počítače budou mít vliv na to. 

### <a name="starting-an-event"></a>Spuštění události 

Jakmile jste se dozvěděli o nadcházející události a dokončili jste logiku pro řádné vypnutí, můžete nezpracované události `POST` schválit voláním služby metadat `EventId`pomocí. To znamená, že Azure může zkrátit minimální dobu oznámení (Pokud je to možné). 

Následuje znak JSON očekávaný v `POST` textu požadavku. Žádost by měla obsahovat seznam `StartRequests`. `StartRequest` Každý`EventId` obsahuje pro událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>Prostředí PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Potvrzení události umožní, aby událost pokračovala `Resources` v případě, že událost není pouze virtuálním počítačem, který událost potvrdí. Můžete tedy zvolit vedoucího vedoucího ke koordinaci potvrzení, což může být jednoduché jako první počítač v `Resources` poli.


## <a name="powershell-sample"></a>Ukázka PowerShellu 

Následující příklad vyžádá službu metadat pro naplánované události a schválí každou zbývající událost.

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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

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

- Podívejte se na [ukázku Scheduled Events](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) v Azure pátek. 
- Přečtěte si ukázky kódu Scheduled Events v [metadatech instance Azure Scheduled Events úložiště GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Přečtěte si další informace o rozhraních API dostupných ve [službě instance metadata](instance-metadata-service.md).
- Přečtěte si o [plánované údržbě virtuálních počítačů s Windows v Azure](planned-maintenance.md).
