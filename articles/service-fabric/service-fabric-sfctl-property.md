---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl vlastnost | Microsoft Docs
description: Popisuje vlastnost příkazy sfctl Service Fabric rozhraní příkazového řádku.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764000"
---
# <a name="sfctl-property"></a>Vlastnost sfctl
Úložiště a dotaz vlastnosti pod názvy Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| odstraňovat | Odstraní zadanou vlastnost Service Fabric. |
| GET | Získá zadanou vlastnost Service Fabric. |
| Seznam | Získá informace o všech vlastnostech Service Fabric pod daným názvem. |
| PUT | Vytvoří nebo aktualizuje vlastnosti Service Fabric. |

## <a name="sfctl-property-delete"></a>odstranění vlastnost sfctl
Odstraní zadanou vlastnost Service Fabric.

Odstraní zadanou vlastnost Service Fabric pod daným názvem. Vlastnost musí být vytvořeny, než mohl být odstraněn.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [vyžaduje] | Název Service Fabric, bez "fabric\:' schéma identifikátoru URI. |
| – Název vlastnosti [vyžaduje] | Určuje název vlastnosti, která má získat. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-property-get"></a>sfctl vlastnost get
Získá zadanou vlastnost Service Fabric.

Získá zadanou vlastnost Service Fabric pod daným názvem. To vždy vrátí hodnotu a metadata.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [vyžaduje] | Název Service Fabric, bez "fabric\:' schéma identifikátoru URI. |
| – Název vlastnosti [vyžaduje] | Určuje název vlastnosti, která má získat. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-property-list"></a>seznam vlastností sfctl
Získá informace o všech vlastnostech Service Fabric pod daným názvem.

Název Service Fabric může mít jeden nebo více s názvem vlastnosti, které obsahují informace o vlastním. Tato operace získá informace o těchto vlastnostech stránkové seznamu. Informace zahrnují název, hodnotu a metadata o každé z vlastností.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [vyžaduje] | Název Service Fabric, bez "fabric\:' schéma identifikátoru URI. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --zahrnují hodnoty | Umožňuje určující, zda mají být zahrnuty hodnoty vlastností vrátila. Hodnota TRUE, pokud má být vrácen hodnoty s metadaty; False vrátit pouze metadata vlastnosti. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-property-put"></a>Vlastnost put sfctl
Vytvoří nebo aktualizuje vlastnosti Service Fabric.

Vytvoří nebo aktualizuje určenou vlastnost Service Fabric pod daným názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id názvu [vyžaduje] | Název Service Fabric, bez "fabric\:' schéma identifikátoru URI. |
| – Název vlastnosti [vyžaduje] | Název vlastnosti Service Fabric. |
| – Hodnota [vyžaduje] | Popisuje hodnotu vlastnosti Service Fabric. Toto je řetězec formátu JSON. <br><br> Řetězce formátu json má dvě pole "Druh" data a 'Hodnota' data. Hodnota "Druh" musí být první položka se objeví v řetězce formátu JSON a může být hodnoty 'Binární', 'Int64', Double, 'Řetězec' nebo Guid. Hodnota by měla moci serializovat-daných typů. Hodnoty "Druh" a "Data, musí zadat jako řetězce. |
| – typ vlastní id | Vlastní vlastnosti zadejte ID. Používání této vlastnosti, je možné označit typ hodnoty vlastnosti uživatele. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).