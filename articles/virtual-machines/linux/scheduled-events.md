---
title: Plánované události pro virtuální počítače s Linuxem v Azure
description: Naplánujte události pomocí služby Azure Metadata Service pro vaše virtuální počítače s Linuxem.
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
ms.openlocfilehash: dbea68f5699f26b866d2e22c960c0359bcb3479b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267193"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Služba metadat Azure: Naplánované události pro virtuální počítače s Linuxem

Naplánované události je služba metadat Azure, která poskytuje vaší aplikaci čas na přípravu na údržbu virtuálního počítače. Poskytuje informace o nadcházejícíudálosti údržby (například restartování), takže vaše aplikace můžete připravit na ně a omezit narušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS na Windows i Linux. 

Informace o naplánovaných událostech v systému Windows naleznete v [tématu Naplánované události pro virtuální počítači systému Windows](../windows/scheduled-events.md).

> [!Note] 
> Naplánované události jsou obecně dostupné ve všech oblastech Azure. Nejnovější informace o verzi najdete v [tématu Verze a dostupnost oblastí.](#version-and-region-availability)

## <a name="why-use-scheduled-events"></a>Proč používat naplánované události?

Mnoho aplikací může těžit z času připravit se na údržbu virtuálního počítače. Čas lze použít k provádění úloh specifických pro aplikaci, které zlepšují dostupnost, spolehlivost a provozuschopnost, včetně: 

- Kontrolní bod a obnovení.
- Vypouštění spojení.
- Primární replika převzetí služeb při selhání.
- Odebrání z fondu vyrovnávání zatížení.
- Protokolování událostí.
- Řádné vypnutí.

S naplánované události, vaše aplikace můžete zjistit, kdy dojde k údržbě a aktivovat úlohy omezit jeho dopad.  

Naplánované události poskytují události v následujících případech použití:

- [Údržba iniciovaná platformou](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (například restartování virtuálního počítače, migrace za provozu nebo aktualizace zachování paměti pro hostitele)
- Virtuální počítač běží na [zhoršené hostitelském hardwaru,](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) u kterého se předpokládá, že brzy selže
- Údržba iniciovaná uživatelem (například uživatel restartuje nebo znovu nasadí virtuální počítač)
- Vyřazování instance sady [virtuálních](spot-vms.md) virtuálních počítačů a [škálovací](../../virtual-machine-scale-sets/use-spot.md) chodspotů.

## <a name="the-basics"></a>Základy  

  Služba metadat poskytuje informace o spouštění virtuálních klíčů pomocí koncového bodu REST, který je přístupný z virtuálního provozu. Informace jsou k dispozici prostřednictvím nesměrovatelné IP tak, aby nebyly vystaveny mimo virtuální hod.

### <a name="scope"></a>Rozsah
Plánované události jsou doručovány do:

- Samostatné virtuální počítače.
- Všechny virtuální aplikace v cloudové službě.
- Všechny virtuální aplikace v sadě dostupnosti.
- Všechny virtuální chody ve skupině umístění škálovací sady. 

V důsledku toho `Resources` zkontrolujte pole v události k identifikaci, které virtuální chody jsou ovlivněny.

### <a name="endpoint-discovery"></a>Zjišťování koncového bodu
Pro virtuální chod s povolenovirtuální sítě je služba metadat dostupná ze statické nesměrovatelné IP adresy `169.254.169.254`. Úplný koncový bod pro nejnovější verzi naplánovaných událostí je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Pokud virtuální počítač není vytvořen v rámci virtuální sítě, výchozí případy pro cloudové služby a klasické virtuální počítače, další logika je nutné zjistit IP adresu, kterou chcete použít. Informace o [tom,](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)jak zjistit koncový bod hostitele , naleznete v této ukázce.

### <a name="version-and-region-availability"></a>Dostupnost verze a oblasti
Služba Naplánované události je verzí. Verze jsou povinné; aktuální verze `2019-01-01`je .

| Version | Typ vydání | Oblasti | Poznámky k verzi | 
| - | - | - | - | 
| 2019-01-01 | Obecná dostupnost | Všechny | <li> Přidána podpora škálovacích sad virtuálních strojů EventType 'Terminate' |
| 2017-11-01 | Obecná dostupnost | Všechny | <li> Přidána podpora pro vyřazovací eventtype spotového virtuálního počítači 'Preempt'<br> | 
| 2017-08-01 | Obecná dostupnost | Všechny | <li> Odebrané předřazené podtržítko z názvů prostředků pro virtuální ms IaaS<br><li>Požadavek na hlavičku metadat vynucený pro všechny požadavky | 
| 2017-03-01 | Preview | Všechny | <li>Původní vydaná verze |


> [!NOTE] 
> Předchozí verze preview plánovaných událostí podporované {latest} jako api-version. Tento formát již není podporován a v budoucnu bude zastaralá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání plánovaných událostí
Naplánované události jsou pro vaši službu povoleny při prvním vytvoření žádosti o události. Měli byste očekávat opožděnou odpověď při prvním volání až dvě minuty.

Plánované události jsou pro vaši službu zakázány, pokud nepodá požadavek na 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Údržba virtuálních počítači iniciovaná uživatelem prostřednictvím portálu Azure, rozhraní API, rozhraní API nebo prostředí PowerShell má za následek naplánovanou událost. Potom můžete otestovat logiku přípravy údržby ve vaší aplikaci a vaše aplikace se může připravit na údržbu inicionouji uživatelem.

Pokud restartujete virtuální počítač, `Reboot` je naplánována událost s typem. Pokud znovu nasadíte virtuální hod, je naplánována událost s typem. `Redeploy`

## <a name="use-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Hlavičky
Při dotazování služby metadat je `Metadata:true` nutné zadat záhlaví, abyste zajistili, že požadavek nebyl neúmyslně přesměrován. Záhlaví `Metadata:true` je vyžadováno pro všechny naplánované požadavky na události. Pokud nezahrnete záhlaví v požadavku, výsledkem je odpověď "Chybný požadavek" ze služby metadat.

### <a name="query-for-events"></a>Dotaz na události
Na plánované události můžete provádět dotazy následujícím voláním:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že aktuálně nejsou naplánovány žádné události.
V případě, že jsou naplánované události, odpověď obsahuje pole událostí. 
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

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| Eventid | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Vliv na tuto událost způsobí. <br><br> Hodnoty: <br><ul><li> `Freeze`: Virtuální počítač je naplánováno pozastavit na několik sekund. Připojení procesoru a sítě může být pozastaveno, ale nemá žádný vliv na paměť nebo otevřené soubory.<li>`Reboot`: Virtuální počítač je naplánováno restartování (netrvalá paměť je ztracena). <li>`Redeploy`: Virtuální počítač je naplánováno přesunout do jiného uzlu (dočasné disky jsou ztraceny). <li>`Preempt`: Virtuální stroj Spot se odstraňuje (dočasné disky jsou ztraceny). <li> `Terminate`: Virtuální počítač je naplánováno na odstranění. |
| ResourceType | Typ prostředku, který tato událost ovlivňuje. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Prostředky| Seznam zdrojů, které tato událost ovlivňuje. Je zaručeno, že seznam bude obsahovat počítače maximálně z jedné [aktualizační domény](manage-availability.md), ale nemusí obsahovat všechny počítače v ud. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánováno spustit po `NotBefore` čase určeném ve vlastnosti.<li>`Started`: Tato událost byla zahájena.</ul> Není `Completed` nikdy uveden žádný nebo podobný stav. Událost již není vrácena po dokončení události.
| Není před| Doba, po které může tato událost začít. <br><br> Příklad: <br><ul><li> Po, 19 Zář 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každá událost je naplánována minimální množství času v budoucnu na základě typu události. Tentokrát se odráží ve `NotBefore` vlastnosti události. 

|Typ události  | Minimální upozornění |
| - | - |
| Zmrazit| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |
| Preempt | 30 sekund |
| Terminate | [Uživatelsky konfigurovatelné:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 až 15 minut |

> [!NOTE] 
> V některých případech azure je schopen předpovědět selhání hostitele z důvodu degradovaného hardwaru a pokusí se zmírnit narušení vaší služby plánováním migrace. Ovlivněné virtuální počítače obdrží naplánovanou událost s, `NotBefore` která je obvykle několik dní v budoucnosti. Skutečný čas se liší v závislosti na předpokládaném posouzení rizika selhání. Azure se pokusí dát 7 dní předem oznámení, pokud je to možné, ale skutečný čas se liší a může být menší, pokud předpověď je, že je vysoká pravděpodobnost selhání hardwaru bezprostředně. Chcete-li minimalizovat riziko pro vaši službu v případě, že hardware selže před migrací iniciodou systémem, doporučujeme, abyste svůj virtuální počítač co nejdříve znovu nasadili.

### <a name="start-an-event"></a>Zahájení události 

Po sešlápnutí nadcházející události a dokončení logiky pro řádné vypnutí `POST` můžete schválit nevyřízenou událost voláním služby Metadata Service pomocí aplikace `EventId`. Toto volání označuje Azure, že můžete zkrátit minimální dobu oznámení (pokud je to možné). 

Následující vzorek JSON se `POST` očekává v těle požadavku. Požadavek by měl `StartRequests`obsahovat seznam . Každý `StartRequest` `EventId` obsahuje událost, kterou chcete urychlit:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash ukázka
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potvrzení události umožňuje událost pokračovat pro `Resources` všechny v události, nikoli pouze virtuální ho virtuálního vědomí, který událost uznává. Proto můžete zvolit vedoucího pro koordinaci potvrzení, které může být stejně `Resources` jednoduché jako první stroj v poli.

## <a name="python-sample"></a>Ukázka Pythonu 

Následující ukázka se dotazuje služby metadat na naplánované události a schvaluje každou nevyřízené události:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
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
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Další kroky 
- Podívejte se [na plánované události v Azure pátek](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) a podívejte se na ukázku. 
- Zkontrolujte ukázky kódu naplánované události v [úložišti GitHub naplánovaných instancí Azure](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)Instance.
- Přečtěte si další informace o rozhraních API, která jsou k dispozici ve [službě metadat instance](instance-metadata-service.md).
- Přečtěte si o [plánované údržbě virtuálních počítačů s Linuxem v Azure](planned-maintenance.md).
