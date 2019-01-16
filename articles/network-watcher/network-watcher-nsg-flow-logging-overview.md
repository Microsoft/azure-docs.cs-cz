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
ms.openlocfilehash: 06130a5ade63e23fdcd139902a19694a510393a3
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332298"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod k protokolování toků pro skupiny zabezpečení sítě

Protokoly toku skupinu zabezpečení sítě jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Protokoly toku jsou napsané ve formátu JSON a zobrazení odchozí a příchozí toků na základě pravidel na síťové rozhraní (NIC) tok se vztahuje na 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový port a protokol), pokud byl provoz povolený nebo odepřený a ve verzi 2, informace o propustnosti (bajtů a paketů).


![Přehled protokolů toku](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Protokoly toku Nsg cíl, ale není zobrazí stejné jako další protokoly. Protokoly toku se ukládají pouze v rámci účtu úložiště a postupujte podle cesty protokolování je znázorněno v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Můžete analyzovat protokoly toků a získejte přehled o provozu vaší sítě pomocí [traffic analytics](traffic-analytics.md).

Platí stejné zásady uchovávání informací, které jsou viditelné pro ostatní protokoly pro protokoly toků. Zásady uchovávání informací protokolu můžete nastavit od 1 den do 2147483647 dnů. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

> [!NOTE] 
> Funkce zásad uchování pomocí toku NSG protokolování může vést k velkému počtu operací úložiště a souvisejících nákladů. Pokud nechcete, aby funkce zásad uchování, doporučujeme vám, nastavte tuto hodnotu na 0.


## <a name="log-file"></a>Soubor protokolu

Protokoly toku obsahují následující vlastnosti:

* **čas** – čas, kdy se událost byla zaznamenána.
* **ID systému** – ID prostředku. skupiny zabezpečení sítě
* **kategorie** – kategorie události. Do kategorie je vždy **NetworkSecurityGroupFlowEvent**
* **ResourceId** -resource Id skupiny zabezpečení sítě
* **operationName** - Always NetworkSecurityGroupFlowEvents
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
                    * **Provoz rozhodnutí** – ať povolené nebo zakázané přenosy. Platné hodnoty jsou **A** povolena a **D** pro odepřen.
                    * **Stav toku – verze 2 pouze** -zachycuje stav toku. Možné stavy **B**: Proces, při vytváření toku. Nejsou zadány Statistika. **C**: Pokračování pro probíhající toku. Statistiky jsou k dispozici v intervalech 5 minut. **E**: Ukončit, pokud tok je ukončen. Statistiky jsou k dispozici.
                    * **Pakety - zdroje do cíle – verze 2 pouze** celkový počet TCP nebo UDP pakety odeslané ze zdroje do cíle od poslední aktualizace.
                    * **Počet odeslaných bajtů - zdroje do cíle – verze 2 pouze** celkový počet TCP nebo UDP paketů bajtů odeslaných ze zdroje do cíle od poslední aktualizace. Bajtů na paket zahrnout hlavičky paketů a datové části.
                    * **Pakety – cíl a zdroj - verze 2 pouze** celkový počet TCP nebo UDP pakety odeslané z cílového zdroje od poslední aktualizace.
                    * **Počet odeslaných bajtů – cíl a zdroj - verze 2 pouze** celkový počet TCP a UDP paketů bajtů odeslaných z cílového zdroje od poslední aktualizace. Bajtů na paket zahrnout hlavičky paketů a datové části.

## <a name="nsg-flow-logs-version-2"></a>Verze 2 protokolů toku NSG
> [!NOTE] 
> Verze protokoly toku 2 jsou k dispozici pouze v centrální oblasti USA – západ. Povolení verze 2 protokoly v nepodporované oblasti výsledkem bude výstupem do vašeho účtu úložiště protokolů verze 1.

Verze 2 v protokolech představuje stav toku. Můžete nakonfigurovat, kterou verzi protokolů toku se zobrazí. Informace o povolení protokolů toku, najdete v článku [protokolování toků NSG povolení](network-watcher-nsg-flow-logging-portal.md).

Tok stavu *B* zaznamenán, kdy se má aktivovat tok. Tok stavu *C* a stav toku *E* jsou stavy, které označíte pokračování toku a ukončení toku, v uvedeném pořadí. Obě *C* a *E* stavy obsahují informace o přenosech šířky pásma.

Pro pokračování *C* a end *E* stavy toku, počet bajtů a paketu jsou agregace počtů z času předchozí záznam tok řazené kolekce členů. Odkazování na předchozí příklad konverzace, celkový počet paketů přenosu je 1021 + 52 + 8005 + 47 = 9125. Celkový počet bajtů přenesených je 588096 + 29952 + 4610880 + 27072 = 5256000.

**Příklad**: Tok řazenými kolekcemi členů z TCP konverzaci mezi 185.170.185.105:35370 a 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Pro pokračování *C* a end *E* stavy toku, počet bajtů a paketu jsou agregace počtů z času předchozí záznam tok řazené kolekce členů. Odkazování na předchozí příklad konverzace, celkový počet paketů přenosu je 1021 + 52 + 8005 + 47 = 9125. Celkový počet bajtů přenesených je 588096 + 29952 + 4610880 + 27072 = 5256000.

Text, který následuje je příkladem protokolu toku. Jak je vidět, existují více záznamů, které následují v seznamu vlastností je popsáno v předchozí části.

## <a name="nsg-flow-logging-considerations"></a>Důležité informace protokolování toku NSG

**Povolení toku NSG protokolování na všechny skupiny Nsg, které jsou připojené k prostředku**: Tok protokolování v Azure je nakonfigurovaná na prostředek NSG. Tok pouze bude přidružen k jedno pravidlo skupiny zabezpečení sítě. Ve scénářích, kde jsou využívána více skupin zabezpečení sítě, doporučujeme vám, že je povoleno protokolování toků NSG na všechny skupiny Nsg použije zdroje podsíti nebo síťovému rozhraní k zajištění, že veškerý provoz zaznamenán. Zobrazit [jak provoz se vyhodnocuje](../virtual-network/security-overview.md#how-traffic-is-evaluated) Další informace o skupinách zabezpečení sítě. 

**Tok protokolování náklady**: Protokolování toků NSG se fakturuje podle objemu protokoly vytvořené. Vysoký objem přenosů může způsobit velké tok svazek protokolu a související náklady. Protokolů toku NSG Flow ceny nezahrnují základní ceny za úložiště. Funkce zásad uchování pomocí toku NSG protokolování může vést k velkému počtu operací úložiště a souvisejících nákladů. Pokud nechcete, aby funkce zásad uchování, doporučujeme vám, nastavte tuto hodnotu na 0. Zobrazit [ceny služby Network Watcher](https://azure.microsoft.com/en-us/pricing/details/network-watcher/) a [ceny za Azure Storage](https://azure.microsoft.com/en-us/pricing/details/storage/) další podrobnosti.

## <a name="sample-log-records"></a>Záznamy protokolu vzorku

Text, který následuje je příkladem protokolu toku. Jak je vidět, existují více záznamů, které následují v seznamu vlastností je popsáno v předchozí části.


> [!NOTE]
> Hodnoty v **flowTuples* vlastnosti jsou seznam oddělený čárkami.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Ukázka formátu protokolů toku NSG verze 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Ukázka formátu protokolů toku NSG verze 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Další postup

- Informace o povolení protokolů toku, najdete v článku [protokolování toků NSG povolení](network-watcher-nsg-flow-logging-portal.md).
- Další informace o protokolování NSG najdete v tématu [Log analytics pro skupiny zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Pokud chcete zjistit, jestli provoz je povolený nebo zakázaný do nebo z virtuálního počítače, naleznete v tématu [Diagnostika potíží s filtrováním na provoz sítě virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md)
