---
title: Úvod do protokolování toků pro skupin zabezpečení sítě
titleSuffix: Azure Network Watcher
description: Tento článek vysvětluje, jak používat funkci NSG Flow Logs v Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 373a3a66044f996edee904c0073dcb0deb58a85b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277986"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod do protokolování toků pro skupiny zabezpečení sítě

Protokoly toku NSG (Network Security Group) jsou funkce Network Watcher, které vám umožní zobrazit informace o příchozích a odchozích přenosech IP adres prostřednictvím NSG. Protokoly toků jsou napsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní (NIC) tok platí pro 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port a protokol), pokud byl provoz povolený nebo zakázaný, a ve verzi 2, informace o propustnosti (bajty a pakety).


![Přehled protokolů toků](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Zatímco Flow zaznamenává cílový skupin zabezpečení sítě, nezobrazí se stejně jako ostatní protokoly. Protokoly toku se ukládají jenom v rámci účtu úložiště a používají cestu k protokolování, která je znázorněná v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Můžete analyzovat protokoly toků a získat přehled o provozu v síti pomocí [analýzy provozu](traffic-analytics.md).

Stejné zásady uchovávání informací, které se zobrazují pro jiné protokoly, se vztahují na protokoly toků. Můžete nastavit zásady uchovávání protokolů od 1 dne do 2147483647 dnů. Pokud zásady uchovávání nejsou nastavené, protokoly se ukládají navždy.

> [!NOTE] 
> Použití funkce zásady uchovávání informací s protokolováním toku NSG může způsobit velký objem operací úložiště a přidružených nákladů. Pokud nepotřebujete funkci zásad uchovávání informací, doporučujeme nastavit tuto hodnotu na 0.


## <a name="log-file"></a>Soubor protokolu

Protokoly toků obsahují následující vlastnosti:

* **čas** , kdy se událost zaznamenala
* **SystemId** – ID prostředku skupiny zabezpečení sítě.
* **Category** – kategorie události. Kategorie je vždycky **NetworkSecurityGroupFlowEvent**
* **ResourceID** – ID prostředku NSG
* **OperationName** – vždy NetworkSecurityGroupFlowEvents
* **vlastnosti** – kolekce vlastností toku
    * **Verze** čísla verze schématu události protokolu toku
    * **toky** – kolekce toků. Tato vlastnost má pro různá pravidla více záznamů.
        * pravidlo **pravidla** , pro které jsou toky uvedené
            * **toky** – kolekce toků
                * **Mac** – adresa MAC síťové karty pro virtuální počítač, ve kterém se tok shromáždil
                * **flowTuples** – řetězec, který obsahuje více vlastností pro řazenou kolekci členů toku ve formátu odděleném čárkami
                    * **Časové razítko** – tato hodnota představuje časové razítko, kdy se tok objevil ve formátu epocha systému UNIX.
                    * **Zdrojová IP adresa** – zdrojová IP adresa
                    * **Cílová IP adresa** – cílová IP adresa
                    * **Zdrojový port** – zdrojový port
                    * **Cílový port** – cílový port
                    * **Protokol** – protokol toku. Platné hodnoty jsou **T** pro TCP a **u** pro UDP
                    * **Tok přenosů** – směr toku přenosů. Platné hodnoty jsou pro **příchozí a výstupní** **hodnoty pro odchozí** .
                    * **Rozhodnutí o provozu** – zda byl provoz povolen nebo odepřen. Platné **hodnoty jsou pro** povolené a **D** pro zamítnutí.
                    * **Stav toku – pouze verze 2** – zachycuje stav toku. Možné stavy jsou při vytváření toku **B**: begin. Statistiky nejsou k dispozici. **C**: pokračuje se na průběžný tok. Statistika je k dispozici v intervalu 5 minut. **E**: end, když se ukončí tok. Statistiky jsou k dispozici.
                    * **Pakety – zdrojová do cíle – jenom verze 2** Celkový počet paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace.
                    * **Odeslané bajty – pouze zdroj do cíle – verze 2** Celkový počet bajtů paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace. Bajty paketů zahrnují hlavičku paketu a datovou část.
                    * **Pakety – cíl až do source-verze 2** Celkový počet paketů TCP nebo UDP odeslaných z cíle do zdroje od poslední aktualizace.
                    * **Odeslané bajty – cíl ke zdroji – verze 2** Celkový počet bajtů paketů TCP a UDP odeslaných z cíle do zdroje od poslední aktualizace. Bajty paketů zahrnují hlavičku paketu a datovou část.

## <a name="nsg-flow-logs-version-2"></a>Protokoly toku NSG verze 2

Verze 2 protokolů zavádí stav toku. Můžete nakonfigurovat, kterou verzi protokolů Flow obdržíte. Informace o tom, jak povolit protokoly toku, najdete v tématu [Povolení protokolování toku NSG](network-watcher-nsg-flow-logging-portal.md).

Stav toku *B* se zaznamenává při zahájení toku. Stav toku *C* a stav toku *E* jsou stavy, které označují pokračování toku a ukončení toku v uvedeném pořadí. Stavy *C* a *E* obsahují informace o šířce pásma provozu.

**Příklad**: toky řazené kolekce členů z konverzace TCP mezi 185.170.185.105:35370 a 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

Pro pokračování ve stavech *C* a koncových *E* toků jsou počty bajtů a paketů agregované od času předchozího záznamu řazené kolekce členů toku. Po odkazování na předchozí příklad konverzace je celkový počet přenesených paketů 1021 + 52 + 8005 + 47 = 9125. Celkový počet přenesených bajtů je 588096 + 29952 + 4610880 + 27072 = 5256000.

Níže uvedený text je příkladem protokolu toku. Jak vidíte, existuje více záznamů, které následují seznam vlastností popsaný v předchozí části.

## <a name="nsg-flow-logging-considerations"></a>NSG Flow – požadavky na protokolování

**Povolit protokolování toku NSG na všech skupin zabezpečení sítě připojených k prostředku**: protokolování toků v Azure je nakonfigurované na prostředku NSG. Tok bude přidružen pouze k jednomu NSG pravidlu. Ve scénářích, kdy je využíváno více skupin zabezpečení sítě, doporučujeme, aby bylo povoleno protokolování NSG Flow na všech skupin zabezpečení sítě použilicích podsíť prostředku nebo síťové rozhraní, aby se zajistilo, že bude zaznamenáván veškerý provoz. Další informace o skupinách zabezpečení sítě najdete v tématu [o vyhodnocení provozu](../virtual-network/security-overview.md#how-traffic-is-evaluated) . 

**Náklady na protokolování toků**: protokolování toku NSG se účtuje podle objemu vyprodukovaných protokolů. Velký objem přenosů může mít za následek objem protokolu velkého toku a související náklady. Ceny protokolu NSG Flow nezahrnují základní náklady na úložiště. Použití funkce zásady uchovávání informací s protokolováním toku NSG může způsobit velký objem operací úložiště a přidružených nákladů. Pokud nepotřebujete funkci zásad uchovávání informací, doporučujeme nastavit tuto hodnotu na 0. Další podrobnosti najdete v tématu [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) ceny a [ceny Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

> [!IMPORTANT]
> V současné době dochází k potížím s [protokolem toku NSG (Network Security Group)](network-watcher-nsg-flow-logging-overview.md) pro Network Watcher se z úložiště objektů BLOB automaticky neodstraňují na základě nastavení zásad uchovávání informací. Pokud máte existující nenulové zásady uchovávání informací, doporučujeme, abyste pravidelně odstranili objekty blob úložiště, které jsou po dobu jejich uchování, a nemuseli se jim účtovat poplatky. Další informace o tom, jak odstranit blog úložiště protokolu toku NSG, najdete v tématu [odstranění objektů BLOB úložiště protokolu toku NSG](network-watcher-delete-nsg-flow-log-blobs.md).

**Příchozí toky zaznamenané z internetových IP adres do virtuálních počítačů bez veřejných**IP adres: virtuální počítače, které nemají veřejnou IP adresu přiřazenou přes veřejnou IP adresu přidruženou k síťovému rozhraní jako veřejná IP adresa na úrovni instance nebo které jsou součástí základního fondu back-end služby Vyrovnávání zatížení, používají [výchozí SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) a mají IP adresu přiřazenou Azure pro usnadnění odchozího připojení. V důsledku toho může dojít k zobrazení záznamů protokolu toku pro toky z internetových IP adres, pokud je tok určen pro port v rozsahu portů přiřazených pro SNAT. I když Azure nedovolí těmto tokům VIRTUÁLNÍm počítačům, bude se zaprotokolovat a bude se zobrazovat Network Watcher v protokolu NSG toku, který navrhuje. Doporučujeme, aby nevyžádaný příchozí internetový provoz byl explicitně zablokován pomocí NSG.

## <a name="sample-log-records"></a>Ukázkové záznamy protokolu

Níže uvedený text je příkladem protokolu toku. Jak vidíte, existuje více záznamů, které následují seznam vlastností popsaný v předchozí části.


> [!NOTE]
> Hodnoty ve vlastnosti **flowTuples* jsou čárkami oddělený seznam.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Ukázka formátu protokolu toku NSG verze 1
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Ukázka formátu protokolu toku NSG verze 2
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

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak povolit protokoly toku, najdete v tématu [Povolení protokolování toku NSG](network-watcher-nsg-flow-logging-portal.md).
- Informace o tom, jak číst protokoly toku, najdete v tématu [Read log NSG Flow](network-watcher-read-nsg-flow-logs.md).
- Další informace o protokolování NSG najdete v tématu [protokoly pro skupiny zabezpečení sítě (skupin zabezpečení sítě) v protokolu Azure monitor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Informace o tom, jestli je provoz povolený nebo zakázaný z virtuálního počítače, najdete v tématu [Diagnostika problému s filtrem provozu sítě virtuálních počítačů](diagnose-vm-network-traffic-filtering-problem.md) .
