---
title: Úvod do protokolování toků pro skupin zabezpečení sítě
titleSuffix: Azure Network Watcher
description: Tento článek vysvětluje, jak používat funkci NSG Flow Logs v Azure Network Watcher.
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
ms.openlocfilehash: 792908236c4f240db64bd3899474d779d5b0570c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018099"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Úvod do protokolování toků pro skupiny zabezpečení sítě

## <a name="introduction"></a>Úvod

Protokoly toku pro [skupinu zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) je funkce služby Azure Network Watcher, která umožňuje protokolovat informace o provozu protokolu IP prostřednictvím NSG. Data toku se odesílají do Azure Storage účtů, kde k nim máte přístup, a také je exportovat do libovolného nástroje pro vizualizaci, SIEM nebo ID podle vašeho výběru.

![Přehled protokolů toků](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Proč používat protokoly toku?

Je důležité monitorovat, spravovat a znát vaši vlastní síť, aby nedošlo k ohrožení zabezpečení, dodržování předpisů a výkonu. Znalost vlastního prostředí má pro ochranu a optimalizaci nejdůležitější důležitost. Často potřebujete znát aktuální stav sítě, který se připojuje, odkud se připojují, které porty jsou otevřené v Internetu, očekávané chování sítě, nepravidelné síťové chování a náhlé zvýšení provozu.

Protokoly toku jsou zdrojem pravdy pro všechny síťové aktivity ve vašem cloudovém prostředí. Bez ohledu na to, jestli se chystáte začít při pokusu o optimalizaci prostředků nebo velké firmy, které se snaží detekovat neoprávněné vniknutí, jsou protokoly toků nejlepší. Můžete ji použít pro optimalizaci toků sítě, monitorování propustnosti, ověřování dodržování předpisů, zjišťování neoprávněných vniknutí a dalších.

## <a name="common-use-cases"></a>Běžné případy použití

**Monitorování sítě**: Identifikujte neznámý nebo nepožadovaný provoz. Monitorujte úrovně provozu a využití šířky pásma. Filtrování protokolů toku podle IP adresy a portu pro pochopení chování aplikace Exportujte protokoly toků do nástrojů pro analýzu a vizualizaci podle vašeho výběru a nastavte řídicí panely pro monitorování.

**Monitorování a optimalizace využití:** Identifikujte hlavní televizní přijímače ve vaší síti. Zkombinujte s GeoIP daty k identifikaci provozu mezi oblastmi. Pochopení nárůstu provozu při prognózování kapacity Pomocí dat odeberte pravidla overtly omezující provoz.

**Dodržování předpisů**: pomocí dat toku ověříte izolaci sítě a dodržování předpisů pomocí pravidel podnikového přístupu.

**Analýza zabezpečení forenzní & sítě**: analýza toků sítě před napadenými IP adresami a síťovými rozhraními Exportujte protokoly toku na libovolný SIEM nebo libovolný nástroj podle vašeho výběru.

## <a name="how-logging-works"></a>Jak funguje protokolování

**Vlastnosti klíče**

- Protokoly toku pracují ve [vrstvě 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) a zaznamenávají všechny toky IP, které přecházejí z NSG.
- Protokoly se shromažďují prostřednictvím platformy Azure a jakýmkoli způsobem neovlivňují prostředky zákazníků ani výkon sítě.
- Protokoly se zapisují ve formátu JSON a zobrazují odchozí a příchozí toky podle NSG pravidla.
- Každý záznam protokolu obsahuje síťové rozhraní (NIC), na které se tok vztahuje, informace o 5-tice, rozhodnutí o provozu & (jenom verze 2) informace o propustnosti. Úplné podrobnosti najdete v níže uvedeném _formátu protokolu_ .
- Protokoly toku mají funkci uchovávání, která umožňuje automaticky odstranit protokoly až do roku po jejich vytvoření. 

> [!NOTE]
> Uchovávání informací je k dispozici pouze v případě, že používáte [účty úložiště pro obecné účely v2 (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts). 

**Základní koncepty**

- Softwarově definované sítě jsou seřazené kolem virtuálních sítí (virtuální sítě) a podsítí. Zabezpečení těchto virtuální sítě a podsítí se dá spravovat pomocí NSG.
- Skupina zabezpečení sítě (NSG) obsahuje seznam _pravidel zabezpečení_ , která povolují nebo zakazují síťový provoz v prostředcích, ke kterým je připojen. Skupin zabezpečení sítě je možné přidružit k podsítím, jednotlivým virtuálním počítačům nebo jednotlivým síťovým rozhraním (NIC) připojeným k virtuálním počítačům (Správce prostředků). Další informace najdete v tématu [Přehled skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json).
- Všechny toky provozu ve vaší síti se vyhodnocují pomocí pravidel v příslušných NSG.
- Výsledkem těchto hodnocení je NSG Flow log. Protokoly toků se shromažďují prostřednictvím platformy Azure a nevyžadují žádnou změnu prostředků zákazníka.
- Poznámka: pravidla jsou dvou typů – ukončení & neukončujících, z nichž každá má jiné chování protokolování.
- - NSG se pravidla zamítnutí. NSG, který zakazuje provoz, se zaprotokoluje v protokolech Flow a zpracování v tomto případě se zastaví po jakémkoli NSG zamítnutí provozu. 
- - Pravidla povolení NSG jsou neukončující, což znamená, že i když jeden NSG umožňuje, bude zpracování pokračovat dalším NSG. Poslední NSG, který povoluje provoz, zaprotokoluje provoz do protokolů Flow.
- Protokoly toku NSG se zapisují do účtů úložiště, ze kterých se dají dostat.
- Protokoly toku můžete exportovat, zpracovávat, analyzovat a vizualizovat pomocí nástrojů, jako jsou TA, Splunk, Grafana, Stealthwatch atd.

## <a name="log-format"></a>Formát protokolu

Protokoly toků obsahují následující vlastnosti:

* **čas** , kdy se událost zaznamenala
* **SystemId** – ID systému skupiny zabezpečení sítě.
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


**Protokoly toku NSG verze 2 (vs verze 1)** 

Verze 2 protokolů zavádí koncept stavu toku. Můžete nakonfigurovat, kterou verzi protokolů Flow obdržíte.

Stav toku _B_ se zaznamenává při zahájení toku. Stav toku _C_ a stav toku _E_ jsou stavy, které označují pokračování toku a ukončení toku v uvedeném pořadí. Stavy _C_ a _E_ obsahují informace o šířce pásma provozu.

### <a name="sample-log-records"></a>Ukázkové záznamy protokolu

Níže uvedený text je příkladem protokolu toku. Jak vidíte, existuje více záznamů, které následují seznam vlastností popsaný v předchozí části.

> [!NOTE]
> Hodnoty ve vlastnosti *flowTuples* jsou čárkami oddělený seznam.
 
**Ukázka formátu protokolu toku NSG verze 1**
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
        
        
```
**Ukázka formátu protokolu toku NSG verze 2**
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
        }
        
```
**Vysvětlení řazené kolekce členů protokolu**

![tok zaznamenává řazenou kolekci členů](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Ukázka výpočtu šířky pásma**

Toky řazené kolekce členů z konverzace TCP mezi 185.170.185.105:35370 a 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

Pro pokračování ve stavech _C_ a koncových _E_ toků jsou počty bajtů a paketů agregované od času předchozího záznamu řazené kolekce členů toku. Po odkazování na předchozí příklad konverzace je celkový počet přenesených paketů 1021 + 52 + 8005 + 47 = 9125. Celkový počet přenesených bajtů je 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Povolení protokolů toků NSG

K povolení protokolů toku použijte odpovídající odkaz níže.

- [Azure Portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [Rozhraní příkazového řádku](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Aktualizují se parametry

**Azure Portal**

V Azure Portal přejděte do části protokoly toku NSG v Network Watcher. Pak klikněte na název NSG. Tím se zobrazí podokno nastavení protokolu toku. Změňte parametry, které chcete, a potom klikněte na **Uložit** , aby se změny nasadily.

**PS/CLI/REST/ARM**

K aktualizaci parametrů prostřednictvím nástrojů příkazového řádku použijte stejný příkaz, který se používá k povolení protokolů toku (výše), ale s aktualizovanými parametry, které chcete změnit.

## <a name="working-with-flow-logs"></a>Práce s protokoly toku

*Čtení a export protokolů toků*

- [Stáhnout &amp; protokoly toku zobrazení z portálu](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Čtení protokolů toku pomocí funkcí PowerShellu](./network-watcher-read-nsg-flow-logs.md)
- [Exportovat protokoly toku NSG do Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Zatímco Flow zaznamenává cílový skupin zabezpečení sítě, nezobrazí se stejně jako ostatní protokoly. Protokoly toku se ukládají jenom v rámci účtu úložiště a používají cestu k protokolování, která je znázorněná v následujícím příkladu:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Vizualizace protokolů toku*

- [Azure Traffic Analytics](./traffic-analytics.md) je nativní služba Azure, která zpracovává protokoly toků, extrahuje přehledy a vizualizuje protokoly toků. 
- [Návodu Vizualizace protokolů toku NSG pomocí Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Návodu Vizualizace protokolů toku NSG pomocí elastického zásobníku](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Návodu Správa a analýza protokolů toku NSG pomocí Grafana](./network-watcher-nsg-grafana.md)
- [Návodu Správa a analýza protokolů toku NSG pomocí Graylogu](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>NSG Flow – požadavky na protokolování

**Požadavky na účet úložiště**: 

- Umístění: použitý účet úložiště musí být ve stejné oblasti jako NSG.
- Samoobslužná správa střídání klíčů: Pokud změníte nebo otočíte přístupové klíče k vašemu účtu úložiště, protokoly toku NSG přestanou fungovat. Chcete-li tento problém vyřešit, je nutné zakázat a znovu povolit protokoly toku NSG.

**Náklady na protokolování toků**: protokolování toku NSG se účtuje podle objemu vyprodukovaných protokolů. Velký objem přenosů může mít za následek objem protokolu velkého toku a související náklady. Ceny protokolu NSG Flow nezahrnují základní náklady na úložiště. Použití funkce zásady uchovávání informací s protokolováním toku NSG znamená, že se za delší dobu účtují samostatné náklady na úložiště. Pokud nepotřebujete funkci zásad uchovávání informací, doporučujeme nastavit tuto hodnotu na 0. Další informace najdete v tématu [Network Watcher ceny](https://azure.microsoft.com/pricing/details/network-watcher/) a [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/) pro další podrobnosti.

**Problémy s uživatelem definovanými příchozími pravidly TCP**: [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/network-security-groups-overview.md) jsou implementované jako [stavová brána firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Vzhledem k aktuálním omezením platforem však uživatelsky definovaná pravidla, která mají vliv na příchozí toky TCP, jsou implementována bezstavovým způsobem. Z tohoto důvodu toky ovlivněné uživatelem definovanými vstupními pravidly se stanou neukončujícími. Pro tyto toky se navíc nezaznamenávají počty bajtů a paketů. V důsledku toho by se počet bajtů a paketů hlášených v protokolech toku NSG (a Analýza provozu) lišil od skutečných čísel. Příznak výslovného souhlasu, který tyto problémy řeší, je naplánován k dispozici do prosince 2020 nejnovějšího. V předběžně se zákazníci, kteří čelí vážným problémům z důvodu tohoto chování, můžou požádat o zaznamenání prostřednictvím podpory, vyvolejte prosím žádost o podporu v části Network Watcher > protokoly toku NSG.  

**Příchozí toky zaznamenané z internetových IP adres do virtuálních počítačů bez veřejných** IP adres: virtuální počítače, které nemají veřejnou IP adresu přiřazenou přes veřejnou IP adresu přidruženou k síťovému rozhraní jako veřejná IP adresa na úrovni instance nebo které jsou součástí základního fondu back-end služby Vyrovnávání zatížení, používají [výchozí SNAT](../load-balancer/load-balancer-outbound-connections.md) a mají IP adresu přiřazenou Azure pro usnadnění odchozího připojení. V důsledku toho může dojít k zobrazení záznamů protokolu toku pro toky z internetových IP adres, pokud je tok určen pro port v rozsahu portů přiřazených pro SNAT. I když Azure nedovolí těmto tokům VIRTUÁLNÍm počítačům, bude se zaprotokolovat a bude se zobrazovat Network Watcher v protokolu NSG toku, který navrhuje. Doporučujeme, aby nevyžádaný příchozí internetový provoz byl explicitně zablokován pomocí NSG.

**Nekompatibilní služby**: kvůli současným omezením platforem není v protokolech toku NSG podporována malá sada služeb Azure. Aktuální seznam nekompatibilních služeb je
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Osvědčené postupy

**Povolit pro kritické virtuální sítě/podsítě**: protokoly toků by měly být povolené na všech kritických virtuální sítě/podsítích ve vašem předplatném jako osvědčený postup pro audit a zabezpečení. 

**Povolit protokolování toku NSG na všech skupin zabezpečení sítě připojených k prostředku**: protokolování toků v Azure je nakonfigurované na prostředku NSG. Tok bude přidružen pouze k jednomu NSG pravidlu. Ve scénářích, kdy je využíváno více skupin zabezpečení sítě, doporučujeme povolit protokoly toků NSG na všech skupin zabezpečení sítě použitých v podsíti prostředku nebo síťové rozhraní, aby se zajistilo, že bude zaznamenáván veškerý provoz. Další informace najdete v tématu [jak se vyhodnocuje provoz](../virtual-network/network-security-group-how-it-works.md) ve skupinách zabezpečení sítě.

**Zřizování úložiště**: úložiště by mělo být zřízené ve službě Intune s očekávaným objemem protokolu toku.

## <a name="troubleshooting-common-issues"></a>Řešení běžných potíží

**Nepodařilo se mi povolit protokoly toku NSG**

- Poskytovatel prostředků **Microsoft. Insights** není zaregistrovaný.

Pokud dojde k chybě _AuthorizationFailed_ nebo _GatewayAuthenticationFailed_, pravděpodobně jste ve svém předplatném nepovolili poskytovatele prostředků Microsoft Insights. Pokud chcete poskytovatele Microsoft Insights povolit, [postupujte podle pokynů](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) .

**Po povolení protokolů toku NSG se v účtu úložiště nezobrazují žádná data**

- **Čas nastavení**

Zobrazení protokolů toku NSG v účtu úložiště může trvat až 5 minut (v případě správné konfigurace). Zobrazí se soubor PT1H.json, který můžete otevřít podle [těchto pokynů](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Žádný provoz ve skupinách zabezpečení sítě**

Protokoly se někdy nezobrazí, protože vaše virtuální počítače nejsou aktivní nebo ve službě App Gateway nebo na jiných zařízeních existují nadřazené filtry, které blokují provoz do vašich skupin zabezpečení sítě.

**Chci automatizovat protokoly toku NSG**

Protokoly toku NSG v současné době nepodporují automatizaci prostřednictvím šablon ARM. Další informace najdete v [oznámení o funkci](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) .

## <a name="faq"></a>Časté otázky

**Co dělají protokoly toku NSG?**

Síťové prostředky Azure je možné kombinovat a spravovat prostřednictvím [skupin zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/network-security-groups-overview.md). Protokoly toku NSG umožňují protokolovat informace o toku 5-řazené kolekce členů o všech přenosech prostřednictvím služby skupin zabezpečení sítě. Protokoly nezpracovaných toků se zapisují na účet Azure Storage, ze kterého se dají dál zpracovávat, analyzovat, dotazovat nebo exportovat podle potřeby.

**Ovlivňují používání protokolů Flow latenci sítě nebo výkon?**

Data protokolů toků se shromažďují mimo cestu síťového provozu, a proto neovlivňují propustnost sítě ani latenci. Můžete vytvářet nebo odstraňovat protokoly toků bez rizika dopadu na výkon sítě.

**Návody používat protokoly toku NSG s účtem úložiště za bránou firewall?**

Pokud chcete použít účet úložiště za bránou firewall, musíte poskytnout výjimku pro důvěryhodné služby Microsoftu pro přístup k vašemu účtu úložiště:

- Přejděte do účtu úložiště zadáním názvu účtu úložiště do globálního vyhledávání na portálu nebo na [stránce účty úložiště](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) .
- V části **Nastavení** vyberte **brány firewall a virtuální sítě** .
- V nástroji **Povolení přístupu z** vyberte  **vybrané sítě**. Pak v části  **výjimky** zaškrtněte políčko vedle položky * * * * umožňuje důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště * * * *
- Pokud je tato možnost již vybraná, není potřeba provádět žádné změny.
- Na [stránce Přehled protokolů toků NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) vyhledejte cílovou NSG a povolte protokoly toku NSG s vybraným účtem úložiště.

Po několika minutách můžete zkontrolovat protokoly úložiště, ve kterých by se mělo zobrazit aktualizované časové razítko nebo nově vytvořený soubor JSON.

**Návody používat protokoly toku NSG s účtem úložiště za koncovým bodem služby?**

Protokoly toku NSG jsou kompatibilní s koncovými body služby bez nutnosti jakékoli další konfigurace. Přečtěte si [kurz povolení koncových bodů služby](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) ve vaší virtuální síti.

**Jaký je rozdíl mezi protokoly toku verze 1 & 2?**

Protokoly Flow verze 2 zavádí koncept _stavu toku_ , & ukládá informace o odeslaných bajtech a paketech. [Další informace](#log-format)

## <a name="pricing"></a>Ceny

Protokoly toku NSG se účtují za GB shromážděných protokolů a mají bezplatnou úroveň 5 GB/měsíc na jedno předplatné. Aktuální ceny ve vaší oblasti najdete na [stránce s cenami Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/).

Ukládání protokolů se účtuje samostatně, další informace najdete v tématu [Azure Storage cenové stránky objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) pro příslušné ceny.
