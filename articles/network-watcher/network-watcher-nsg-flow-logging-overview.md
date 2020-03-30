---
title: Úvod do protokolování toku pro nevládní společnosti
titleSuffix: Azure Network Watcher
description: Tento článek vysvětluje, jak používat funkci protokolů toku nsg služby Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228250"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod k protokolování toku pro skupiny zabezpečení sítě

Protokoly toku skupiny zabezpečení sítě (NSG) představují funkci ve službě Network Watcher, která umožňuje zobrazit informace o příchozím a odchozím provozu IP přes NSG. Protokoly toku jsou ve formátu JSON a uvádějí odchozí a příchozí toky pro jednotlivá pravidla, síťové rozhraní (NIC), na které se tok vztahuje, 5člennou řazenou kolekci členů pro daný tok (zdrojová/cílová IP adresa, zdrojový/cílový port a protokol), údaj, jestli byl provoz povolený nebo odepřený, a ve verzi 2 také informace o propustnosti (bajty a pakety).


![přehled protokolů toku](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Zatímco tok protokoly cílnit skupiny nsg, nejsou zobrazeny stejné jako ostatní protokoly. Protokoly toku jsou uloženy pouze v rámci účtu úložiště a postupujte podle cesty protokolování uvedené v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Protokoly toku můžete analyzovat a získat přehled o síťovém provozu pomocí [analýzy provozu](traffic-analytics.md).

Stejné zásady uchovávání informací pro jiné protokoly platí pro protokoly toku. Zásady uchovávání informací protokolu můžete nastavit od 1 dne do 365 dnů. Pokud zásady uchovávání informací není nastavena, protokoly jsou udržovány navždy.

## <a name="log-file"></a>Soubor protokolu

Protokoly toku zahrnují následující vlastnosti:

* **time** - Čas, kdy byla událost zaznamenána
* **systemId** - Id prostředku skupiny zabezpečení sítě.
* **kategorie** - Kategorie události. Kategorie je vždy **NetworkSecurityGroupFlowEvent**
* **resourceid** - ID prostředku skupiny nsg
* **operationName** - Always NetworkSecurityGroupFlowEvents
* **vlastnosti** - Kolekce vlastností toku
    * **Verze** – číslo verze schématu událostí protokolu toku
    * **toky** - Kolekce toků. Tato vlastnost má více položek pro různá pravidla
        * **pravidlo** - pravidlo, pro které jsou uvedeny toky
            * **toky** - sbírka toků
                * **mac** – MAC adresa nic pro virtuální počítač, kde byl shromážděn tok
                * **flowTuples** - Řetězec, který obsahuje více vlastností pro nit křísí toku ve formátu odděleném čárkou
                    * **Časové razítko** - Tato hodnota je časové razítko, kdy došlo k toku ve formátu unixové epochy
                    * **Zdrojová IP** adresa – zdrojová IP adresa
                    * **Cílová IP** adresa – cílová ADRESA IP
                    * **Zdrojový port** – zdrojový port
                    * **Cílový port** – cílový port
                    * **Protokol** - Protokol toku. Platné hodnoty jsou **T** pro TCP a **U** pro UDP
                    * **Dopravní tok** - Směr dopravního toku. Platné hodnoty jsou **I** pro příchozí a **O** pro odchozí.
                    * **Dopravní rozhodnutí** - Zda byl povolen nebo odepřen provoz. Platné hodnoty jsou **A** pro povoleno a **D** pro odepřeno.
                    * **Stav toku – pouze verze 2** – zachycuje stav toku. Možné stavy jsou **B**: Begin, když je vytvořen tok. Statistiky nejsou k dispozici. **C**: Pokračování pro probíhající tok. Statistiky jsou poskytovány v 5minutových intervalech. **E**: Konec, když je tok ukončen. Statistiky jsou poskytovány.
                    * **Pakety - Zdroj do cíle - pouze verze 2** Celkový počet paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace.
                    * **Odeslané bajty – Zdroj do cíle – pouze verze 2** Celkový počet bajtů paketů TCP nebo UDP odeslaných ze zdroje do cíle od poslední aktualizace. Bajty paketů zahrnují hlavičku paketu a datovou část.
                    * **Pakety - cíl ke zdroji - pouze verze 2** Celkový počet paketů TCP nebo UDP odeslaných z cíle do zdroje od poslední aktualizace.
                    * **Odeslané bajty – cíl ke zdroji – pouze verze 2** Celkový počet bajtů paketů TCP a UDP odeslaných z cíle ke zdroji od poslední aktualizace. Bajty paketů zahrnují hlavičku paketů a datová část.

## <a name="nsg-flow-logs-version-2"></a>Protokoly toku nsg verze 2

Verze 2 protokolů zavádí stav toku. Můžete nakonfigurovat, kterou verzi protokolů toku obdržíte. Informace o povolení protokolů toku naleznete [v tématu Povolení protokolování toku toku nsg](network-watcher-nsg-flow-logging-portal.md).

Stav toku *B* se zaznamená při zahájení toku. Stav toku *C* a stav toku *E* jsou stavy, které označují pokračování zakončení toku a toku. Stavy *C* i *E* obsahují informace o šířce pásma provozu.

**Příklad**: Tok řazené kolekce členů z konverzace TCP mezi 185.170.185.105:35370 a 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Pro pokračování *C* a konec *E* toku stavy bajtů a paketů jsou agregační počty z doby předchozího toku n-tice záznamu. Odkazující na předchozí ukázkovou konverzaci je celkový počet přenesených paketů 1021+52+8005+47 = 9125. Celkový počet převedených bajtů je 588096+29952+4610880+27072 = 5256000.

Následující text je příkladem protokolu toku. Jak můžete vidět, existuje více záznamů, které následují seznam vlastností popsaný v předchozí části.

## <a name="nsg-flow-logging-considerations"></a>Důležité informace o protokolování toku nksG

**Důležité informace o účtu úložiště**: 

- Umístění: Použitý účet úložiště musí být ve stejné oblasti jako nsg.
- Vlastní správa střídání klíčů: Pokud změníte nebo otočíte přístupové klíče k účtu úložiště, protokoly toku skupiny NSG přestanou fungovat. Chcete-li tento problém vyřešit, je nutné zakázat a znovu povolit protokoly toku nsg.

**Povolte protokolování toku nsg na všech skupinách zabezpečení sítě připojených k prostředku**: Protokolování toku v Azure je nakonfigurované na prostředku skupiny zabezpečení sítě. Tok bude přidružen pouze k jednomu pravidlu služby NSG. Ve scénářích, kde se využívá více skupin nsg, doporučujeme, aby protokolování toku skupiny zabezpečení sítě bylo povoleno na všech skupinách sítě zabezpečení, které používají podsíť nebo síťové rozhraní prostředku, aby bylo zajištěno, že se zaznamená veškerý provoz. Další informace naleznete v [tématu jak se vyhodnocuje provoz](../virtual-network/security-overview.md#how-traffic-is-evaluated) ve skupinách zabezpečení sítě.

**Náklady na protokolování toku:** Protokolování toku nsg se účtuje na objemu vytvořených protokolů. Vysoký objem provozu může mít za následek velký objem protokolu toku a související náklady. Ceny protokolu toku nsg nezahrnuje základní náklady na úložiště. Použití funkce zásad uchovávání informací s protokolováním toku toku nsg znamená, že vzniknou samostatné náklady na úložiště po delší dobu. Pokud nepožadujete funkci zásad uchovávání informací, doporučujeme nastavit tuto hodnotu na hodnotu 0. Další informace najdete v tématu [Network Watcher Ceny](https://azure.microsoft.com/pricing/details/network-watcher/) a ceny azure storage [pro](https://azure.microsoft.com/pricing/details/storage/) další podrobnosti.

**Příchozí toky protokolované z internetových IP adres do virtuálních počítačů bez veřejných IP adres**: Virtuální počítače, které nemají veřejnou IP adresu přiřazenou přes veřejnou IP adresu přidruženou k nic nebo veřejnou IP adresu na úrovni instance nebo které jsou součástí základního back-endového fondu vyrovnávání zatížení, používají [výchozí SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) a mají IP adresu přiřazenou Azure pro usnadnění odchozího připojení. V důsledku toho se mohou zobrazit položky protokolu toku pro toky z internetových IP adres, pokud je tok určen pro port v rozsahu portů přiřazených pro SNAT. Zatímco Azure nepovolí tyto toky do virtuálního počítače, pokus se zaprotokoluje a zobrazí se v protokolu toku NSG sledovacího programu sítě podle návrhu. Doporučujeme, aby byl nevyžádaný příchozí internetový provoz explicitně blokován pomocí sítě zabezpečení sítě.

**Nesprávný počet bajtů a paketů pro toky bezstavové**: [Skupiny zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) jsou implementovány jako [stavová brána firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Nicméně mnoho výchozí / vnitřní pravidla, která řídí tok provozu jsou implementovány bezstavové způsobem. Z důvodu omezení platformy bajtů a paketů počty nejsou zaznamenány pro bezstavové toky (to znamená, že toky provozu prochází bezstavová pravidla), jsou zaznamenány pouze pro stavové toky. V důsledku toho počet bajtů a paketů hlášených v protokolech toku nsg (a Traffic Analytics) se může lišit od skutečných toků. Toto omezení má být stanoveno do června 2020.

## <a name="sample-log-records"></a>Ukázkové záznamy protokolu

Následující text je příkladem protokolu toku. Jak můžete vidět, existuje více záznamů, které následují seznam vlastností popsaný v předchozí části.


> [!NOTE]
> Hodnoty ve vlastnosti **flowTuples* jsou seznam oddělený čárkou.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Ukázka formátu toku nsg nsg nsg
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Ukázka formátu toku nsg nsg nsg
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

- Informace o povolení protokolů toku naleznete [v tématu Povolení protokolování toku toku nsg](network-watcher-nsg-flow-logging-portal.md).
- Informace o tom, jak číst protokoly toku, naleznete [v tématu Čtení protokolů toku nsg](network-watcher-read-nsg-flow-logs.md).
- Další informace o protokolování skupiny zabezpečení sítě najdete v [tématu protokoly Azure Monitor pro skupiny zabezpečení sítě (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Pokud chcete zjistit, jestli je provoz povolen nebo odepřen virtuálnímu virtuálnímu virtuálnímu síti nebo z ho, přečtěte si informace [o diagnostice problému s filtrem síťového provozu virtuálního měn.](diagnose-vm-network-traffic-filtering-problem.md)
