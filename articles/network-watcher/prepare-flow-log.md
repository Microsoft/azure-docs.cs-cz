---
title: Změnami protokolu toku skupin zabezpečení sítě Azure | Dokumentace Microsoftu
description: Další informace o změnami protokolu toku skupin zabezpečení sítě Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180739"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Protokoly toku skupin zabezpečení sítě – verze 2 – nadcházející změny

Změna formátu protokoly sítě zabezpečení skupiny tok od 15. září 2018. Přidaná pole poskytují informace o toku, státní i přírůstkové počet bajtů a v každém směru přenosu paketů. Aplikace, které analýzou protokolů toku tato změna ovlivní a muset podle nich aktualizuje. Tento článek popisuje, jak o změnu.

## <a name="what-is-changing"></a>Co se mění?

Verze protokoly toků skupin zabezpečení sítě se změní z "Verze": 1 "Verze": 2, se další pole, které jsou přidány do *flowTuples* vlastnost v protokolech. Podrobnosti o formátu jsou k dispozici v [jak tok modelována ve verzi 2](#how-is-a-flow-modeled-in-version-2).

### <a name="version-1-flow-tuple-format"></a>Formát verze 1 tok řazené kolekce členů

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>Formát řazené kolekce členů tok verze 2

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>Když se tato změna proběhnout?

Tato změna přejde do začátku efekt **15. září 2018** v oblasti střed USA – západ. Zavádění změn ve veřejných cloudových oblastech dokončí podle dne 31. května 2018, po které pouze verze 2 protokoluje bude k dispozici.

## <a name="am-i-affected-by-the-change"></a>Jsem změna ovlivňuje?

Pokud sestavení nebo použít aplikaci, která zpracovává data protokolu toku skupiny zabezpečení sítě mohou být ovlivněny tuto změnu.

### <a name="if-i-use-traffic-analytics"></a>Pokud mám použít analýzu provozu?

Ne. Analýza provozu nebude mít vliv při přechodu z verze 1 na protokolování toků verze 2. Vylepšení už brzo bude využívat další relace a šířky pásma informací uvedených v protokolů verze 2 toku.

### <a name="if-im-using-third-party-tooling"></a>Pokud používám nástrojů třetích stran?

Změna ve formátu protokolu, má se všemi předem poskytnuty [sledovací proces sítě partnerů](https://azure.microsoft.com/services/network-watcher). Kontaktujte dodavatele podrobnosti.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Pokud vytvořil(a) jsem si vlastní aplikaci nebo integrace?

**Ano**, budete muset upravit svou aplikaci k procesu protokolů toku NSG v novém formátu.

## <a name="what-is-the-new-flow-logging-format"></a>Co je nového formátu protokolování toku?

Verze protokoly toku 2 obsahuje tok řazených kolekcí členů v následujícím formátu:

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

Následující tabulka obsahuje názvy vlastností a popisy pro sítě zabezpečení skupiny verze 2 tok řazené kolekce členů. Záznamy úplnou ukázku najdete v [událost vzorku pro verzi 2](#version2sampleevent).

| Název vlastnosti                        | Hodnota           | Popis                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Časové razítko                           | 1493763938      | Časové razítko odpovídající položce toku. UNIX EPOCHA formátu se používá.                                                                                       |
| Zdrojová IP adresa                    | 185.170.185.105 |                                                                                                                                                             |
| Cílová IP adresa               | 10.2.0.4        |                                                                                                                                                             |
| Zdrojový port                          | 35370           |                                                                                                                                                             |
| Cílový port                     | 23              |                                                                                                                                                             |
| Protokol přenosů                     | T               | **T**: TCP a **U**: UDP.                                                                                                                                  |
| Směr toku provozu               | I               | **Můžu**: příchozí provoz a **O**: odchozí provoz.                                                                                                         |
| Rozhodnutí o provozu                     | A               | **A**: byl povolen tok a **D**: tok byl odepřen.                                                                                                         |
| Stav toku                           | C               | **B**: Begin, když se vytvoří tok. Nejsou zadány Statistika. **C**: pokračování pro probíhající toku. Statistiky jsou k dispozici v intervalech 5 minut. **Elektronické**: ukončení, když tok je ukončen. Statistiky jsou k dispozici.                                                                                                                                                        |
| Pakety - zdroje do cíle      | 1               | Celkový počet TCP a UDP odeslaných paketů za sekundu ze zdroje do cíle od poslední aktualizace.                                                                  |
| Počet odeslaných bajtů - zdroje do cíle   | 103             | Celkový počet TCP a UDP paketů bajtů odeslaných ze zdroje do cíle od poslední aktualizace. Bajtů na paket zahrnout hlavičky paketů a datové části.         |
| Pakety odeslané – cílový zdroj | 1               | Celkový počet TCP a UDP pakety odeslané z cílového zdroje od poslední aktualizace.                                                                  |
| Počet odeslaných bajtů – cílový zdroj   | 186             | Celkový počet TCP a UDP paketů bajtů odeslaných z cílového zdroje od poslední aktualizace. Bajtů na paket zahrnout hlavičky paketů a datové části.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Jak je tok modelována ve verzi 2?

Verze 2 v protokolech představuje stav toku. Tok stavu *B* zaznamenán, kdy se má aktivovat tok. Tok stavu *C* a stav toku *E* jsou stavy, které označíte pokračování toku a ukončení toku, v uvedeném pořadí. Obě *C* a *E* stavy obsahují informace o přenosech šířky pásma.

**Příklad**: tok řazenými kolekcemi členů z TCP konverzaci mezi 185.170.185.105:35370 a 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>Jak formát se liší od verze 1?

Ve verzi 1, řazené kolekce členů toku ["1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"] vytvoří pokaždé, když se tok navázat, nebo došlo k pokusu o navázat (zabránění případu).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Jak se pakety a bajtů zvláštní pozornost?

Pro pokračování *C* a end *E* stavy toku, počet bajtů a paketu jsou agregace počtů z času předchozí záznam tok řazené kolekce členů. Odkazování na předchozí příklad konverzace, celkový počet paketů přenosu je 1021 + 52 + 8005 + 47 = 9125. Celkový počet bajtů přenesených je 588096 + 29952 + 4610880 + 27072 = 5256000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Pokud aplikace nemá informace o polích provoz šířky pásma, verze 2 vliv aplikace?

Aplikací s použitím zabezpečení sítě verze 1 skupině tok protokolování obvykle počet jedinečných toků. Pokud se neprovedly žádné změny v analýze účet pro stav toku, může se zobrazit nepřesné zvýšení počtu toků, které jsou hlášeny. Počítání jedinečný toky se dá udělat ignoruje tok řazenými kolekcemi členů v *C* a *E* stavy toku.

## <a name="can-i-control-the-version-format-i-receive"></a>Můžete řídit formát verze, které získám?

Ne. Výstupní formát protokoly nebude mít vliv na povolení a zakázání protokolů toku. Změna z verze 1 pro verzi 2 protokoly dojde na základě regiony. Když oblasti se upgraduje z verze 1 na verze 2, může se zobrazit v oba formáty protokolů toku NSG. Po dokončení tohoto uvedení, budou k dispozici pouze verze 2 protokoly.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>Při změně, můžete mi ukázat, oba formáty pro stejnou skupinu zabezpečení sítě?

Ano. V rámci oblasti dojde k přechodu na základě adres mac na. Vzhledem k tomu, že skupina zabezpečení sítě můžete použít pro mnoho počítačů, může se zobrazit protokoly v oba formáty zapsána do úložiště. Protokoly bude buď verze 1 nebo 2.

## <a name="will-i-see-duplicate-data"></a>Se zobrazí duplicitních dat?

Nebudou duplikace toku protokolování dat mezi formáty. Tok se zaznamená ve formátu verze 1 nebo verze 2, když dojde ke změně.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Jak můžu otestovat nový formát předem?

Ano. Je možné [Stáhnout](https://aka.ms/nsgflowlogsv2blobsample) ukázkový soubor protokolu toku verze 2 má použít pro testování řešení.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>Existují změny v konfiguraci a správu protokolování toků skupin zabezpečení sítě?

Ne. Podpora pro účty Azure Storage napříč předplatnými, které sdílejí stejného tenanta služby Azure Active Directory byl [vydané](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) počátkem tohoto roku. Tato změna pomáhá snížit počet účtů úložiště, které jsou potřeba při správě protokoly toků skupin zabezpečení sítě.

## <a name="questions-or-feedback"></a>Dotazy nebo připomínky?

Těšíme se na vaše odpovědi o vašich zkušenostech s protokoly toků skupin zabezpečení sítě a Network Watcher. Online nebo po e-mailové adrese můžete zadat svůj názor nebo navrhnout AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>Ukázková verze 2

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>Ukázková verze 1

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

        } 

    ] 
}
```