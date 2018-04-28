---
title: Úvod do toku protokolování pro zabezpečení sítě skupiny s sledovací proces sítě Azure | Microsoft Docs
description: Tento článek vysvětluje, jak používat funkci protokoly toku NSG sledovací proces sítě Azure.
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
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod do toku protokolování pro skupiny zabezpečení sítě

Protokoly toku (NSG) skupiny zabezpečení sítě jsou funkce sledovací proces sítě, která vám umožní zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu NSG. Tok protokoly jsou zapsány ve formátu json a zobrazit příchozí a odchozí toky na základě na pravidlo, toku platí pro síťové rozhraní (NIC), 5 řazené kolekce členů informace o toku (zdrojové nebo cílové adresy IP, portu zdroj nebo cíl a protokolu), a pokud byl provoz povolený nebo zakázaný.

![Přehled protokoly toku](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

I když toku protokoluje cílové skupiny Nsg, nejsou zobrazí stejné jako další protokoly. Tok protokoly se ukládají pouze v rámci účtu úložiště a postupujte podle cesta pro protokolování vidět v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Stejné zásady uchovávání informací pohledu pro další protokoly se vztahuje na protokoly toku. Zásady uchovávání protokolu můžete nastavit od 1 den, do 365 dní. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

## <a name="log-file"></a>Soubor protokolu

Tok protokoly obsahují následující vlastnosti:

* **čas** – čas při protokolu byla zaznamenána událost
* **ID systému** -skupiny zabezpečení sítě ID prostředku.
* **kategorie** – kategorie události. Kategorie je vždy **NetworkSecurityGroupFlowEvent**
* **ResourceId** – prostředek Id NSG
* **operationName** -vždy NetworkSecurityGroupFlowEvents
* **vlastnosti** -kolekci vlastností toku
    * **Verze** -číslo verze schématu toku protokolu událostí
    * **toky** -kolekce toků. Tato vlastnost má několik záznamů pro různá pravidla
        * **pravidlo** -pravidla pro tyto toky jsou uvedeny
            * **toky** -kolekce toků
                * **Mac** – adresa MAC síťového adaptéru pro virtuální počítač, kde byl shromážděn toku
                * **flowTuples** -řetězec, který obsahuje více vlastností řazené kolekce členů toku ve formátu čárkami
                    * **Časové razítko** – tato hodnota je časové razítko, když toku došlo k chybě ve formátu UNIX EPOCH
                    * **Zdrojová adresa IP** -zdrojové IP adresy
                    * **Cílové IP** -cílovou IP adresu
                    * **Zdrojový Port** -zdrojový port
                    * **Cílový Port** – cílový Port
                    * **Protokol** -protokol toku. Platné hodnoty jsou **T** pro TCP a **U** pro UDP
                    * **Tok provozu** -směr toku přenosu. Platné hodnoty jsou **I** pro příchozí a **O** pro odchozí.
                    * **Provoz** – ať povolené nebo zakázané přenosy. Platné hodnoty jsou **A** pro povolené a **D** pro odepřen.

Text, který následuje je příklad toku protokolu. Jak vidíte, existuje více záznamů, které následují seznam vlastností, které jsou popsané v předchozí části.

> [!NOTE]
> Hodnoty v **flowTuples* vlastnost jsou seznam oddělený čárkami.
 
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

## <a name="next-steps"></a>Další postup

- Informace o povolení toku protokolů najdete v tématu [NSG povolení protokolování toku](network-watcher-nsg-flow-logging-portal.md).
- Další informace o protokolování NSG najdete v tématu [protokolu analýzy pro skupiny zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Chcete-li zjistit, jestli provoz je povolený nebo zakázaný do nebo z virtuálního počítače, přečtěte si téma [diagnostikovat problém filtru provoz sítě virtuálních počítačů](diagnose-vm-network-traffic-filtering-problem.md)