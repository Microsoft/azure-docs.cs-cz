---
title: Naplánované události pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Naplánovat událostí pomocí služby Azure Metadata Service pro své Linuxové virtuální počítače.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: fc9ceb06eb51d1e88306f0971ad055facd05f9fb
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437261"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Služby Azure Metadata: Naplánované události pro virtuální počítače s Linuxem

Naplánované události se Azure Metadata Service, která poskytuje vaše aplikace času na přípravu údržby virtuální počítač (VM). Poskytuje informace o událostech nadcházející údržby (například restartování) tak, aby vaše aplikace lze připravit pro ně a omezit přerušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS ve Windows a Linuxu. 

Informace o službě Scheduled Events ve Windows najdete v tématu [naplánované událostí pro Windows VMs](../windows/scheduled-events.md).

> [!Note] 
> Naplánované události je obecně dostupná ve všech oblastech Azure. Zobrazit [verzí a dostupnost oblastí](#version-and-region-availability) pro informace o nejnovější verzi.

## <a name="why-use-scheduled-events"></a>Proč používat naplánovaných událostí?

Mnoho aplikací můžete těžit z času na přípravu pro údržbu virtuálního počítače. Čas je možné provádět úlohy specifické pro aplikaci, které zlepšují dostupnost, spolehlivost a použitelnost, včetně: 

- Kontrolní bod a obnovení.
- Vyprázdnění připojení.
- Převzetí služeb při selhání primární repliky.
- Odebrání z fondu nástroje pro vyrovnávání zatížení.
- Protokolování událostí.
- Řádné vypnutí.

Pomocí naplánovaných událostí vaše aplikace můžete zjistit, kdy bude údržby dojít k a spouštění úloh a omezit jejich dopad.  

Naplánované události poskytuje události v následujících případech použití:

- Údržby iniciované uživatelem platformy (například aktualizace hostitelský operační systém)
- Údržba zahájená uživatelem (například uživatele restartuje nebo znovu nasadí virtuální počítač)

## <a name="the-basics"></a>Základní informace  

  Metadata služby zveřejňuje informace o spouštění virtuálních počítačů s využitím koncového bodu REST, který je přístupný ve virtuálním počítači. Informace jsou dostupné prostřednictvím nepoužívající IP tak, aby se nevystaví mimo virtuální počítač.

### <a name="scope"></a>Rozsah
Naplánované události se doručují na:

- Všechny virtuální počítače v cloudové službě.
- Všechny virtuální počítače ve skupině dostupnosti.
- Všechny virtuální počítače ve škálovací nastavit skupiny umístění. 

Proto zkontrolujte, `Resources` pole událostí k určení, které virtuální počítače se to týká.

### <a name="endpoint-discovery"></a>Zjišťování koncových bodů
Pro virtuální síť virtuálních počítačů s povoleným, Metadata služba je k dispozici z IP adresy statické nepoužívající `169.254.169.254`. Úplné koncový bod pro nejnovější verzi naplánovaných událostí je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Pokud virtuální počítač se vytvoří ve virtuální síti, výchozí případy pro cloud services a klasické virtuální počítače, je další logiku potřebné ke zjišťování IP adres používat. Další postupy [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), najdete v této ukázce.

### <a name="version-and-region-availability"></a>Verze a dostupnost oblastí
Služba Scheduled Events se systémovou správou verzí. Verze jsou povinné; aktuální verze je `2017-08-01`.

| Verze | Typ verze | Oblasti | Poznámky k verzi | 
| - | - | - | - | 
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Odebrat před podtržítka z názvy prostředků pro virtuální počítače Iaas<br><li>Požadavek na metadata záhlaví vynucovat pro všechny požadavky | 
| 2017-03-01 | Preview | Vše | <li>Původní vydaná verze


> [!NOTE] 
> Předchozí verze preview Scheduled Events {nejnovější} podporován jako verze api-version. Tento formát se už nepodporuje a v budoucnu budou zastaralé.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání naplánované události
Naplánované události je povolený pro vaše služby první čas, který může požádat o události. Měli byste očekávat zpožděné odpovědi v první volání až dvě minuty.

Naplánované události je zakázaná pro vaši službu, pokud nepoužívá žádost po dobu 24 hodin.

### <a name="user-initiated-maintenance"></a>Údržba zahájená uživatelem
Uživatelem iniciované Údržba virtuálního počítače prostřednictvím webu Azure portal, rozhraní API, rozhraní příkazového řádku nebo Powershellu výsledkem naplánované události. Potom můžete otestovat logiky přípravy údržby ve vaší aplikaci a aplikace můžete připravit Údržba zahájená uživatelem.

Pokud restartování virtuálního počítače, událost s typem `Reboot` naplánován. Pokud jste opětovné nasazení virtuálního počítače, událost s typem `Redeploy` naplánován.

## <a name="use-the-api"></a>Použití rozhraní API

### <a name="headers"></a>Hlavičky
Když odešlete dotaz na Metadata Service, je nutné zadat hlavičku `Metadata:true` zajistit požadavek nebyl přesměrován náhodně. `Metadata:true` Hlavičky je povinná pro všechny požadavky naplánovaných událostí. Nepodařilo se zahrnout hlavičku požadavku za následek "Chybný požadavek" odpověď z metadat služby.

### <a name="query-for-events"></a>Dotaz pro události
Můžete zadat dotaz na naplánované události tak, že následující volání:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Odpověď obsahuje celou řadu naplánovaných událostí. Prázdné pole znamená, že aktuálně jsou naplánované žádné události.
V případě, kdy jsou naplánované události, odpověď obsahuje celou řadu událostí. 
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
| Typ události | Dopad, který způsobí, že se tato událost. <br><br> Hodnoty: <br><ul><li> `Freeze`: Virtuální počítač je naplánovaná pozastavit na několik sekund. Procesor je pozastaveno, ale neexistuje žádný vliv na paměť, otevřené soubory nebo připojení k síti. <li>`Reboot`: Virtuální počítač je naplánovaná restartování. (Nonpersistent paměti je ztracena). <li>`Redeploy`: Virtuální počítač je naplánovaná přesunout do jiného uzlu. (Dočasné disky se ztratí.) |
| ResourceType | Typ prostředku, který má vliv na tuto událost. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které má vliv na tuto událost. V seznamu je zaručeno, že obsahují počítače maximálně jednu [aktualizační doména](manage-availability.md), ale nemusí obsahovat všechny počítače ve skupině UD. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánované spuštění po dobu určenou v `NotBefore` vlastnost.<li>`Started`: Tato událost se spustila.</ul> Ne `Completed` nebo podobné stav je stále k dispozici. Vrátí události se už po dokončení události.
| neplatí před| Doba, po jejímž uplynutí může spustit tuto událost. <br><br> Příklad: <br><ul><li> Pondělí, 19. září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování události
Každé události je naplánovaný minimální množství čas v budoucnosti podle typu události. Tentokrát se projeví v události `NotBefore` vlastnost. 

|Typ události  | Minimální oznámení |
| - | - |
| zablokování| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |

### <a name="start-an-event"></a>Spustit událost 

Po další nadcházející události a dokončí svoji logiku pro řádné vypnutí, tím, že můžete schválit nezpracovaných událostí `POST` volání služby Metadata s `EventId`. Toto volání označuje do Azure, můžete zkrátit minimální oznámení čas (Pokud je to možné). 

Podle následující ukázky JSON se očekává v `POST` text žádosti. Požadavek by měl obsahovat seznam `StartRequests`. Každý `StartRequest` obsahuje `EventId` chcete urychlit události:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Ukázková prostředí bash
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Potvrdil událost umožňuje událostí, aby bylo možné pokračovat pro všechny `Resources` v případě, že, ne jenom virtuální počítač, který potvrzuje události. Proto můžete také zvolit vedoucí instanci ke koordinaci potvrzení, což může být stejně snadné jako prvního počítače v `Resources` pole.

## <a name="python-sample"></a>Ukázky Pythonu 

Následující ukázka dotazuje služby Metadata pro naplánované události a následně schválí každé nevyřízené události:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
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
- Sledování [naplánovaných událostí v Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) k podívejte se na ukázku. 
- Projděte si ukázky kódu naplánovaných událostí [úložiště Azure Instance Metadata naplánované události GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Další informace o rozhraní API, která jsou k dispozici v [služba Instance Metadata](instance-metadata-service.md).
- Další informace o [plánované údržby pro virtuální počítače s Linuxem v Azure](planned-maintenance.md).
