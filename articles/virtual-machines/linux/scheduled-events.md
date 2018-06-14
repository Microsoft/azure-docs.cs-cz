---
title: Naplánované události pro virtuální počítače s Linuxem v Azure | Microsoft Docs
description: Naplánovat událostí pomocí služby Azure metadat pro virtuální počítače Linux.
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
ms.openlocfilehash: db4a0d1f288394276cd400e7a060cfb3662b34f0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911994"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Služba Azure Metadata: Naplánované události pro virtuální počítače s Linuxem

Naplánované události je služba Azure Metadata, která dává vaší aplikace času Příprava pro virtuální počítač (VM) údržby. Poskytuje informace o události nadcházející údržby (například restartování počítače), aby vaše aplikace můžete připravit pro ně a omezit přerušení. Je k dispozici pro všechny typy virtuálních počítačů Azure, včetně PaaS a IaaS v systému Windows a Linux. 

Informace o naplánované události v systému Windows najdete v tématu [naplánované události pro virtuální počítače Windows](../windows/scheduled-events.md).

> [!Note] 
> Naplánované události je všeobecně dostupná v všech oblastech Azure. V tématu [verze a dostupnost v oblastech](#version-and-region-availability) pro nejnovější informace o verzi.

## <a name="why-use-scheduled-events"></a>Proč používat naplánované události?

Mnoho aplikací můžete těžit z času na přípravu pro údržbu virtuálních počítačů. Čas lze použít k provedení úlohy specifické pro aplikace, které zlepšení dostupnosti, spolehlivosti a použitelnost, včetně: 

- Kontrolní bod a obnovení.
- Připojení vyprazdňování.
- Převzetí služeb při selhání primární repliky.
- Odebrání z fondu vyrovnávání zatížení.
- Protokolování událostí.
- Řádné vypnutí.

S naplánované události vaše aplikace může zjistit, když bude údržby dojít a aktivuje úlohy omezit její vliv.  

Naplánované události poskytuje události v následujících případech použití:

- Platforma spouštěná údržby (například aktualizace hostitelský operační systém)
- Uživatel spustil údržby (například uživatele restartuje nebo opětovně nasadí virtuální počítač)

## <a name="the-basics"></a>Základní informace  

  Metadata služby zpřístupní informace o spuštěných virtuálních počítačů pomocí koncový bod REST, který je přístupný z virtuálního počítače. Informace k dispozici prostřednictvím nepoužívající IP, takže není nezveřejní virtuálního počítače.

### <a name="scope"></a>Rozsah
Naplánované události budou doručeny do:

- Všechny virtuální počítače v cloudové službě.
- Všechny virtuální počítače v nastavení dostupnosti.
- Všechny virtuální počítače ve skupině umístění sady škálování. 

Výsledkem je, zkontrolujte `Resources` pole v události pro identifikaci, které virtuální počítače se vztahuje.

### <a name="endpoint-discovery"></a>Koncový bod zjišťování
Pro virtuální síť povoleno virtuální počítače, služby metadat je k dispozici z IP adresy statické nepoužívající `169.254.169.254`. Úplné koncový bod pro nejnovější verzi naplánované události je: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Pokud virtuální počítač není vytvořena v rámci virtuální sítě, výchozí případů pro cloudové služby a klasické virtuální počítače, je potřeba další logiku zjistit adresu IP. Další postupy [zjistit koncový bod hostitele](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), najdete v této ukázce.

### <a name="version-and-region-availability"></a>Verze a dostupnost v oblastech
Služba naplánované události je verzí. Verze jsou povinné; aktuální verze je `2017-08-01`.

| Verze | Typ verze | Oblasti | Poznámky k verzi | 
| - | - | - | - | 
| 2017-08-01 | Všeobecná dostupnost | Vše | <li> Přidá jako předpona podtržítka odeberou názvy prostředků pro virtuální počítače Iaas<br><li>Metadata hlavičky požadavku vynucuje pro všechny požadavky | 
| 2017-03-01 | Preview | Vše | <li>Původní vydaná verze


> [!NOTE] 
> Předchozí verze preview naplánované události podporované {nejnovější} jako verze rozhraní api. Tento formát se už nepodporuje a bude v budoucnu zastaralá.

### <a name="enabling-and-disabling-scheduled-events"></a>Povolení a zakázání naplánované události
Naplánované události je povolený pro vaše služba první době může požádat o události. Zpožděné odpověď by měl očekávat při první volání až dvě minuty.

Naplánované události je zakázána pro vaši službu, pokud není ho odešlete požadavek na 24 hodin.

### <a name="user-initiated-maintenance"></a>Uživatel spustil údržby
Uživatel spustil údržby virtuálního počítače přes portál Azure, rozhraní API, rozhraní příkazového řádku nebo prostředí PowerShell výsledkem plánovaná událost. Potom můžete otestovat logiku přípravy údržby v aplikaci a připravit aplikace spouštěné uživateli údržby.

Pokud se je restartovat virtuální počítač, událost s typem `Reboot` je naplánováno. Pokud jste znovu nasadit virtuální počítač, událost s typem `Redeploy` je naplánováno.

## <a name="use-the-api"></a>Použít rozhraní API

### <a name="headers"></a>Záhlaví
Když dotazujete Metadata služby, je nutné zadat hlavičku `Metadata:true` zajistit požadavek nebyl přesměrován náhodně. `Metadata:true` Záhlaví je povinný u všech požadavků naplánované události. Zahrnout záhlaví v žádosti se nezdařilo výsledkem "Chybný požadavek" odpověď z metadat služby.

### <a name="query-for-events"></a>Dotaz pro události
Tím, že toto volání se můžete dotazovat pro naplánované události:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Odpověď obsahuje pole naplánované události. Prázdné pole znamená, že aktuálně nejsou naplánovány žádné události.
V případě, kde je naplánované události, odpověď obsahuje řadu událostí. 
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
| EventType | Dopad, který způsobí, že tato událost. <br><br> Hodnoty: <br><ul><li> `Freeze`: Virtuálního počítače je naplánováno se pozastavit několik sekund. Procesor je pozastavená, ale neexistuje žádný vliv na paměti, otevřených souborů nebo připojení k síti. <li>`Reboot`: Restartování je naplánováno virtuálního počítače. (Zajišťováno paměti je ztraceno.) <li>`Redeploy`: Oprava virtuální počítač přesunout do jiného uzlu. (Dočasné disky jsou ztraceny). |
| ResourceType | Typ prostředku, který má vliv na tuto událost. <br><br> Hodnoty: <ul><li>`VirtualMachine`|
| Zdroje a prostředky| Seznam prostředků, které má vliv na tuto událost. V seznamu záruku, že se tak, aby obsahovala počítačů z maximálně jeden [aktualizace domény](manage-availability.md), ale nemusí obsahovat všechny počítače ve UD. <br><br> Příklad: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stav této události. <br><br> Hodnoty: <ul><li>`Scheduled`: Tato událost je naplánováno spuštění po dobu uvedenou v `NotBefore` vlastnost.<li>`Started`: Tato událost byla spuštěna.</ul> Ne `Completed` nebo podobné stav je někdy k dispozici. Událost se už vrátí po dokončení události.
| Neplatí před| Doba, po jejímž uplynutí může spustit tuto událost. <br><br> Příklad: <br><ul><li> MON 19 září 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Plánování událostí
Každá událost je naplánováno minimální množství času v budoucnu podle typu události. Tentokrát se odrazí v události `NotBefore` vlastnost. 

|EventType  | Minimální oznámení |
| - | - |
| Zablokování| 15 minut |
| Restartování | 15 minut |
| Opětovné nasazení | 10 minut |

### <a name="start-an-event"></a>Událost spuštění 

Po další nadcházející události a dokončení logika pro řádné vypnutí, můžete schválit nevyřízené události tak, že `POST` volání na Metadata služby s `EventId`. Toto volání do Azure označuje, že ho zmenšit minimální oznámení čas (Pokud je to možné). 

Následující ukázka JSON je očekávána v `POST` text žádosti. Žádost musí obsahovat seznam `StartRequests`. Každý `StartRequest` obsahuje `EventId` pro událost, kterou chcete urychlit:
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
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> To v úvahu událost umožňuje událostí, aby bylo možné pokračovat pro všechny `Resources` v případě, že, nejen virtuálních počítačů, které uznává události. Proto můžete zvolit vedoucí ke koordinaci potvrzení, což může být stejně jednoduché jako první počítač v `Resources` pole.

## <a name="python-sample"></a>Ukázka Pythonu 

Následující ukázka dotazuje služba metadat pro naplánované události a schválí všechny nevyřízené události:

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
- Kukátko [naplánované události na Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) zobrazíte ukázku. 
- Přečtěte si ukázky kódu naplánované události v [úložiště Azure Instance Metadata naplánované události Github](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Další informace o rozhraní API, které jsou k dispozici v [Instance Metadata služby](instance-metadata-service.md).
- Další informace o [plánované údržby pro virtuální počítače s Linuxem v Azure](planned-maintenance.md).
