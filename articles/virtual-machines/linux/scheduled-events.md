---
title: Scheduled Events pro virtuální počítače se systémem Linux v Azure
description: Plánování událostí pomocí Metadata Service Azure pro virtuální počítače se systémem Linux.
author: EricRadzikowskiMSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: 3bda1e2076e29fc1365bfc236adc9071db2564a1
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104735"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Scheduled Events pro virtuální počítače s Linuxem

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

- [Údržba iniciovaná platformou](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json) (například restartování virtuálního počítače, migrace za provozu nebo zachovávání aktualizací v paměti pro hostitele)
- Virtuální počítač běží na [degradované hostitelském hardwaru](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) , který brzy vypoví selhání.
- Údržba iniciované uživatelem (například uživatel restartuje nebo znovu nasadí virtuální počítač)
- Vyřazení instancí [sad](../../virtual-machine-scale-sets/use-spot.md) [virtuálních počítačů](../spot-vms.md) a škálování na místě

## <a name="the-basics"></a>Základy  

  Metadata Service zpřístupňuje informace o spuštěných virtuálních počítačích pomocí koncového bodu REST, který je přístupný z virtuálního počítače. Tyto informace jsou k dispozici prostřednictvím nonroutable IP adresy, aby se nezobrazovaly mimo virtuální počítač.

### <a name="scope"></a>Obor
Naplánované události jsou doručovány do:

- Samostatné Virtual Machines.
- Všechny virtuální počítače v cloudové službě.
- Všechny virtuální počítače ve skupině dostupnosti.
- Všechny virtuální počítače v zóně dostupnosti. 
- Všechny virtuální počítače ve skupině umístění sady škálování 

> [!NOTE]
> V rámci konkrétního virtuálního počítače v zóně dostupnosti najdou naplánované události na jednotlivé virtuální počítače v zóně.
> Například pokud máte ve skupině dostupnosti 100 virtuálních počítačů a k jedné z nich je nějaká aktualizace, naplánovaná událost přejde na všechny 100, zatímco 100 Pokud v zóně dojde k jednomu virtuálnímu počítači, pak se do virtuálního počítače přejdou jenom ty, které mají vliv na.

Výsledkem je, že `Resources` v poli v události zjistíte, které virtuální počítače jsou ovlivněné.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální počítače s povolenými VIRTUÁLNÍmi sítěmi je Metadata Service k dispozici ze statické IP adresy nonroutable `169.254.169.254` . Úplný koncový bod pro nejnovější verzi Scheduled Events je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Pokud se virtuální počítač nevytvoří v rámci Virtual Network, výchozí případy cloudových služeb a klasických virtuálních počítačů vyžadují další logiku pro zjištění IP adresy, která se má použít. Další informace o tom, jak [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), najdete v této ukázce.

### <a name="version-and-region-availability"></a>Dostupnost verze a oblasti
Služba Scheduled Events má verzi. Verze jsou povinné. aktuální verze je `2019-08-01` .

| Verze | Typ verze | Oblasti | Zpráva k vydání verze | 
| - | - | - | - | 
| 2019-08-01 | Obecná dostupnost | Vše | <li> Přidání podpory pro EventSource |
| 2019-04-01 | Obecná dostupnost | Vše | <li> Přidání podpory pro popis události |
| 2019-01-01 | Obecná dostupnost | Vše | <li> Přidaná podpora pro virtuální počítač Scale Sets EventType ' ukončit ' |
| 2017-11-01 | Obecná dostupnost | Vše | <li> Přidání podpory pro vyřazení virtuálních počítačů s názvem EventType<br> | 
| 2017-08-01 | Obecná dostupnost | Vše | <li> Z názvů prostředků pro virtuální počítače s IaaS se odebraly předpony s podtržítkem.<br><li>Požadavek na hlavičku metadat vynutil pro všechny požadavky | 
| 2017-03-01 | Preview | Vše | <li>Původní vydaná verze |


> [!NOTE] 
> Předchozí verze Preview Scheduled Events podporovaly {nejnovější} jako verzi API. Tento formát se už nepodporuje a v budoucnu se už nepoužívá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání plánovaných událostí
Scheduled Events je pro vaši službu povolený při prvním vytvoření žádosti o události. Při prvním volání až dvou minut byste měli očekávat opožděnou odpověď.

Scheduled Events je pro vaši službu zakázaný, pokud nevytvoří žádost na 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba iniciovaná uživatelem
Údržba virtuálního počítače iniciovaná uživatelem pomocí Azure Portal, rozhraní API, CLI nebo PowerShellu způsobí naplánovanou událost. Potom můžete otestovat logiku přípravy údržby ve vaší aplikaci a vaše aplikace se může připravit na uživatelem iniciovaná údržba.

Při restartování virtuálního počítače se naplánuje událost s typem `Reboot` . Pokud znovu nasadíte virtuální počítač, bude naplánována událost s typem `Redeploy` .

## <a name="use-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Hlavičky
Při dotazování Metadata Service musíte zadat hlavičku, `Metadata:true` abyste zajistili, že se žádost neúmyslně přesměrovala. `Metadata:true`Hlavička je vyžadována pro všechny požadavky na naplánované události. Neúspěšné zahrnutí hlavičky do žádosti způsobí odpověď "chybná žádost" od Metadata Service.

### <a name="query-for-events"></a>Dotaz na události
Dotaz na naplánované události můžete provést provedením následujícího volání:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Odpověď obsahuje pole naplánovaných událostí. Prázdné pole znamená, že aktuálně nejsou žádné události naplánovány.
V případě naplánovaných událostí obsahuje odpověď pole událostí. 
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
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Vlastnosti události
|Vlastnost  |  Popis |
| - | - |
| ID události | Globálně jedinečný identifikátor pro tuto událost. <br><br> Příklad: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ události | Dopad této události způsobí. <br><br> Hodnoty: <br><ul><li> `Freeze`: U virtuálního počítače se naplánovalo pozastavení na několik sekund. Může být pozastaveno připojení k procesoru a k síti, ale neexistuje žádný vliv na paměť nebo otevřené soubory.<li>`Reboot`: Virtuální počítač má naplánován restart (netrvalá paměť je ztracená). <li>`Redeploy`: Virtuální počítač má naplánovaný přesun na jiný uzel (dočasné disky se ztratí). <li>`Preempt`: Odstraňuje se virtuální počítač se skvrnou (dočasné disky se ztratí). <li> `Terminate`: Je naplánováno odstranění virtuálního počítače. |
| ResourceType | Typ prostředku, na který tato událost ovlivňuje. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které tato událost ovlivňuje V seznamu je zaručeno, že bude obsahovat počítače z jedné [aktualizační domény](../manage-availability.md), ale nemusí obsahovat všechny počítače v ud. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stav této události <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánována na spuštění po uplynutí doby zadané ve `NotBefore` Vlastnosti.<li>`Started`: Tato událost je spuštěná.</ul> `Completed`Není k dispozici žádný nebo podobný stav. Událost již není vrácena po dokončení události.
| NotBefore| Čas, po kterém může být tato událost spuštěna. <br><br> Příklad: <br><ul><li> Pondělí 19. září 2016 18:29:47 GMT  |
| Description | Popis této události <br><br> Příklad: <br><ul><li> Hostitelský server prochází údržbou. |
| EventSource | Iniciátor události. <br><br> Příklad: <br><ul><li> `Platform`: Tato událost je iniciována platformou. <li>`User`: Tato událost je iniciována uživatelem. |

### <a name="event-scheduling"></a>Plánování událostí
Každé události je naplánováno minimální množství času v budoucnu na základě typu události. Tato doba se projeví ve vlastnosti události `NotBefore` . 

|Typ události  | Minimální oznámení |
| - | - |
| Uvolnění| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |
| Přerušen | 30 sekund |
| Terminate (Ukončení) | [Uživatelsky konfigurovatelné](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 až 15 minut |

> [!NOTE] 
> V některých případech může Azure předpovědět selhání hostitele kvůli zhoršenému hardwaru a při plánování migrace se pokusí zmírnit přerušení služby. Ovlivněné virtuální počítače dostanou plánovanou událost `NotBefore` , která je obvykle několik dní v budoucnu. Skutečný čas se liší v závislosti na předpokládaném vyhodnocení rizik při selhání. Pokud je to možné, Azure se pokusí poskytnout oznámení v předstihu 7 dní, ale skutečná doba se změní a může být menší, pokud je předpověď taková, že dojde k bezprostřednímu výpadku hardwaru. Abyste minimalizovali riziko pro vaši službu pro případ, že se hardware před migrací iniciované systémem nezdařil, doporučujeme, abyste virtuální počítač co nejdříve znovu nasadili.

### <a name="polling-frequency"></a>Frekvence cyklického dotazování

Koncový bod můžete podle potřeby dotazovat na aktualizace, jak často nebo často. Doba mezi požadavky však bude trvat déle, než budete chtít reagovat na nadcházející událost. Většina událostí má 5 až 15 minut oznámení předem, i když v některých případech může předběžné oznámení trvat až 30 sekund. Aby bylo zajištěno, že budete mít k dispozici co nejvíce času pro zmírňování akcí, doporučujeme, abyste službu provedli jednou za sekundu.

### <a name="start-an-event"></a>Spustit událost 

Jakmile se seznámíte s nadcházející událostí a dokončíte logiku pro řádné vypnutí, můžete tuto událost schválit tím, že `POST` zavoláte metadata Service s `EventId` . Toto volání indikuje Azure, že může zkrátit minimální dobu oznámení (Pokud je to možné). 

V textu žádosti se očekává následující ukázka JSON `POST` . Žádost by měla obsahovat seznam `StartRequests` . Každý `StartRequest` obsahuje `EventId` pro událost, kterou chcete urychlit:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potvrzení události umožní, aby událost pokračovala v případě, že událost `Resources` není pouze virtuálním počítačem, který potvrzuje událost. Proto můžete zvolit vedoucího, který bude koordinovat potvrzení, což může být jednoduché jako první počítač v `Resources` poli.

## <a name="python-sample"></a>Ukázka Pythonu 

Následující ukázkové dotazy Metadata Service pro naplánované události a schvalují každou zbývající událost:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
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
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Další kroky 
- Podívejte se na ukázku [Scheduled Events v Azure pátek](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) . 
- Přečtěte si ukázky kódu Scheduled Events v [metadatech instance Azure Scheduled Events úložiště GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Přečtěte si další informace o rozhraních API, která jsou k dispozici v [instance metadata Service](instance-metadata-service.md).
- Přečtěte si o [plánované údržbě virtuálních počítačů se systémem Linux v Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json).
