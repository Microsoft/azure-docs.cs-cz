---
title: Azure Service Fabric CLI sfctl vlastnost | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl vlastnost.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: f24d273afa47466fe53b93d9c9c22bbcb1fc6959
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493515"
---
# <a name="sfctl-property"></a>sfctl property
Vlastnosti v Service Fabric názvy Store a dotazu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou vlastnost Service Fabric. |
| získat | Získá zadanou vlastnost Service Fabric. |
| Seznam | Získá informace o všech vlastnostech Service Fabric s daným názvem. |
| Vložit | Vytvoří nebo aktualizuje vlastnosti Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl vlastnost delete
Odstraní zadanou vlastnost Service Fabric.

Odstraní zadanou vlastnost Service Fabric s daným názvem. Vlastnost musí být vytvořeny, aby se Dal odstranit.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [povinné] | Název Service Fabric, aniž by "fabric\:' schéma identifikátoru URI. |
| --[povinný] název vlastnosti | Určuje název vlastnosti, která má získat. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-property-get"></a>sfctl vlastnost get
Získá zadanou vlastnost Service Fabric.

Získá zadanou vlastnost Service Fabric s daným názvem. Vždy vrátí hodnotu a metadata.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [povinné] | Název Service Fabric, aniž by "fabric\:' schéma identifikátoru URI. |
| --[povinný] název vlastnosti | Určuje název vlastnosti, která má získat. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-property-list"></a>seznam vlastností sfctl
Získá informace o všech vlastnostech Service Fabric s daným názvem.

Service Fabric název může mít jeden nebo více pojmenované vlastnosti, které ukládání uživatelských informací. Tato operace načte informace o těchto vlastnostech ve stránkovaném seznamu. Informace zahrnují název, hodnotu a metadata o všech vlastnostech.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [povinné] | Název Service Fabric, aniž by "fabric\:' schéma identifikátoru URI. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --zahrnují hodnoty | Umožňuje zadat, jestli se má zahrnout hodnoty vlastností vrácena. Hodnota TRUE, pokud má být vrácen hodnoty s metadaty; False, pokud chcete vrátit pouze metadata vlastnosti. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-property-put"></a>sfctl vlastnost put
Vytvoří nebo aktualizuje vlastnosti Service Fabric.

Vytvoří nebo aktualizuje zadanou vlastnost Service Fabric s daným názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [povinné] | Název Service Fabric, aniž by "fabric\:' schéma identifikátoru URI. |
| --[povinný] název vlastnosti | Název vlastnosti Service Fabric. |
| --hodnotu [povinné] | Popisuje vlastnosti Service Fabric. Toto je řetězec formátu JSON. <br><br> Řetězec formátu json má dvě pole, "Druh" data a 'Value' data. Hodnota "Druh" musí být první položky se zobrazí v řetězci JSON a můžou být hodnoty "Binární", "Int64", 'Double', 'Řetězec' nebo 'Guid'. Hodnota by měla možné serializovat-daných typů. Hodnoty "Druh" a "Data" by měl být ve formě řetězce. |
| – vlastní typ id | Id vlastnosti vlastního typu. Pomocí této vlastnosti, uživatel je možnost Označit typ hodnoty vlastnosti. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).