---
title: Úvod k protokolování toků pro zabezpečení sítě skupin pomocí služby Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak používat funkci protokolů toku NSG Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ae4edb82fa5e192a30d297dae82199bb7efca0c2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344967"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod k protokolování toků pro skupiny zabezpečení sítě

Protokoly toku skupinu zabezpečení sítě jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Protokolů toku jsou napsané ve formátu json a zobrazit toky odchozí a příchozí pravidlo na základě, tok se vztahuje na síťové rozhraní (NIC), 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový port a protokol), a pokud byl provoz Povolí nebo zakáže.

![Přehled protokolů toku](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Protokoly toku Nsg cíl, ale není zobrazí stejné jako další protokoly. Protokoly toku se ukládají pouze v rámci účtu úložiště a postupujte podle cesty protokolování je znázorněno v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Platí stejné zásady uchovávání informací, které jsou viditelné pro ostatní protokoly pro protokoly toků. Zásady uchovávání informací protokolu můžete nastavit od 1 den do 2147483647 dnů. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

## <a name="log-file"></a>Soubor protokolu

Protokoly toku obsahují následující vlastnosti:

* **čas** – čas, kdy se událost byla zaznamenána.
* **ID systému** – ID prostředku. skupiny zabezpečení sítě
* **kategorie** – kategorie události. Do kategorie je vždy **NetworkSecurityGroupFlowEvent**
* **ResourceId** -resource Id skupiny zabezpečení sítě
* **operationName** -vždy NetworkSecurityGroupFlowEvents
* **vlastnosti** – kolekce vlastností toku
    * **Verze** – číslo verze schéma událostí protokolu toku
    * **toky** – kolekce toků. Tato vlastnost obsahuje několik záznamů pro různá pravidla
        * **pravidlo** -pravidlo pro které jsou uvedeny toků
            * **toky** – kolekce toků
                * **Mac** – adresa MAC síťové karty pro virtuální počítač, ve kterém byl shromážděn toku
                * **flowTuples** – řetězec, který obsahuje více vlastností pro tok řazené kolekce členů ve formátu odděleném čárkami
                    * **Časové razítko** – tato hodnota je časové razítko, kdy došlo k chybě toku ve formátu UNIX EPOCHA
                    * **Zdrojová IP adresa** -Zdrojová IP adresa
                    * **Cíl IP** – cílová IP adresa
                    * **Zdrojový Port** – zdrojový port
                    * **Cílový Port** – cílový Port
                    * **Protokol** -protokolu toku. Platné hodnoty jsou **T** pro protokol TCP a **U** pro UDP
                    * **Přenosy dat** -směr toku provozu. Platné hodnoty jsou **můžu** pro příchozí a **O** pro odchozí.
                    * **Provoz** – ať povolené nebo zakázané přenosy. Platné hodnoty jsou **A** povolena a **D** pro odepřen.

Text, který následuje je příkladem protokolu toku. Jak je vidět, existují více záznamů, které následují v seznamu vlastností je popsáno v předchozí části.

> [!NOTE]
> Hodnoty v **flowTuples* vlastnosti jsou seznam oddělený čárkami.
 
```json
{
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Další kroky

- Informace o povolení protokolů toku, najdete v článku [protokolování toků NSG povolení](network-watcher-nsg-flow-logging-portal.md).
- Další informace o protokolování NSG najdete v tématu [Log analytics pro skupiny zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Pokud chcete zjistit, jestli provoz je povolený nebo zakázaný do nebo z virtuálního počítače, naleznete v tématu [Diagnostika potíží s filtrováním na provoz sítě virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md)
