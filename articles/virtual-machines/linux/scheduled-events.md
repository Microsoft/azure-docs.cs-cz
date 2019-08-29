---
title: Scheduled Events pro virtuální počítače se systémem Linux v Azure | Microsoft Docs
description: Plánování událostí pomocí Metadata Service Azure pro virtuální počítače se systémem Linux.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: d427544ab9396211e4cbb247527a0eb848f42926
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091287"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Metadata Service Azure: Scheduled Events pro virtuální počítače se systémem Linux

Scheduled Events je Azure Metadata Service, který umožňuje čas vaší aplikace připravit se na údržbu virtuálních počítačů. Poskytuje informace o nadcházejících událostech údržby (například restartování), aby se aplikace mohla připravit na jejich přerušení a omezit jejich přerušení. Je k dispozici pro všechny typy Azure Virtual Machines, včetně PaaS a IaaS v systémech Windows i Linux. 

Informace o Scheduled Events ve Windows najdete v tématu [Scheduled Events pro virtuální počítače s Windows](../windows/scheduled-events.md).

> [!Note] 
> Scheduled Events je všeobecně dostupná ve všech oblastech Azure. Nejnovější informace o verzi najdete v tématu [dostupnost verze a oblasti](#version-and-region-availability) .

## <a name="why-use-scheduled-events"></a>Proč použít Scheduled Events?

K přípravě na údržbu virtuálních počítačů může přinést spousta aplikací čas. Čas lze použít k provádění úloh specifických pro aplikaci, které zlepšují dostupnost, spolehlivost a dostupnost služby, včetně: 

- Kontrolní bod a obnovení.
- Vyprazdňování připojení.
- Převzetí služeb při selhání primární repliky.
- Odebrání z fondu nástroje pro vyrovnávání zatížení.
- Protokolování událostí.
- Řádné vypnutí.

Pomocí Scheduled Events může aplikace zjistit, kdy se bude provádět údržba, a aktivovat úkoly, které omezují jeho dopad.  

Scheduled Events poskytuje události v následujících případech použití:

- [Údržba iniciovaná platformou](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (například restartování virtuálního počítače, migrace za provozu nebo zachovávání aktualizací v paměti pro hostitele)
- Snížený hardware
- Údržba iniciované uživatelem (například uživatel restartuje nebo znovu nasadí virtuální počítač)
- [Vyřazení virtuálních počítačů s nízkou prioritou](https://azure.microsoft.com/blog/low-priority-scale-sets) v sadách škálování

## <a name="the-basics"></a>Základní informace  

  Metadata Service zpřístupňuje informace o spuštěných virtuálních počítačích pomocí koncového bodu REST, který je přístupný z virtuálního počítače. Tyto informace jsou k dispozici prostřednictvím nonroutable IP adresy, aby se nezobrazovaly mimo virtuální počítač.

### <a name="scope"></a>Scope
Naplánované události jsou doručovány do:

- Samostatné Virtual Machines.
- Všechny virtuální počítače v cloudové službě.
- Všechny virtuální počítače ve skupině dostupnosti.
- Všechny virtuální počítače ve skupině umístění sady škálování 

Výsledkem je, že `Resources` v poli v události zjistíte, které virtuální počítače jsou ovlivněné.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální počítače s povolenými VIRTUÁLNÍmi sítěmi je Metadata Service k dispozici `169.254.169.254`ze statické IP adresy nonroutable. Úplný koncový bod pro nejnovější verzi Scheduled Events je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Pokud se virtuální počítač nevytvoří v rámci Virtual Network, výchozí případy cloudových služeb a klasických virtuálních počítačů vyžadují další logiku pro zjištění IP adresy, která se má použít. Další informace o tom, jak [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), najdete v této ukázce.

### <a name="version-and-region-availability"></a>Dostupnost verze a oblasti
Služba Scheduled Events má verzi. Verze jsou povinné. aktuální verze je `2017-11-01`.

| Version | Typ verze | Regions | Poznámky k verzi | 
| - | - | - | - | 
| 2017-11-01 | Všeobecná dostupnost | Vše | <li> Přidala se podpora pro vyřazení virtuálních počítačů s nízkou prioritou, EventType.<br> | 
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Z názvů prostředků pro virtuální počítače s IaaS se odebraly předpony s podtržítkem.<br><li>Požadavek na hlavičku metadat vynutil pro všechny požadavky | 
| 2017-03-01 | Náhled | Vše | <li>Původní vydaná verze


> [!NOTE] 
> Předchozí verze Preview Scheduled Events podporovaly {nejnovější} jako verzi API. Tento formát se už nepodporuje a v budoucnu se už nepoužívá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zákaz Scheduled Events
Scheduled Events je pro vaši službu povolený při prvním vytvoření žádosti o události. Při prvním volání až dvou minut byste měli očekávat opožděnou odpověď.

Scheduled Events je pro vaši službu zakázaný, pokud nevytvoří žádost na 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Údržba virtuálního počítače iniciovaná uživatelem pomocí Azure Portal, rozhraní API, CLI nebo PowerShellu způsobí naplánovanou událost. Potom můžete otestovat logiku přípravy údržby ve vaší aplikaci a vaše aplikace se může připravit na uživatelem iniciovaná údržba.

Při restartování virtuálního počítače se naplánuje událost s typem `Reboot` . Pokud znovu nasadíte virtuální počítač, bude naplánována událost `Redeploy` s typem.

## <a name="use-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Záhlaví
Při dotazování metadata Service musíte zadat hlavičku `Metadata:true` , abyste zajistili, že se žádost neúmyslně přesměrovala. `Metadata:true` Hlavička je vyžadována pro všechny požadavky na naplánované události. Neúspěšné zahrnutí hlavičky do žádosti způsobí odpověď "chybná žádost" od Metadata Service.

### <a name="query-for-events"></a>Dotaz na události
Dotaz na naplánované události můžete provést provedením následujícího volání:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že aktuálně nejsou žádné události naplánovány.
V případě naplánovaných událostí obsahuje odpověď pole událostí. 
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

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| ID události | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Dopad této události způsobí. <br><br> Hodnota <br><ul><li> `Freeze`: U virtuálního počítače se naplánovalo pozastavení na několik sekund. Může být pozastaveno připojení k procesoru a k síti, ale neexistuje žádný vliv na paměť nebo otevřené soubory.<li>`Reboot`: U virtuálního počítače je naplánován restart (netrvalá paměť). <li>`Redeploy`: Virtuální počítač má naplánovaný přesun na jiný uzel (dočasné disky se ztratí). <li>`Preempt`: Odstraňuje se virtuální počítač s nízkou prioritou (dojde ke ztrátě dočasných disků).|
| Typ prostředku | Typ prostředku, na který tato událost ovlivňuje. <br><br> Hodnota <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které tato událost ovlivňuje V seznamu je zaručeno, že bude obsahovat počítače z jedné [aktualizační domény](manage-availability.md), ale nemusí obsahovat všechny počítače v ud. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stav této události <br><br> Hodnota <ul><li>`Scheduled`: Tato událost je naplánována na spuštění po uplynutí doby zadané ve `NotBefore` vlastnosti.<li>`Started`: Tato událost je spuštěná.</ul> Není `Completed` k dispozici žádný nebo podobný stav. Událost již není vrácena po dokončení události.
| NotBefore| Čas, po kterém může být tato událost spuštěna. <br><br> Příklad: <br><ul><li> Pondělí 19. září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každé události je naplánováno minimální množství času v budoucnu na základě typu události. Tato doba se projeví ve `NotBefore` vlastnosti události. 

|Typ události  | Minimální oznámení |
| - | - |
| Uvolnění| 15 minut |
| Restartovat | 15 minut |
| Nasaďte | 10 minut |
| Přerušen | 30 sekund |

### <a name="start-an-event"></a>Spustit událost 

Jakmile se seznámíte s nadcházející událostí a dokončíte logiku pro řádné vypnutí, můžete tuto událost schválit tím `POST` , že zavoláte metadata Service s. `EventId` Toto volání indikuje Azure, že může zkrátit minimální dobu oznámení (Pokud je to možné). 

V `POST` textu žádosti se očekává následující ukázka JSON. Žádost by měla obsahovat seznam `StartRequests`. Každý `StartRequest` obsahuje`EventId` pro událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Ukázka bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Potvrzení události umožní, aby událost pokračovala `Resources` v případě, že událost není pouze virtuálním počítačem, který potvrzuje událost. Proto můžete zvolit vedoucího, který bude koordinovat potvrzení, což může být jednoduché jako první počítač v `Resources` poli.

## <a name="python-sample"></a>Ukázka Pythonu 

Následující ukázkové dotazy Metadata Service pro naplánované události a schvalují každou zbývající událost:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Další postup 
- Podívejte se na ukázku [Scheduled Events v Azure pátek](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) . 
- Přečtěte si ukázky kódu Scheduled Events v [metadatech instance Azure Scheduled Events úložiště GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Přečtěte si další informace o rozhraních API, která jsou k dispozici v [instance metadata Service](instance-metadata-service.md).
- Přečtěte si o [plánované údržbě virtuálních počítačů se systémem Linux v Azure](planned-maintenance.md).
